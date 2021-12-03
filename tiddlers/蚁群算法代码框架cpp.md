# 蚁群算法代码框架

## 算法简介

蚁群算法具有全局搜索、分布式计算、信息正反馈和启发式搜索的特征，本质上是进化算法中的一种启发式全局优化算法，它有较强的鲁棒性，并且易于与其他方法相结合。

根据蚁群的觅食行为，利用其群体活动中：自组织的特性、分布式计算和正反馈机制，达到搜索收敛的效果。

每一轮迭代每一个蚂蚁都搜索路径，结束后更新信息素，多次迭代后收敛得到解。

## 算法框架

### 算法参数

- 节点数N
- 蚂蚁数M
- 路径搜索时的参数α、β
- 信息素挥发系数ρ

### 运行框架

![image-20210830153709033](C:\Users\lei\AppData\Roaming\Typora\typora-user-images\image-20210830153709033.png)

## 代码程序

### 类1——Graph

#### 主要成员

- tau信息素矩阵
- visi启发式信息矩阵
- prob_matrix转移概率值矩阵

#### 主要成员函数

- 构造函数，初始化启发式信息
- 路径构造之前，更新转移概率值矩阵
- 路径构造之后，信息素更新，给ant提供接口

#### 主要代码

```cpp
class Graph
{
public:
	Graph();//初始化
	void update_prob_matrix();//更新转移概率矩阵（未归一化）
	double get_prob(int i, int j) { return prob_matrix[i][j]; };
	double get_dis(int i, int j) { return distance[i][j]; };
	void update_tau_reduce();//挥发
	void update_tau_increase(const int path[N_dim], const double length);//一只蚂蚁的新增信息素

private:
	double distance[N_dim][N_dim];
	double tau[N_dim][N_dim];
	double visible[N_dim][N_dim];
	double prob_matrix[N_dim][N_dim];
};

```

### 类2——Ant

#### 主要成员

- tabu，禁忌表，构造路径时用
- path_array，路径，即一个解的编码
- path_length，解的适应度
- graph，静态，蚂蚁活动与图交互。

#### 主要成员函数

- find_path()，寻路
- calc_length()：计算适应度
- 增加信息素

#### 主要代码

```cpp
class Ant
{
private:
	int currentCity, nextCity;
	int currentTourIndex;
	int tabu[N_dim];

public:
	int path[N_dim + 1];			//路径，解
	double path_length = INT_MAX;	//适应度

	//初始化所有值
	void init();
	////计算转移值
	//double get_trans_value(int current_index, int next_index);

	void choose_first();
	//根据目前的地点，寻找下一个地点，记录在nextCity中
	void select_next(); //寻找下一个地点，即更新next_city
	//寻找下一个地点后，更新状态，包括： path &currentCity &tabu
	void update_status();
	//每一只蚂蚁构建完整路径
	void find_path();
	//计算适应度,
	void calc_length();

	void show_ant_path();


	static Graph graph;

	void add_tau();
};
```

### 类3——ACA

#### 主要成员

- Ant ants[M];蚂蚁们，种群，解的集合
- Ant best_ant;最优解记录

#### 主要成员函数

- 路径构建
- 更新信息素

#### 主要代码

```cpp
class AntColony
{
private:
	Ant ants[M];
	Ant best_ant;

public:
	AntColony();
	void construct_solution(); //路径构建
	void update_pheromone();   //更新信息素
	bool show_current_optimal(); //更新最优值则返回false，局部最优则返回true，输出当前最优解
	void show_best();

	void aca_go(int bad_time=300);
};
```

