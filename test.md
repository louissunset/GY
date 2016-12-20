#include<stdio.h>
#include<stdlib.h>
#include<math.h>
#define INPUTNUM 3      //输入节点数
#define HIDDENNUM 6     //隐含层节点数
#define OUTPUTNUM 3     //输出节点数
#define SAMPLE 750      //训练样本数
//BP结构体类型
struct
{
    double weight1[HIDDENNUM][INPUTNUM];//输入层到隐含层的权值
	double weight2[OUTPUTNUM][HIDDENNUM];//隐含层到输出层权值
	double threshold1[HIDDENNUM];//隐含层阈值
	double threshold2[OUTPUTNUM];//输出层阈值
}BPNode[SAMPLE];
//全局变量
double maxmin[INPUTNUM][2];
double str[3];
double value[SAMPLE][OUTPUTNUM];
//函数声明
double *LastOutput(int N);//输出层输出
void Getbpnet();//BP网络的获取
void judge();//识别结果
void Getoutput();//获取神经元输出
//主函数
void main()
{
	int i,j;
	printf("请输入待测试样本的三个特征值：速度 温度 心率\n");
	scanf("%lf%lf%lf",&str[0],&str[1],&str[2]);
	printf("识别结果为：\n");
    Getbpnet();
    Getoutput();
    judge();
}
//输出层输出函数
double *LastOutput(int N)
{
	int i,j;
	double *sum,*H,*Q,*sum2;
    H=(double*)malloc(sizeof(double)*HIDDENNUM);
    Q=(double*)malloc(sizeof(double)*OUTPUTNUM);
    sum=(double*)malloc(sizeof(double)*HIDDENNUM);
    sum2=(double*)malloc(sizeof(double)*OUTPUTNUM);
	for(j=0;j<HIDDENNUM;j++)
	{
		sum[j]=0;
		for(i=0;i<INPUTNUM;i++)
		{
			sum[j]=sum[j]+(str[i])*(BPNode[N].weight1[j][i]);
		}
	    sum[j]=sum[j]-(BPNode[N].threshold1[j]);
		H[j]=1/(exp(-sum[j])+1);
	}
	for(i=0;i<OUTPUTNUM;i++)//K 
	{
		sum2[i]=0;
		for(j=0;j<HIDDENNUM;j++)//J
		{
			sum2[i]=sum2[i]+H[j]*(BPNode[N].weight2[i][j]);
		}
		sum2[i]=sum2[i]-(BPNode[N].threshold2[i]);
		Q[i]=1/(exp(-sum2[i])+1);	
	}
	return Q;
}
//BP网络的获取
void Getbpnet()
{
	FILE *fp;
	int i,j,k;
	if((fp=fopen("bpbest.txt","r"))==NULL)
		printf("cannot open this file\n");
	//最大最小值
	for(i=0;i<INPUTNUM;i++)
	{
		for(j=0;j<2;j++)
			fscanf(fp,"%lf",&maxmin[i][j]);
	}
	for(k=0;k<SAMPLE;k++)
	{
		for(i=0;i<HIDDENNUM;i++)//输入到隐含的权值
		{
			for(j=0;j<INPUTNUM ;j++)
			{
				fscanf(fp,"%lf",&BPNode[k].weight1[i][j]);
			}
		}
		for(i=0;i<OUTPUTNUM;i++)//隐含到输出的权值
		{
			for(j=0;j<HIDDENNUM;j++)
			{
				fscanf(fp,"%lf",&BPNode[k].weight2[i][j]);
			}
		}
		for(j=0;j<HIDDENNUM;j++)//隐含层阈值
			fscanf(fp,"%lf",&BPNode[k].threshold1[j]);
        for(j=0;j<OUTPUTNUM;j++)//输出层阈值
			fscanf(fp,"%lf",&BPNode[k].threshold2[j]);
	}
	fclose(fp);
}
//结果识别
void judge()
{
	double *qq,sum,min=10000;
	int i,j,k,t;
	for(i=0;i<SAMPLE;i++)
	{
		qq=(double*)malloc(sizeof(double)*OUTPUTNUM);
	    qq=LastOutput(i);//实际输出
		sum=0;
        for(j=0;j<OUTPUTNUM;j++)
		{
			sum=sum+(qq[j]-value[i][j])*(qq[j]-value[i][j]);
		}
		if(sum<min)
		{
			k=i;
			min=sum;
		}
	}
	t=SAMPLE/OUTPUTNUM;
	if(k<t)
	{
		printf("用户行为：\tWalking\n");    
	}
	else if(k<2*t)
	{
		printf("用户行为：\tJogging\n");    
	}
	else 
	{
		printf("用户行为：\tIdle\n");      
	}
}
//获取神经元输出
void Getoutput()
{
	FILE *fp;
	int i,j;
    if((fp=fopen("bpout.txt","r"))==NULL)
		printf("cannot open this file\n");
	for(i=0;i<SAMPLE;i++)
	{
		fscanf(fp,"%lf%lf%lf",&value[i][0],&value[i][1],&value[i][2]);
	}
    fclose(fp);
}