### Algorithm
题意:	给你两个单词 word1 和 word2，请你计算出将 word1 转换成 word2 所使用的最少操作数 。
你可以对一个单词进行如下三种操作：

插入一个字符
删除一个字符
替换一个字符

这道题每进行一步操作都会触发对以后n步的影响,所以可以每进行一步算可能影响的值。对A插入就相当于对B删除；对B插入就相当于对A删除；修改A中字符也可以理解成对B进行修改。其中六种操作实际上只有3种，所以采用动态规划。

```cpp
int word(string word1,string word2)
{
	int width=word1.size();
	int heigh=word2.size();
	int arr[width][heigh]=0;
	if(!width*heigh) return min(width,heigh);
	for(int i=0;i<width+1;++i)
	{	
		arr[i][0]=i;
	}
	for(int j=0;j<heigh+1;++j)
	{
		arr[0][j]=j;
	}
	for(int i ;i<width+1;++i){
		for(int j;j<heigh+1;++j)
		{
			int left=arr[i-1][j]+1;
			int down=arr[i][j-1]+1;
			int left_down=arr[i-1][j-1];
			if(word1[i-1]!=word2[j-1]) left_down+=1;
			arr[i][j]=min(left,min(down,left_down));
		}
	}
	return arr[width][heigh];
	}
}
```

### Review
#### 《[Learn C++ Multi-Threading in 5 Minutes](https://medium.com/hackernoon/learn-c-multi-threading-in-5-minutes-8b881c92941f)》
这篇文章主要介绍一种多线程并发的结构，把一些都交给内核来处理，等到处理完成后再返回。一般多线程做法如下

 1. 使用函数指针
 2. 使用函子函数
 3. 使用lambda函数

使用函数指针，如果需要返回数据可以通过参数，std：：thread没有返回值。需要使用join函数来等待线程完成。
函子函数和函数指针非常相似，无需设置参数来返回结果，可以将返回值存储在函子函数的成员变量中。
lambda函数是通过lambda捕捉器捕捉返回值。


##### Task Futures Promises
现在使用tasks代替std：：thread，与线程相比，tasks能够有返回值，是一种更抽象定义线程的方式。

```cpp
#include <future>
//Tasks, Future, and Promises
    {
        auto f1 = [](std::vector<int> &v, 
            unsigned int left, unsigned int right) {
            unsigned long long acm = 0;
            for (unsigned int i = left; i < right; ++i)
            {
                acm += v[i];
            }

            return acm;
        };
        auto t1 = std::async(f1, std::ref(v), 
            0, v.size() / 2);
        auto t2 = std::async(f1, std::ref(v), 
            v.size() / 2, v.size());
        //You can do other things here!
        unsigned long long acm1 = t1.get();
        unsigned long long acm2 = t2.get();

        std::cout << "acm1: " << acm1 << endl;
        std::cout << "acm2: " << acm2 << endl;
        std::cout << "acm1 + acm2: " << acm1 + acm2 << endl;
    }
```
使用std::asynsc定义和创建线程,返回值是一个std::future的对象，可以通过get函数获取它的返回值。


### Tip
本周在工作中学到了libcurl库的知识，从安装到使用。下面对一个知识点结合我的认识做一个分享说明。
```
CURLcode curl_easy_setopt(CURL *curl, CURLoption option, ...);
```
三个点号代表后面还有未知个参数，这个和printf函数是一样的，可以去网上查查看这个。

 1. 第一个参数：使用curl_init函数创建出来的对象。
 2. 第二个参数：属性。有很多种。
 3. 第三个参数：如果有，可能是属性的值，比如一些true或false，或者数字。


现在主要讲 **CURLOPT_SSL_VERIFYHOST** 选项，在libcurl早期版本中，这个参数的值，也就是第三个参数是bool型，后来又出现检查CN字段（SSL/TLS）中出现的。 在之后版本中，这个值就已经不够用了，而开发者也经常因为之前使用这个函数设置为True或False（之前死两个值）而出错(现在是三个值)。

```
0	：不检查证书
1	：检查证书不检查域名
2	：既检查证书又检查域名
```
曾经做过HTTPS请求分析，对这个三个状态还是比较熟悉，我来解释一下。

**首先不检查证书，一些网站虽然在HTTP协议上加入了SSL和TLS加密，对服务器和客户端做中间人攻击，发现服务器端根本不会检查证书，也就是它空有一个加密协议，而没有校验你的证书；**

**下一步就是检查证书，而没有检查域名，也就是说你可以随意用一个假的证书，就能骗过服务器，与你中间程序交互，这种也不太可靠，随便用一些工具生成证书就能蒙混过关；**

**最后一种是既检查证书，又能要检测域名，第二种是空有一个证书的，是没有host域名的，而且这个域名是服务器相对应的，即使是一个合格证书，有一个域名，和服务器对不上，还是没有作用，欺骗不了服务器。**

所以在做中间人攻击的时候，空有一个证书是吓唬不了人的，大家应该有信心，帮助互联网公司完成标准化，哈哈哈.......

回到这个主题，新版本已经废除了为1的选项，现在只存在0或2的选值。（默认是2）

## 以上第一周的分享，谢谢。
