#include<stdio.h>
#include<stdlib.h>
#include<math.h>
#define INPUTNUM 3      //����ڵ���
#define HIDDENNUM 6     //������ڵ���
#define OUTPUTNUM 3     //����ڵ���
#define SAMPLE 750      //ѵ��������
//BP�ṹ������
struct
{
    double weight1[HIDDENNUM][INPUTNUM];//����㵽�������Ȩֵ
	double weight2[OUTPUTNUM][HIDDENNUM];//�����㵽�����Ȩֵ
	double threshold1[HIDDENNUM];//��������ֵ
	double threshold2[OUTPUTNUM];//�������ֵ
}BPNode[SAMPLE];
//ȫ�ֱ���
double maxmin[INPUTNUM][2];
double str[3];
double value[SAMPLE][OUTPUTNUM];
//��������
double *LastOutput(int N);//��������
void Getbpnet();//BP����Ļ�ȡ
void judge();//ʶ����
void Getoutput();//��ȡ��Ԫ���
//������
void main()
{
	int i,j;
	printf("�������������������������ֵ���ٶ� �¶� ����\n");
	scanf("%lf%lf%lf",&str[0],&str[1],&str[2]);
	printf("ʶ����Ϊ��\n");
    Getbpnet();
    Getoutput();
    judge();
}
//������������
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
//BP����Ļ�ȡ
void Getbpnet()
{
	FILE *fp;
	int i,j,k;
	if((fp=fopen("bpbest.txt","r"))==NULL)
		printf("cannot open this file\n");
	//�����Сֵ
	for(i=0;i<INPUTNUM;i++)
	{
		for(j=0;j<2;j++)
			fscanf(fp,"%lf",&maxmin[i][j]);
	}
	for(k=0;k<SAMPLE;k++)
	{
		for(i=0;i<HIDDENNUM;i++)//���뵽������Ȩֵ
		{
			for(j=0;j<INPUTNUM ;j++)
			{
				fscanf(fp,"%lf",&BPNode[k].weight1[i][j]);
			}
		}
		for(i=0;i<OUTPUTNUM;i++)//�����������Ȩֵ
		{
			for(j=0;j<HIDDENNUM;j++)
			{
				fscanf(fp,"%lf",&BPNode[k].weight2[i][j]);
			}
		}
		for(j=0;j<HIDDENNUM;j++)//��������ֵ
			fscanf(fp,"%lf",&BPNode[k].threshold1[j]);
        for(j=0;j<OUTPUTNUM;j++)//�������ֵ
			fscanf(fp,"%lf",&BPNode[k].threshold2[j]);
	}
	fclose(fp);
}
//���ʶ��
void judge()
{
	double *qq,sum,min=10000;
	int i,j,k,t;
	for(i=0;i<SAMPLE;i++)
	{
		qq=(double*)malloc(sizeof(double)*OUTPUTNUM);
	    qq=LastOutput(i);//ʵ�����
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
		printf("�û���Ϊ��\tWalking\n");    
	}
	else if(k<2*t)
	{
		printf("�û���Ϊ��\tJogging\n");    
	}
	else 
	{
		printf("�û���Ϊ��\tIdle\n");      
	}
}
//��ȡ��Ԫ���
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