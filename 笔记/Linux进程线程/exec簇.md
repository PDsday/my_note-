>好不容易理解[[进程基础]]，却发现子进程仅仅是父进程的复制品，要这个孩子又有什么用呢？
>显然，单纯的复制一份程序一起跑确实没有意义，事实上在一般情况下，我们会先准备一个事先编写好的ELF文件或者脚本，用以覆盖从父进程复制过来的代码。而加载这些代码进入子进程的接口函数，为exec簇。

_先回顾几个单词_
_list(列表)、vector(矢量)、path(路径)、environment(环境)、_

exec家族成员有许多：
- execl
- execv
- execle
- execlp
- execvp
- execvpe

_exec的接口长得都差不多，在运用上，可以依据后缀进行区别。例如
1，函数名带字母 l 意味着其参数以列表（list）的方式提供。
2，函数名带字母 v 意味着其参数以矢量（vector）数组的方式提供。 
3，函数名带字母 p 意味着会利用环境变量 PATH 来找寻指定的执行文件。 
4，函数名带字母 e 意味着用户提供自定义的环境变量_

---
```c
//最常用的两个成员是execl和execv，使用方式如下
#include <stdio.h>
#include <stdlib.h>

int main()
{
	int fork_ret = fork();
	if(fork_ret==-1)
	{
	
	}
	else if(fork_ret==0)
	{
		//假如已经准备好可执行文件test，子进程使用的方式为
		//分别是执行可执行文件，同时设置argv参数，argv[0]即是文件名，没有后续参数
		execl("./test","test",NULL);
		//使用execv调用则是使用矢量
		//const char *argv[2] = {"test", NULL};
		//execv("./test",argv);
	}
	else
	{
		while(1)
		{
			sleep(1);
			printf("我是父进程");
		}
	}
}

```

