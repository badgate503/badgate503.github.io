---
title: Numerical Analysis 习题
date: 2023-10-07 19:40:17
tags: [数学,课程,编程,习题]
index_img: /img/naimg.jpg
categories: 习题
---
本贴为浙江大学 Numerical Analysis Lab 题解集合，请勿直接复制使用。
## 6-1 Numerical Summation of a Series
```c
double calcInt(double i){
    int j;  //x为整数
    double res =0;
    for(j = 1;j<=i;j++)
    {
        res+=1.0/j;
    }
    return res/i;
}
void Series_Sum( double sum[] )
{
    double x;
    double remain = 0;
    double id,s1,s2,s3;
    double k,res=0;
    double cx = 0;
    int c = 0,i;
    //for(k=6000;k<=1e7;k++){
    //    remain+=(1.0/(k*k*(k+1)));
    //}
    for(c=0;c<=3000;c++){
        if(c%10==0){
            i = c/10+1;
            id = (double) i;
            s1=calcInt(i);
            s2=calcInt(i+1);
            s3=calcInt(i+2);
            if(c!=0){
                sum[c]=calcInt(c/10);
                cx+=0.1;
                continue;
            }
            //printf("%lf %lf %lf %lf\n",id,s1,s2,s3);
        }
        res=0;
        for(k=2000;k>0;k--){
            res+=1.0/(k*(k+cx)*(k+id)*(k+id+1)*(k+id+2));
        }
        sum[c]=(0-s2+s1+(id-cx+1)*((id-cx+2)*res+0.5*s1-s2+0.5*s3))*(id-cx)+s1;
        cx+=0.1;
    }
}
```
## 6-2 Root of a Polynomial

```c
double getVal(double x,int n, double c[])
{
    int i;
    double res = 0;
    for(i=n;i>=0;i--)
    {
        res=res*x+c[i];
    }
    return res;
}

double getDVal(double x,int n, double c[])
{
    int i;
    double res = 0;
    for(i=n;i>=1;i--)
    {
        res=res*x+c[i]*i;
    }
    return res;
}
double getDDVal(double x,int n, double c[])
{
    int i;
    double res = 0;
    for(i=n;i>=2;i--)
    {
        res=res*x+c[i]*i*(i-1);
    }
    return res;
}
double Polynomial_Root(int n, double c[], double a, double b, double EPS)
{
    double l = a<b?a:b;
    double r = a>b?a:b;
    double x0,x1;
    
    double val,der1,der2,xp;
    int i;
    
    for(xp=l;xp<=r;xp+=(r-l)*0.5)
    {
        x0=xp;
        for(i = 0;i<1000;i++)
        {
            
            double fm = (getDVal(x0,n,c)*getDVal(x0,n,c)-getDDVal(x0,n,c)*getVal(x0,n,c));
            //if(fabs(fm)<ZERO)break;
            if(fabs(getVal(x0,n,c))<=ZERO)return x0;
            x1 = x0 - getVal(x0,n,c)*getDVal(x0,n,c)/fm;
            //x1 = x0-getVal(x0,n,c)/getDVal(x0,n,c);
            if(x1<l||x1>r)break;
            
            if(fabs(x1-x0)<EPS){return x1;}
            x0 = x1;
        }
    }
}
```