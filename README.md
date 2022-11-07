# c
日常学习

int main()
{
	int age;
	scanf("%d", &age);
	if (age < 18)
		printf("禁止访问");
	else if (age >= 18 && age < 28)
		printf("允许访问");
	else if (age >= 28 && age < 50)
		printf("精力充沛");
	else if (age >= 50 && age < 80)
		printf("老当益壮");
	else
		printf("老不死");
	return 0;
}


int main()
{
	int a = 0;
	int b = 2;
	if (a == 1)  //因为a！==1，所以之后程序无法进行
	{
		if (b == 2)
			printf("hehe\n");
	}            //要与上if匹配要加大括号
		else     //else与最近的未匹配的if匹配
			printf("haha\n");
	return 0;
}



/****************数据结构实验*********************/
/***蟀.h****/
#include<stdlib.h>
#include<stdio.h>
#include<string.h>
typedef struct
{    //结点权重
	int weight, parent, lchild, rchild;;//父结点、左孩子、右孩子在数组中的位置下标
}HTNode, *HuffmanTree;
typedef char**HuffmanCode;//动态二维数组，存储哈夫曼编码
//HT数组中存放的哈夫曼树，end表示HT数组中存放结点的最终位置，s1和s2传递的是HT数组中权重值最小的两个结点在数组中的位置
void Select(HuffmanTree HT, int end, int *s1, int *s2)
{
	int min1, min2;
	int i = 1;//遍历数组初始下标为 1
	while (HT[i].parent != 0 && i <= end)  //找到不是头和尾的节点
		//找到还没构建树的结点
	{
		i++;
	}

	min1 = HT[i].weight;
	*s1 = i;
	i++;
	while (HT[i].parent != 0 && i <= end)
	{
		i++;
	}
	//对找到的两个结点比较大小，min2为大的，min1为小的
	if (HT[i].weight < min1)    //min1>HT[i].weight的话
	{
		min2 = min1;      //让min2=min1,s2=s1
		*s2 = *s1;        //让min2代替mim1
		min1 = HT[i].weight; //把min1赋值HT[i].weight，s1=i
		*s1 = i;          //让min1代替HT[i].weight
	}  //总结：如果HT[i].weight < min1，则将min2与min1的值替换,将min1与HT[i].weight的值替换，使得min2>min1
	else //HT[i].weight > min1
	{
		min2 = HT[i].weight;
		*s2 = i;
	} //若HT[i].weight > min1，则直接将HT[i].weight的赋给min2


	for (int j = i + 1; j <= end; j++)   //两个结点和后续的所有未构建成树的结点做比较
	{
		if (HT[j].parent != 0)  //如果有父结点，直接跳过，进行下一个
		{
			continue;
		}

		//如果比最小的还小，将min2=min1，min1赋值新的结点的下标
		if (HT[j].weight < min1)
		{
			min2 = min1;   //让min2=min1,s2=s1
			min1 = HT[j].weight; //让min1代替HT[i].weight
			*s2 = *s1;
			*s1 = j;
		}
		else if (HT[j].weight > min1 && HT[j].weight < min2)
			//如果HT[j].weight大于min1且HT[j].weight小于min2
			//可将HT[j].weight的值赋给min2，j赋给s2
		{
			min2 = HT[j].weight;
			*s2 = j;
		}
	}
}


//HT为地址传递的存储哈夫曼树的数组，w为存储结点权重值的数组，n为结点个数
void CreateHuffmanTree(HuffmanTree *HT, int *w, int n)
{
	if (n <= 1) return; // 如果只有一个编码就相当于0
	int m = 2 * n - 1; // 哈夫曼树总节点数，n就是叶子结点
	*HT = (HuffmanTree)malloc((m + 1) * sizeof(HTNode)); // 0号位置不用
	HuffmanTree p = *HT;
	// 初始化哈夫曼树中的所有结点
	for (int i = 1; i <= n; i++)
	{
		(p + i)->weight = *(w + i - 1);
		(p + i)->parent = 0;
		(p + i)->lchild = 0;
		(p + i)->rchild = 0;
	}
	//从树组的下标 n+1 开始初始化哈夫曼树中除叶子结点外的结点
	for (int i = n + 1; i <= m; i++)
	{
		(p + i)->weight = 0;
		(p + i)->parent = 0;
		(p + i)->lchild = 0;
		(p + i)->rchild = 0;
	}
	//构建哈夫曼树
	for (int i = n + 1; i <= m; i++)
	{
		int s1, s2;
		Select(*HT, i - 1, &s1, &s2);
		(*HT)[s1].parent = (*HT)[s2].parent = i;
		(*HT)[i].lchild = s1;
		(*HT)[i].rchild = s2;
		(*HT)[i].weight = (*HT)[s1].weight + (*HT)[s2].weight;
	}
}

//HT为哈夫曼树，HC为存储结点哈夫曼编码的二维动态数组，n为结点的个数
void HuffmanCoding(HuffmanTree HT, HuffmanCode *HC, int n) {
	*HC = (HuffmanCode)malloc((n + 1) * sizeof(char *));
	char *cd = (char *)malloc(n * sizeof(char)); //存放结点哈夫曼编码的字符串数组
	cd[n - 1] = '\0';//字符串结束符

	for (int i = 1; i <= n; i++) {
		//从叶子结点出发，得到的哈夫曼编码是逆序的，需要在字符串数组中逆序存放
		int start = n - 1;
		//当前结点在数组中的位置
		int c = i;
		//当前结点的父结点在数组中的位置
		int j = HT[i].parent;
		// 一直寻找到根结点
		while (j != 0) {
			// 如果该结点是父结点的左孩子则对应路径编码为0，否则为右孩子编码为1
			if (HT[j].lchild == c)
				cd[--start] = '0';
			else
				cd[--start] = '1';
			//以父结点为孩子结点，继续朝树根的方向遍历
			c = j;
			j = HT[j].parent;
		}
		//跳出循环后，cd数组中从下标 start 开始，存放的就是该结点的哈夫曼编码
		(*HC)[i] = (char *)malloc((n - start) * sizeof(char));
		strcpy_s((*HC)[i], 4, &cd[start]);

	}
	//使用malloc申请的cd动态数组需要手动释放
	free(cd);
}
//打印哈夫曼编码的函数
void PrintHuffmanCode(HuffmanCode htable, int *w, int n)
{
	printf("Huffman code : \n");
	for (int i = 1; i <= n; i++)
		printf("%d 的字符编码是 = %s\n", w[i - 1], htable[i]);
}

#include"蟀.h"

int main()
{
	int n;
	int w[30];
	HuffmanTree htree;
	HuffmanCode htable;
	printf("请输入参加排序的元素个数:");
	scanf_s("%d", &n);
	printf("\n请输入元素:");
	for (int i = 0; i < n; i++)
	{
		scanf_s("%d", &w[i]);
	}
	CreateHuffmanTree(&htree, w, n);
	HuffmanCoding(htree, &htable, n);
	PrintHuffmanCode(htable, w, n);
	
	return 0;
}

//练习：判断一个数是否为奇数，取1到100之间的奇数
int main()
{
	for(int i=1;i<100;i++)
	if (i % 2 == 1)
		printf("%d ", i);
	return 0;
}
//swith   case    break用法
int main()
{
	int day = 0;
	scanf("%d", &day);
	switch (day)
	{
	case 1:
		printf("月曜日\n");
		break;
	case 2:
		printf("火曜日\n");
		break;
	case 3:
		printf("水曜日\n");
		break;
	case 4:
		printf("木曜日\n");
		break;
	case 5:
		printf("金曜日\n");
		break;
	case 6:
		printf("土曜日\n");
		break;
	case 7:
		printf("日曜日\n");
		break;
	default:
		printf("输入错误\n");
	return 0;
	}
}

int main()
{
	int n = 1;
	int m = 2;
	switch (n) //因为无break，所以从1往后都执行，直到遇到break
	{
	case 1: m++; //m=3
	case 2: n++; //n=2
	case 3: 
		switch (n) //n=2
		{
		case 1:n++;  //不执行
		case 2:m++, n++; break; //n=3 m=4
		default:
			break;  //弹出case 3里的switch，继续执行case 4
		}
	case 4:m++; //m=5
	default:
		break;
	}
	printf("m=%d,n=%d\n", m, n);
	return 0;
}

int main()
{
	int i = 0;
	while (i < 10)
	{
		i++;
		if (i == 5)
			continue;//后面的代码不再执行，跳转回while
		printf("%d ", i);
		
		//break; //跳出
		//continue; //跳回
	}
	return 0;
}

int main()
{
	int a = 1, b = 2, c = 3;
	if (a > c)//当if后没有括号代表if只影响下一个语句
		b = a;  //因为if不通过跳过此句
		a = c;
		c = b;
	return 0;
}

int main()
{
	int i = 0;
	while (i < 10)
	{
		i++;
		if (i == 5)
			continue;//后面的代码不再执行，跳转回while
		printf("%d ", i);
		
		//break; //跳出
		//continue; //跳回
	}
	return 0;
}

int main()
{
	int a, b;
	scanf("%d", &a);
	switch (a)
	{
	case 1:
		printf("我回来了");
		break;
	case 2:
		printf("我还在生气了");
		break;
	case 3:
		printf("想听听我想说什么？\n");
		scanf("%d", &b);
		switch (b)
		{
		case 1:
			printf("真话");
			break;
		case 2:
			printf("假话");
			break;
		default:
			printf("不听不听");
			break;
		}
	default:
		break;
	}
	return 0;
}

int main()
{
	//int ch = 0; //EOF-文件结束标志
	//while ((ch = getchar()) != EOF)
	//{
	//	putchar(ch);
	//}
	int ret = 0;
	char arr[20];
	printf("请输入密码：");
	scanf("%s", arr);
	printf("请确认密码（Y/N）:");
	getchar();//读取缓冲区里剩余的\n
	ret = getchar();//若无i= getchar()，则会直接读取\n使得ret=‘\n’==10
	if (ret == 'Y')
	{
		printf("确认成功\n");
	}
	else
	{
		printf("放弃确认\n");
	}
	return 0;
}

int main()
{
	int ch;
	int ret = 0;
	char arr[20];
	printf("请输入密码：");
	scanf("%s", arr); //若输入的密码带空格等非法字符如：123456 asd
	printf("请确认密码（Y/N）:");
	while ((ch = getchar()) != '\n');//可以使用while循环
	{           //将getchar重复运行直到等于'\n'
		;       //停止循环
	}           //此时缓冲区里的东西已被全部取出
	ret = getchar();
	if (ret == 'Y')
	{
		printf("确认成功\n");
		printf("你的密码是：%s", arr);
	}
	else
	{
		printf("放弃确认\n");
	}
	return 0;
}

int main()
{
	int ch = 0;
	while((ch=getchar())!=EOF)
	{
	if (ch < '0' || ch > '9')
	
		continue;
		putchar(ch);
	}
	return 0;
}

int main()
{
	int n;
	int i;
	int k = 1;
	scanf("%d", &n);
	for (i = 1; i <= n; i++) //计算n的阶乘
	{
		k = k * i;
	}
	printf("%d", k);
	return 0;
}

int main()
{
	int i = 1;
	int sum = 0;
	int k = 1;	
		for (i = 1; i <= 10;i++)  //计算1！+2！+......+10！
		{
			k = k * i;
			sum = sum + k;
		}
	printf("%d", sum);
	return 0;
}

int main()
{
	int i;
	int sz;
	int left = 0;
	char arr[] = {1,2,3,4,5,6,7,8,9,10};
	sz = sizeof(arr) / sizeof(arr[0]);
	int right = sz - 1;
	scanf("%d", &i);
	while (left <= right)
	{
		int mid = (left + right) / 2;
		if (arr[mid] < i)
		{
			left = mid + 1;
		}
		else if (arr[mid] > i)
		{
			right = mid - 1;
		}
		else if(arr[mid] == i)
		{
			printf("找到了，下标是：%d\n",mid);
			break;
		}
	}
	if (left > right)
	{
		printf("找不到\n");
	}
	return 0;
}

int main()
{
	char arr1[] = "welcome to my word";
	char arr2[] = "******************";
	int left = 0;
	int right;
	right = sizeof(arr1) / sizeof(arr1[0])-2;
	
	while (left <= right)
	{
		arr2[left] = arr1[left];
		arr2[right] = arr1[right];
		printf("%s\n", arr2);
		Sleep(500);
		system("cls");
		left++;
		right--;		
	}
	printf("%s", arr2);
	return 0;
}

int main()
{
	int i;
	char arr[20];
	for (i = 0; i < 3; i++)
	{
		printf("请输入密码：");
		scanf("%s", arr);
		if (strcmp(arr,"123456")==0) // ==不能用来比较两个字符串是否相等，应该使用一个库函数strcmp
		{
			printf("登陆成功！\n");
			break;
		}
		else
		{
			printf("密码错误\n");
		}
	}
	if (i == 3)
	{
		printf("三次密码均错误，清退出程序");
	}
	return 0;
}

/*练习（自做）*/
//1.计算n的阶乘
//2.计算1！+2！+...+n！
int main()
{
	int n; int i; int k=0;
	int sum=1;
	printf("请输入要计算谁的阶乘：");
	scanf("%d", &n);
	for (i = 1; i <= n; i++)
	{		
		sum = sum * i;	
		k = k+sum;
	}
	printf("n的阶乘是：%d\n", sum);
	printf("1！+2！+...+n！的值为：%d", k);
}

int main() //输入a,b,c,让a,b,c从大到小输入
{
	int a = 0; int b = 0; int c = 0;
	scanf("%d%d%d", &a, &b, &c);
	if (a<b)
	{
		int n = a;
		a = b;
		b = n;
	}
	if (a < c)
	{
		int n = a;
		a = c;
		c = n;
	}
	if (b < c)
	{
		int n = b;
		b = c;
		c = n;
	}
	printf("%d %d %d\n", a, b, c);
	return 0;
}

int main()
{
	int n;
	for (n = 1; n <= 100; n++)
	{
		if (n % 3 == 0)
			printf("%d\n", n);
	}
	return 0;
}

int main() //求两个数的最大公约数
{
	int a, b;
	scanf("%d%d", &a, &b);
	if (a < b)
	{
		int n = a;
		a = b;
		b = n;
	}
	while (a%b)
	{
		int r = a % b;
		a = b;
		b = r;
	}
	printf("%d\n", b);
	return 0;
}

int main()
{
	int i;
	int y=0;
	for (i = 1000; i <= 2000; i++)
		//能被4整除并且不能被100整除是闰年
		//能被400整除是闰年
		
	{
		if (i % 4 == 0&&i % 100 != 0|| i % 400 == 0)
		{
			printf("%d\n", i);
			y++;
		}	
	}
	printf("闰年有%d天", y);
	return 0;
}

int main()//计算1到100之间带有多少个9
{
	int i;
	int j=0;
	for (i = 1; i <= 100; i++)
	{
		if (i % 10 == 9)
			j++;
		if (i / 10 == 9)
			j++;
	}
	printf("%d", j);
	return 0;
}

int main()  //计算1/1+1/2+1/3+1/4+...+1/100
{
	int b;
	double sum = 0;
	int nb = 1;
	for (b = 1; b <= 100; b++)
	{
		sum += nb * 1.0 / b;
		nb = -nb;
	}
	printf("%lf\n", sum);
	return 0;
}

int main()   //求10个数的最大值
{ 
	int arr[] = { -1,-2,-3,-4,-5,-8,-9,-51,-55 };
	int max=arr[0]; int i;
	int sz = sizeof(arr) / sizeof(arr[0]);
	for (i = 1; i <= sz; i++)
	{
		if (max < arr[i])
		{
			max = arr[i];
		}
	}
	printf("max=%d", max);
	return 0;
}

int main()
{
	int a, b, c;
	for (a = 1; a <= 9; a++)
	{
		printf("\n");
		for (b = 1; b <= 9; b++)
		{			
			c = a * b;
			printf("%d*%d=%-2d ", a, b, c);//%-2d将整数输入两位并向左对齐
			if (b == 9 - a + 1)
			{
				break;
			}
		}
	}
	return 0;
}
