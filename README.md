# 扫雷

## 1. 逻辑导图





<img src="C:\Users\wangzhen\AppData\Roaming\Typora\typora-user-images\image-20220311124239242.png" alt="image-20220311124239242" style="zoom:33%;" />

## 2. 代码实现

### 2.1 游戏主体

~~~C
int main()
{
	srand((unsigned int)time(NULL));
	int input = 0;
	do
	{
		menu();
		printf("请选择:>");
		scanf("%d", &input);
		switch (input)
		{
		case 1:



			game();
			break;
		case 0:
			printf("退出\n");
			break;
		default:
			printf("选择错误\n");
			break;
		}

	} while (input);
	return 0;

}
~~~

### 2.2 游戏部分

首先创建两个二维数组，mine,show;其中mine是实际布置雷使用，show是为游戏用户展示的数组；两个数组大小相等，值得注意的是创建的数组要比展示的布局多出两行两列，例如用户操作的是9X9的画布大小，那么我们需要创建11X11的数组-mine,show ；原因是只有这样才能计算处于9X9边界位置的雷数。

~~~c
void game()
{
	char mine[ROWS][COLS] = { 0 };
	char show[ROWS][COLS] = { 0 };
	//初始化棋盘
	InitBoard(mine, ROWS, COLS,'0');
	InitBoard(show, ROWS, COLS,'*');
	//打印棋盘
	DisplayBoard(show, ROW, COL);
	SetMine(mine, ROW, COL);
	//DisplayBoard(mine, ROW, COL);
	//排查雷
	FindMine(mine, show, ROW, COL);
}
~~~



#### 2.2.1 初始化棋盘

~~~C
InitBoard(mine, ROWS, COLS,'0');
InitBoard(show, ROWS, COLS,'*');
~~~

值得注意的是，初始化的值是通过传参给set来决定。

~~~C
void InitBoard(char board[ROWS][COLS], int rows, int cols, char set)//遍历
{
	int i = 0;
	int j = 0;
	for (i = 0; i < rows; i++)
	{
		for (j = 0; j < cols; j++)
		{
			board[i][j] = set;
		}
	}

}
~~~

2.2.2 展示棋盘

值得注意的是，展示的数组大小为9X9。

~~~C
void DisplayBoard(char board[ROWS][COLS], int row, int col)
{
	int i = 0;
	int j = 0;
	printf("*********扫雷游戏*********\n");
	for (i = 0; i <= col;i++)
	{
		printf("%d ", i);
	}
	printf("\n");

	for (i = 1; i <= row; i++)
	{
		printf("%d ", i);
		for (j = 1; j <= col; j++)
		{
			printf("%c ", board[i][j]);
		}
		printf("\n");
	}
	printf("*********扫雷游戏*********\n");
}
~~~

2.2.3 布雷

注意：布雷坐标由随机函数rand()生成，其中rand()%row+1目的是生成1-9之间的数字。e_count为define定义的标识符常量。

~~~c
void SetMine(char board[ROWS][COLS], int row, int col)
{
//布置十个雷
	int count = e_count;
	while (count)
	{
		int x = rand() % row + 1;
		int y = rand() % col + 1;
		if (board[x][y] == '0')//字符一定要用‘’
		{
			board[x][y] = '1';
			count--;
		}
	}
}
~~~

2.2.3 排雷

首先判断坐标的合法性，如果合法，则利用函数get_mine_count计算所输坐标周围存在的雷数并显示；其次为判断输赢，

当变量win<row*col - e_count循环执行扫雷，当相等时则游戏结束，玩家胜利。

注意：数字大小=字符-'0'；原因二维数组里存放的是字符因此，当你想显示数字时需要一个转换，这个转换的规律是字符数字=数字+'0'。

~~~c
 static int get_mine_count(char mine[ROWS][COLS], int x, int y)
{
	return
		mine[x - 1][y - 1] +
		mine[x - 1][y] +
		mine[x - 1][y + 1] +
		mine[x][y - 1] +
		mine[x][y + 1] +
		mine[x + 1][y - 1] +
		mine[x + 1][y] +
		mine[x + 1][y + 1] - 8 * '0';

}

void FindMine(char mine[ROWS][COLS], char show[ROWS][COLS], int row, int col)
{
	int x = 0;
	int y = 0;
	int win = 0;
	
	while (win < row*col - e_count)
	{
		printf("请输入坐标:>");
		scanf("%d%d", &x, &y);
		if (x >= 1 && x <= row&&y >= 1 && y <= col)
		{
			if (mine[x][y] == '1')
			{
				printf("很遗憾，你被炸死了\n");
				DisplayBoard(mine, row, col);
				break;
			}
			else
			{
				int count = get_mine_count(mine, x, y);
				show[x][y] = count + '0';//数字count+'0'表示count代表的数字的ASCII
				DisplayBoard(show, row, col);
				win++;
			}
		
		}
		else
		{
			printf("坐标不合法，请重新输\n");
		}
	}
	if (win == row*col - e_count)
	{
		printf("您赢了！\n");
	}
}
~~~

## 3. 运行效果

![image-20220311135326384](C:\Users\wangzhen\AppData\Roaming\Typora\typora-user-images\image-20220311135326384.png)

![image-20220311135523618](C:\Users\wangzhen\AppData\Roaming\Typora\typora-user-images\image-20220311135523618.png)