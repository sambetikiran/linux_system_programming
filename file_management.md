## program to write the  info into the file
```c
#include<stdio.h>
#include<string.h>
#include<unistd.h>
#include<fcntl.h>
int main()
{
        int fd=0;
        fd=open("create1.txt",O_WRONLY|O_CREAT,0644);
        char str[100];
        printf("enter the string");
        fgets(str,sizeof(str),stdin);
        write(fd,str,strlen(str));
        close(fd);
}
```
## program to read the info from the file by character by character.
```c
#include<string.h>
#include<stdio.h>
#include<unistd.h>
#include<fcntl.h>
int main()
{
        int fd=open("create1.c",O_RDONLY,0644);
        int ch;
        ssize_t n;
        while(n=read(fd,&ch,1)>0)
        {
                write(1,&ch,1);
                sleep(1);
        }

        close(fd);
}
```
## program to print how many lines in a given file
```c
#include<stdio.h>
#include<string.h>
#include<unistd.h>
#include<fcntl.h>
int main()
{
        int fd=open("create1.txt",O_RDONLY,0644);
        char ch;
        ssize_t n;
        int count=0;
        while((n=read(fd,&ch,1))>0)
        {
                if(ch=='\n')
                {
                        count++;
                }
        }
        printf("numberr of lines are-->%d\n",count);
        close(fd);
}
```
## program to read the a line from a file
```c
#include<stdio.h>
#include<unistd.h>
#include<fcntl.h>
#include<string.h>
int main()
{
        int fd=open("create1.txt",O_RDONLY,0644);
        char ch;
        ssize_t n;
        while(n=read(fd,&ch,1)>0&&ch!='\n')
        {
                write(1,&ch,1);
        }
        printf("\n");

        close(fd);
}
```
## prorgam to printing word by word from a file
```c
#include<stdio.h>
#include<unistd.h>
#include<string.h>
#include<fcntl.h>
int main()
{
        int fd=open("create1.txt",O_RDONLY,0644);
        char ch;
        ssize_t n;
        while((n=read(fd,&ch,1))>0)
        {
                if(ch==' '||ch=='\n')
                {
                        printf("\n");
                        continue;
                }
                write(1,&ch,1);
        }
        close(fd);
}
```
## program to copy the data from one file to another.
```c
#include<stdio.h>
#include<string.h>
#include<unistd.h>
#include<fcntl.h>
int main()
{
        int fd=open("create1.txt",O_RDONLY,0644);
        char ch;
        int kd=open("create2.txt",O_WRONLY|O_CREAT,0644);
        ssize_t n;
        char str[100];
        int i=0;
        while((n=read(fd,&ch,1))>0)
        {
                str[i++]=ch;
        }
        str[i]='\0';
        for(int j=0;j<i;j++)
        {
                write(kd,&str[j],1);
        }
        close(fd);
        close(kd);
}
```
## program to remove the extra spaces in a string .
```c
#include<string.h>
#include<stdio.h>
#include<unistd.h>
#include<fcntl.h>
int main()
{
        int fd=open("create2.txt",O_RDONLY,0644);
        int dst=open("space_clear1.txt",O_WRONLY|O_CREAT|O_TRUNC,0644);
        char ch;
        ssize_t n;
        char str[100];
        int k=0;
        while((n=read(fd,&ch,1))>0)
        {
                str[k++]=ch;
        }
        str[k]='\0';
        char new[100];
        int l=0;
        int count=0;
        int flag=0;
        for(int i=0;i<k;i++)
        {
                if(str[i]!=' ')
                {
                        new[l++]=str[i];
                        flag=0;
                }
                else
                {
                        if(flag==0)
                        {
                                new[l++]=' ';
                                flag=1;
                        }
                }
        }
        new[l]='\0';
        write(dst,new,l);
        close(fd);
        close(dst);
}
```
## 3 Implement a C program to create a new directory named "Test" in the current directory? 
```c
#include<stdio.h>
#include<sys/stat.h>
#include<sys/types.h>
int main()
{
        int stats=mkdir("test",0755);
        if(stats)
        {
                printf("created\n");
        }
        else
        {
                printf("not created\n");
        }
}
```
## 16. Write a C program to append "Goodbye!" to the end of an existing file named "message.txt"? 
```c
#include<stdio.h>
#include<fcntl.h>
#include<stdlib.h>
#include<string.h>
int main()
{
        int fd=open("newfile.txt",O_WRONLY|O_APPEND);
        char buf[100];
        fgets(buf,sizeof(buf),stdin);
        write(fd,buf,strlen(buf));
}
```
## 17. Implement a C program to change the permissions of a file named "file.txt" to read only? 
```c
#include<stdio.h>
#include<sys/stat.h>
int main()
{
        int ret=chmod("file.txt",0644);
        if(ret>=0)
        {
                printf("changed the file permission\n");
        }
        else
        {
                printf("not changed the permissioni\n");
        }
}
```
