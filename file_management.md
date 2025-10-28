## copy to another
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
