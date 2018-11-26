# bcjmaze
第一行三个整数 N A B，表示迷宫的大小，也就是一共N行。两个整数A, B，分别表示入口和出口的格子编号。  解释：格子编号就是从上往下横着一行一行的数，分别是1234……  第二行开始不知道有多少行，一行两个整数Pi和Qi。表示第i次砸墙的参数，Pi表示砸的是哪个格子周围的墙,Qi一共有0,1,2,3四种可能性，分别表示砸左上、右上、左下、右下的墙。  为了简化问题，左右的墙就不给砸了。输出一行一列整数空格隔开，表示从入口到出口的路径
输出一行一列整数空格隔开，表示从入口到出口的路径
    
    #include<iostream>
    using namespace std;
    int DisjoinSet[1000000];
    int visit[1000000];
    int mat[1000][1000];
    int note[1000000][2];
    int dir[1000000][4];
    int n, N;
    void init()
    {
      	int idx = 1;
      	for (int i = 1; i <= n; i++)
    		for (int j = 1; j <= i; j++)//实现地图的初始化，得到如1    的地图
	    	{                                                   //2 3
	          mat[i][j] = idx;                                //4 5 6 
			      DisjoinSet[idx] = idx;//DisjoinSet[]是每个点的父节点的位置，初始状态每个点的父节点均是其本身
			      note[idx][0] = i; note[idx++][1] = j;//note[][]存储的第idx个点在地图中位置的x和y
		    }
	      N = idx - 1;//总点数
    }
    int find(int x)
    {
      	if (x>N || x<1) return -1;
	      if (DisjoinSet[x] == x)
		        return x;
	      else
		        return DisjoinSet[x] = find(DisjoinSet[x]);
    }
    int find2(int x)//
    {
	      if (x>N || x<1) return -1;
	      int stack[1000];
	      int top = 0;
	      while (DisjoinSet[x] != x)
	      {
		        stack[top++] = x;
		        x = DisjoinSet[x];
	      }
	      for (int i = 0; i<top; i++)
		        DisjoinSet[stack[i]] = x;
	      return x;
    }
    void join(int r1, int r2, int t)//t是敲墙的选项，0 1 2 3分别代表着四个方向
    {
	    dir[r2][t] = 1;
	    r1 = find(r1);
	    r2 = find(r2);
	    if (r2>N || r2<1) return;
	    DisjoinSet[r2] = DisjoinSet[r1];
    }
    bool f = false;
    int path[100000];
    int dy[4] = { -1,0,0,1 };//dx  dy分别记录当i是0 1 2 3对应的位置
    int dx[4] = { -1,-1,1,1 };
    void dfs(int pos, int dst, int step, bool &flag)//pos是起点,dst是终点,step记录走过的总步数
    {
    	if (flag) return;
	    if (pos == dst)
	    {
		    path[step++] = pos;
		    for (int i = 0; i < step; i++)
			    cout << path[i] << ' ';
		    flag = true;
		    return;
	    }
	    visit[pos] = 1;
	    path[step++] = pos;
	    int temp;
	    for (int i = 0; i<4; i++)
	    {
		    if (!dir[pos][i]) continue;//找以pos为点敲过的方向位置
		    temp = mat[note[pos][0] + dx[i]][note[pos][1] + dy[i]];
		    if (temp && !visit[temp] && find(temp) == find(dst))//即当这一步可以走到终点时以这一点为起点继续往下走
		    {
			    dfs(temp, dst, step, flag);
		    }
	    }
	    visit[pos] = 0;//如果这条道路走不通一定要回溯把这条道路的经过记忆清空，且如下减去这一步
	    step--;
    }
    int main()
    {
	    ios::sync_with_stdio(0);
	    cin.tie(0);
	    cout.tie(0);
	    int a, b, x, y;
	    cin >> n >> a >> b;
	    init();
	    do {//当起点与终点连接起来时退出，而不需要继续输入即可直接得到答案
		    cin >> x >> y;
		    dir[x][y] = 1;
		    switch (y)
		    {
		    case 0: if (note[x][1]>1 && note[x][0]>1)//保证敲墙的这一面可以被敲
		    	join(x, mat[note[x][0] - 1][note[x][1] - 1], 3); break;
		    case 1: if (note[x][0]>1) join(x, mat[note[x][0] - 1][note[x][1]], 2); break;
		    case 2: if (note[x][0]<n) join(x, mat[note[x][0] + 1][note[x][1]], 1); break;
		    case 3: if (note[x][0]<n) join(x, mat[note[x][0] + 1][note[x][1] + 1], 0); break;
		    }
	    } while (find(a) != find(b));
	    dfs(a, b, 0, f);//确定并输出路线
	    return 0;
    }
