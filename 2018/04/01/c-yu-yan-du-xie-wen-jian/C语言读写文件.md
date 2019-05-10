---
title: C语言读写文件
categories: 
  - C/C++
tags:
  - 编程语言
  - C
date: 2018-04-01
---
**使用标准I/O处理文件**
```
#include <stdio.h>
#include <stdlib.h>	// exit()原型

int main(int argc, char *argv[])
{
	int ch;
	FILE *fp;  // 文件指针
	unsigned long count = 0;
	if (argc != 2)
	{
		printf("Usage: %s filename\n",argv[0]);
		exit(EXIT_FAILURE);
	}
	if ((fp = fopen(argv[1],"r")) == NULL)
	{
		printf("Can't open %s\n",argv[1]);
		exit(EXIT_FAILURE);
	}
	while ((ch = getc(fp)) != EOF)
	{
		putc(ch, stdout); // putchar(ch)相同
		count++;
	}
	fclose(fp);
	printf("File %s hash %lu characters\n", argv[1],count);

	return 0;
}
```
```
//输入/输出升级版，将一个文件附加到另一个文件的末尾
//未测试

#include <stdio.h>
#include <stdlib>
#include <string.h>
#define BUFSIZE 4096
#define SLEN 81

void append(FILE *source,FILE *dest);  //函数原型,没有返回值，参数为两个文件指针
char *s_gets(char *st,int n);

int main(void)
{
	FILE *fa, *fs;
	int file = 0;
	char file_app[SLEN];
	char file_src[SLEN];
	int ch;
	
	puts("Enter name of destination file:");
	s_gets(file_app,SLEN);
	if ((fa = fopen(file_app,"a+") = NULL)
	{
	fprintf(stderr, "Can't opne %s\n", file_app);
		exit(EXIT_FAILURE);		//程序结束失败
	}
	if(setvbuf(fa,NULL,_IOFBF,BUFSIZE) !=0) // 完全缓冲。若无法缓冲，就返回0
	{
		fputs("Can't creat output buffer\n",stderr);
		exit(EXIT_FAILURE);
	}
	puts("Enter name of first source file (empty line to quit):");
	while(s_gets(file_src,SLEN) && file_src[0] != '\0')
	{
		if (strcmp(file_src,file_app)==0)
			fputs("Can't append file to itself\n",stderr);
		else if ((fs = fopen(file_src,"r")) == NULL)
			fprintf(stderr, "Can't open %s\n",file_src);
		else
		{
			 if (setvbuf(fs, NULL,_IOFBF,BUFSIZE) != 0)
			 {
				fputs("Can't creat input buffer\n",stderr);
				continue;
			 }
			 append(fs,fa);
			 if (ferror(fs) != 0)
				 fprintf(stderr,"Error in reading file %s.\n",file_src);
			 if (ferror(fa) != 0)
				 fprintf(stderr,"Error in writing file %s.\n",file_app);
			 fclose(fs);
			 files++;
			 puts("Next file (empty line to quit):");
		}
	}
	printf("Done appending. %d files appending.\n", files );
	rewind(fa);
	printf("%s continue:\n", file_app);
	while  ((ch = gets(fa) != EOF)
		putchar(ch);
	puts("Done displaying.");
	fclose(fa);
	
	return 0;
}

void append(FILE *source, FILE *dest)
{
	size_t bytes;
	static char temp[BUFSIZE];
	
	while((bytes = fread(temp,sizeof(char),BUFSIZE,source)) > 0)	//二进制形式处理数据
		fwrite(temp,sizeof(char),bytes, dest);
	return 0;
}

char *s_gets(char *st, int n)
{
	char *ret_val;
	char *find;
	
	ret_val = fgets(st,n, stdin);	// fgets(类型,长度,文件指针)
	if(ret_val)
	{
		find = strchr(st,'\n');	//查找换行符
		if(find)
		
			*find = '\0';
		else
			while(getchar() != '\n')
				continue;
	}
	return ret_val;
}
```