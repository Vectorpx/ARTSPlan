# algorithm
**题目**：在给定int数组中存在着两个不同的数字，而其他数字都存在另一个数与它对应（相同），求出此数组两个不同的数字。
**解法**：熟悉C++位操作的同学知道一种位操作（异或）能够消除相同的数据，规则是

```cpp
1^1=0;
1^0=1;
0^1=1;
0^0=1;
```
**显而易见，位上相同异或会置0，位上不相同会置1，所以相同的数字在进行异或时，会变为0，而对于任何数异或0都是它本身。如果将全部的数异或一次，那么剔除的是相同的数，得到的结果也是两个不同数的异或结果。我们知道异或的两个数，只有咋对应位不同的时候才会得到结果1，显然最终的结果不是0，不然整个数组都是两两相对的。**
**如果我们根据异或结果某位为1的数给整个数组分类，那么就能找到这个不同的数了，原因是这两个不同的数必然有一位是不同的。**

```cpp
int func(int arr[],int a,int b)
{
	int num=0,a=0,temp=1;
	for(int i=0;i<sizeof(arr)/sizeof(int);++i)
	{	
		num^=arr[i];
	}
	while((num^temp)==0)		//找到不同的位
		temp<<1;
	for(int i=0;i<sizeof(arr)/sizeof(int);++i)
	{	
		if(temp&arr[i]>0)  //如果大于1，表示在temp位上这个数字为1，所以我们把数字为1的分为一类
		a^=arr[i];      //与异或结果再一次异或，得到的是在temp位上为0的单独的数
	}
	b=num^a;//得到在temp位上为1的单独的数
}
```
拿到题目之后，想到的解决方法是异或，但是以后就没有思路了，看了解题思路后自己写出的........

---

# Tip

#### 当我们在判断条件语句的时候，更多的使用的数据switch case 和if else。如果在作用域类是函数，整个代码看起来还不是那么臃肿，反之看起来非常的不顺眼。
**目前有三种方法解决**
- 使用map构建树和节点，查询时间复杂度为log(N)，扩展起来也非常方便
- 使用数组，因为数组是连续的，意味着索引映射到函数也是连续的，查询复杂度为(N)，扩展起来，特别是增删不是很方便。
- 使用C++特性---抽象继承。(不实现)


```cpp
// 动物会一些动作
enum	type_func
{
	type_begin = -1,
	type_eat,
	type_sleep,
	type_walk,
	type_run,
	type_smile,
	type_cry,
	type_jump,
 
	type_max_size,
};
 
class	CAnimal
{
public:
	typedef	int	(CAnimal::*ptr_func)(bool);
 
 
protected:
 
	static map<type_func,ptr_func>	s_map;					
	static ptr_func					s_array[type_max_size];			
 
public:
	CAnimal()
	{
		memset(s_array,0,sizeof(s_array));
		Init();	
	}
 
	// 需要映射函数的返回值 和 参数必须 统一
	int		eat		(bool= true)		{	return printf("eatn")	,1;	}
	int		sleep	(bool= true)		{	return printf("sleepn"),1;	}
	int		walk	(bool= true)		{	return printf("walkn")	,1;	}
	int		run		(bool= true)		{	return printf("runn")	,1;	}
	int		smile	(bool= true)		{	return printf("smilen"),1;	}
	int		cry		(bool= true)		{	return printf("cryn")	,1;	}
	int		jump	(bool= true)		{	return printf("jumpn")	,1;	}
 
	// 初始化
	void	Init	()
	{
		s_map[type_eat]		= &CAnimal::eat;
		s_map[type_sleep]	= &CAnimal::sleep;
		s_map[type_walk]	= &CAnimal::walk;
		s_map[type_run]		= &CAnimal::run;
		s_map[type_smile]	= &CAnimal::smile;
		s_map[type_cry]		= &CAnimal::cry;
		s_map[type_jump]	= &CAnimal::jump;
 
		s_array[type_eat]	= &CAnimal::eat;
		s_array[type_sleep]	= &CAnimal::sleep;
		s_array[type_walk]	= &CAnimal::walk;
		s_array[type_run]	= &CAnimal::run;
		s_array[type_smile]	= &CAnimal::smile;
		s_array[type_cry]	= &CAnimal::cry;
		s_array[type_jump]	= &CAnimal::jump;
	}
 
	// 一般做法是switc case 或者 if else...  
	// 其实这里看起来还不算糟糕，一方面这里我把每个模块内容都封装到相应函数了
	// 分支内部才会看起来相对简洁，实际编码中可能就不是你现在所看到的方式。
	void	Process (type_func type)
	{
		switch (type)
		{
		case type_eat:		eat();		break;
		case type_sleep:	sleep();	break;
		case type_walk:		walk();		break;
		case type_run:		run();		break;
		case type_smile:	smile();	break;
		case type_cry:		cry();		break;
		case type_jump:		jump();		break;
		}
	}
 
	// 很熟悉的感觉吧！ :)
	void	Process2(type_func type)
	{
		if (type_eat == type)
		{
			eat();
		}
		else if (type_sleep == type)
		{
			sleep();
		}
		else if (type_walk == type)
		{
			walk();
		}
		else if (type_run == type)
		{
			run();
		}
		else if (type_smile == type)
		{
			smile();
		}
		else if (type_cry == type)
		{
			cry();
		}
		else if (type_jump == type)
		{
			jump();
		}
	}
 
	// 使用map 映射
	void ProcessByUseMap(int key, bool val)
	{
		map<type_func,ptr_func>::iterator it =  s_map.find((type_func)key);
		if (it != s_map.end())
		{
			ptr_func pFun = it->second;
			if (pFun)	
				(this->*pFun)(val);
		}
	}
 
	// 使用数组 映射
	void ProcessByUseArray(int key, bool val)
	{
		// 数组
		if (type_begin < key && type_max_size > key)
		{
			ptr_func pFun = s_array[key];
			if (pFun)	
				(this->*pFun)(val);
		}
	}
 
 
	// 使用map 映射
	int	operator[]	(int key)
	{
		map<type_func,ptr_func>::iterator it =  s_map.find((type_func)key);
		if (it != s_map.end())
		{
			ptr_func pFun = it->second;
			if (pFun)		return (this->*pFun)(false);
		}
		return NULL;
	}
 
	// 使用数组 映射
	int operator()	(int key,bool val)
	{
		if (type_begin < key && type_max_size > key)
		{
			ptr_func pFun = s_array[key];
			if (pFun)		return (this->*pFun)(val);
		}
		return NULL;
	}
 
};
 
map<type_func, CAnimal::ptr_func>	CAnimal::s_map;					
CAnimal::ptr_func					CAnimal::s_array[type_max_size];
 
 
//////////////////////////////////////////////////////////////////////////
// 非成员函数
void	func_eat(int = 0)	{	}
void	func_run(int = 0)	{	}
void	func_walk(int =0)	{	}
void	func_cry(int = 0)	{	}
 
typedef void	(*ptrFun)(int);
map<type_func,ptrFun>	g_map;
ptrFun					g_array[type_max_size];
 
 
 
 
int _tmain(int argc, _TCHAR* argv[])
{
 
	//////////////////////////////////////////////////////////////////////////
	// 为了便于说明，下面代码不做安全检查
 
	// 非成员函数映射2种用法
	// init
	g_map[type_eat] = func_eat;
	g_map[type_run] = func_run;
	g_map[type_walk] = func_walk;
	g_map[type_cry] = func_cry;
 
	g_array[type_eat] = func_eat;
	g_array[type_run] = func_run;
	g_array[type_walk] = func_walk;
	g_array[type_cry] = func_cry;
 
	// using
	g_map[type_eat](1);
	g_map[type_run](2);
	g_map[type_walk](3);
	g_map[type_cry](4);
 
	g_array[type_eat](1);
	g_array[type_run](2);
	g_array[type_walk](3);
	g_array[type_cry](4);
 
 
	//////////////////////////////////////////////////////////////////////////
	// 成员函数映射使用
 
	CAnimal Dog;
 
	Dog.Process(type_eat);
	Dog.ProcessByUseMap(type_run,true);
	Dog.ProcessByUseArray(type_cry,false);
	Dog[type_walk];
	Dog(type_sleep,true);
	Dog(type_run,false);
 
 
	return 1;
}

```