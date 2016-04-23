---
title: 丢棋子问题
date: 2016-04-22 15:20:20
tags: [算法 动态规划]
---
动态规划问题

##算法一：

###关键步骤讲解：

给定楼层数和棋子的个数，棋子在第i层会碎掉，如果想找到i值，求出在最差的情况下扔的最少的次数，**dp[0][j]=0,dp[i][1]=N,dp[i][j]=min{max{dp[k-1][j-1],dp[i-k][j] (1<=k<=i)}}+1**;时间复杂度：O(k*N^2)；其中dp[i][j]代表楼层数为i层，棋子数为j个时，第一个测试的楼层是k层，此时最坏情况下需要测试的最少次数；其中取max{dp[k-1][j-1],dp[i-k][j](1<=k<=i)}这个最大值说明要取棋子在第k层测试之后的后续两种情况中（碎了/没碎）最坏的情况，min说明在最坏的情况下测试的次数要最少，+1说明在第k层的这次测试也算一次；

###代码：

```C
#include<iostream>
using namespace std;
#define min(x,y) (x<=y ? x:y)
#define max(x,y) (x>=y ? x:y)
int solution(int nLevel, int kChess){
	if (nLevel < 1 || kChess < 1)
		return 0;
	if (kChess == 1)
		return nLevel;
	//申请内存dp与s
	int **dp = new int *[nLevel + 1];
	int **s = new int *[nLevel + 1];
	for (int i = 0; i < nLevel + 1; i++){
		*(dp + i) = new int[kChess + 1];
		*(s + i) = new int[kChess + 1];
	}
	//初始化矩阵dp与s,s[i][j]代表在i层楼的情况下，有j个棋子，第一次测试的楼层数；
	dp[0][0]=0;
	s[0][0] = 0;
	for (int i = 1; i < nLevel + 1; i++)
	{
		dp[i][1] = i;
		dp[i][0] = 0;
		s[i][1] = 1;
		s[i][0] = 0;
		//dp[0][i] = 0;//可能超范围；因为kChess值的大小
	}
	for (int i = 1; i < kChess + 1; i++)
	{
		dp[0][i] = 0;
		s[0][i] = 0;
	}
	//算法的核心
	for (int i = 1; i < nLevel + 1; i++){
		for (int j = 2; j < kChess + 1; j++){
			int minValue = INT_MAX;
			int tempMin = INT_MAX;
			for (int k = 1; k <= i; k++){
				//dp[0][j]=0,dp[i][1]=N,dp[i][j]=min{max{dp[k-1][j-1],dp[i-k][j](1<=k<=i)}+1;
				//时间复杂度：O(k*N^2)
				tempMin = max(dp[k - 1][j - 1], dp[i - k][j]);
				if (tempMin < minValue)
				{
					s[i][j] = k;
					//记录出第一个判断的楼层数，如果棋子碎了，取dp[k - 1][j - 1]对应的s[k - 1][j - 1]值；
					//如果棋子没碎，取dp[i - k][j]对应的s[i - k][j]值+i(关键，因为在i层的基础之上进行测试)，
					//才是第二次判断的楼层数
					minValue = tempMin;
				}
			}
			dp[i][j] = minValue + 1;
		}
	}
	//输出s,dp矩阵：
	for (int i = 0; i < nLevel + 1; i++){
		cout << endl;
		for (int j = 0; j < kChess + 1; j++){
			cout << dp[i][j] << " ";
			//cout << s[i][j] << " ";
		}
	}
	//输出值
	int result = dp[nLevel][kChess];
	//释放内存
	for (int i = 0; i < nLevel + 1; i++)
	{
		delete[] dp[i];
	}
	delete[] dp;
	return result;
}
int main(){
	int level, chess;
	while (cin >> level >> chess)
	{
		cout << endl<< "less times:" << solution(level, chess) << endl;
	}
	getchar(); getchar();
	return 0;
}
```
`注：在接下来的算法中忽略内存的开辟与释放的细节`