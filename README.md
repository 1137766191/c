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
