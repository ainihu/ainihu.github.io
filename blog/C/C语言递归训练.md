欢迎加入QQ：498903810 一起交流、讨论知识，里面有大佬，也有小白，天下码农一家亲，大家一起讨论进步。

<font size = 4>题目：某游戏公司刚创立时只有一名员工，每名员工有三个月的使用期，过试用期后转为正式员工，每名正式员工每个月都会推荐一名新员工进入公司，新员工经过三个月使用期后转为正式员工每个月又会推荐一名新员工进入公司，加入公司创立时的第一名员工也需要使用期，并且所有员工都不会离职，据此，请书写main方法打印出来公司刚成立前a个月总员工数量(纯手打 哦~)。</font>

废话不多说直接先上代码：

```
#define _CRT_SECURE_NO_WARNINGS
#include<stdio.h>

int func(int a)
{
	if (a <= 3)
	{
		return 1;
	}
	else
	{
		return func(a - 1) + func(a - 3);
	}
}

int main()
{
	int month;
	printf("请输入月数：");
	scanf("%d", &month);

	printf("公司的总人数是： %d\n", func(month));

	system("pause");
	return 0;
}
```
<font size = 5 color = "red">代码最开始的#define _CRT_SECURE_NO_WARNINGS是vs编译器的一个宏定义，其他的编译器可以去掉之后就可以执行了。</font>


接下来说一下我的思路，先给一张手打表格帮助理解(用电脑网页才能看见这个表格，手机的话会可能会出现乱码)

|月数|  1  |  2  |  3  |  4  |  5  |  6  |  7  |  8  |  9  |  10 |  11 |  12 |  13 |  14 |  15 |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| 正式 |	    |	  |	    |  1  |  1  |  1  |  2  |  3  |	 4  |  6  |  9  | 13  |  19 |  28 | 41  |
|试用1 |	 1  |     |	    |  1  |     |     |	 2  |	  |	    |  6  |     |	  |  19 |	  |     |
|试用2 |	    |     |	    |	  |  1  |     |	    |  3  |	    |	  |  9  |	  |     |  28 |     |
|试用3 |	    |     |	    |	  |     |  1  |	    |	  |	 4  |     |	    |  13 |	    |     | 41  |
|总人数|	 1  |  1  |  1  |  2  |  3  |  4  |  6  |  9  |  13 |  19 |  28 |  41 |  60 |  88 | 125 |


这个效果很清楚了吧！！！，不枉费我手打的，可能我有点zz了
<font color = "red" size = 5>PS：没有写的地方的数据就是‘0’了。</font>

<font size = 4>有没有发现什么规律吗？？？</font>
前三个月的人数不看，从第四个月开始看，

四月份的总人数 = 三月份的<font color = "red">正式/试用</font>员工 + 一月份的正式员工
五月份的总人数 = 四月份的<font color = "red">正式/试用</font>员工 + 二月份的正式员工
六月份的总人数 = 五月份的<font color = "red">正式/试用</font>员工 + 三月份的正式员工
七月份的总人数 = 六月份的<font color = "red">正式/试用</font>员工 + 四月份的正式员工
八月份的总人数 = 七月份的<font color = "red">正式/试用</font>员工 + 五月份的正式员工
九月份的总人数 = 八月份的<font color = "red">正式/试用</font>员工 + 六月份的正式员工
<font size = 6>... ...</font>
n月份的总人数 = （n - 1）月的<font color = "red">正式/试用</font>员工 + （n - 3）月份的正式员工
<font size = 6>... ...</font>
代码翻译过来的核心就是：return func(a - 1) + func(a - 3);
<font color = "red" size = 4>注意红色部分！！！
注意红色部分！！！
注意红色部分！！！
为什么要写成 正式/试用 呢？？？
因为正式员工当月可以介绍新的员工进来，有多少个正式员工，当月就有多少个试用员工，所以直接就可以这样写。
</font>
再想一想，递归的本质就是<font color = "red" size = 4>大事化小、小事化无</font>的思想,所以不妨这样想

假设让你算六个月的的总人数，那么是不是可以先要算五个月的总人数，但是，每个人有三个月的试用期，试用期间不能介绍新人的。所以每个人进入公司后有三个月的<font color = "red">"蛰伏期"</font>,所以重点理解这三个月的<font color = "red">"蛰伏期"</font>就能很快的理解这道题了。再返回头看一看我的表格和递推公式是不是就明白了呢？？？

<font color = "red">这里来说一下前三个月为什么要单独写出来,因为前三个月的人数不能去加上一个负的月份数，所以要单独写出来这样才能</font>
<font size = 5>over,希望大家听明白了，不懂的话一起讨论</font>
