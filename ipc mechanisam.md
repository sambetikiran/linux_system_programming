## 1. What is meant by an IPC Mechanism?
```
Inter-Process Communication (IPC) mechanism is a method that allows different processes to exchange data and information with each other, either within the same system or across a network.
```
## 2. Why we use IPC Mechanism?
```
IPC is used to enable communication and data sharing between processes, synchronize their execution, and coordinate tasks to achieve parallel processing or modular program design.
```
## 3. What are the types of IPC Mechanisms?
```
The main types of IPC mechanisms are:
* Pipes
* named pipes
* Message Queues
* Shared Memory
* Semaphore
```
## 4. What is meant by “unicast” and “multicast” IPC?
```
Unicast IPC: Communication occurs between a single sender and a single receiver process.
Multicast IPC: Communication occurs between one sender and multiple receiver processes simultaneously.
```
5. What is meant by PIPES?
```
Pipes are a unidirectional IPC mechanism that allows one process to send data to another in a sequential stream, typically between a parent and child process.
```
6. What is meant by Blocking Calls?
```
A blocking call is an operation that makes a process wait until the requested operation (like reading or writing data) completes before continuing execution.
```
## 7. What are the types of Blocking Calls?
```
The main types are:
Blocking I/O: Process waits until operation completes.
Non-blocking I/O: Process continues execution even if the operation isn’t complete.
```
## 8. What are the different types of I/O Calls?
```
The main I/O call types are:
Blocking I/O
Non-blocking I/O
Asynchronous I/O
Synchronous I/O
```
## 9. What are the I/O calls we are used in IPC Mechanisms?
```
In IPC, the common I/O system calls used are:
* `read()`
* `write()`
* `open()`
* `close()`
* `pipe()`
* `mkfifo()`
```
## 10. What are the Blocking Calls used in IPC?
```
The blocking calls used in IPC are mainly:
* `read()` — waits until data is available.
* `write()` — waits until the data is written to the pipe or queue.
```
## 41. Implement a program that uses pipes for communication between a parent and child process. Show how data can be passed between processes using pipes.
```c
#include<stdio.h>
#include<stdlib.h>
#include<string.h>
#include<unistd.h>
int main()
{
        int fd[2];
        int pid;
        char str[100];
        char buf[100];
        pipe(fd);
        printf("enter the input");
        fgets(str,sizeof(str),stdin);
        str[strcspn(str,"\n")]='\0';
        pid=fork();
        if(pid<0)
        {
                perror("empty\n");
                exit(1);
        }
        if(pid==0)
        {
                close(fd[1]);
                printf("child is reading msg\n");
                sleep(2);
                int ret=read(fd[0],buf,sizeof(buf));
                printf("%s\n",buf);
                buf[ret]='\0';
                close(fd[0]);
        }
        else
        {
                close(fd[0]);
                int ret=write(fd[1],str,strlen(str)+1);
                close(fd[1]);
        }
}
```
## 43. Implement a program that uses Named pipes for communication between two processes. 
```c
// write process
#include<stdio.h>
#include<string.h>
#include<stdlib.h>
#include<unistd.h>
#include<fcntl.h>
#include<sys/stat.h>
int main()
{
        mkfifo("myfifo",0666);
        char str[100];
        printf("enter the input\n");
        fgets(str,sizeof(str),stdin);
        str[strcspn(str,"\n")]='\0';
        int fd=open("myfifo",O_WRONLY);
        printf("write process\n");
        write(fd,str,strlen(str)+1);
        close(fd);
}
// read process
#include<stdio.h>
#include<string.h>
#include<stdlib.h>
#include<unistd.h>
#include<fcntl.h>
#include<sys/stat.h>
int main()
{
        char buf[100];
        mkfifo("myfifo",0666);
        int fd=open("myfifo",O_RDONLY);
        printf("read process\n");
        read(fd,buf,sizeof(buf));
        printf("%s\n",buf);
}
```
## 44. Write a C program to create a message queue using the msgget system call. Ensure that the program checks for errors during the creation process. 
```c
#include<stdio.h>
#include<sys/ipc.h>
#include<sys/msg.h>
#include<stdlib.h>
# define KEY 1220
int main()
{
        int msgid=msgget(KEY,0666|IPC_CREAT);
        if(msgid<0)
        {
                perror("msgget failed");
                exit(1);
        }
        printf("msg queue object is generated\n");
}
```
## 45. Develop two separate C programs, one for sending messages and the other for receiving messages through a created message queue. 
```c
//unidirectional sending info
#include<stdio.h>
#include<string.h>
#include<stdlib.h>
#include<sys/msg.h>
#include<sys/ipc.h>
#include<unistd.h>
#define KEY 1220
#define MSG_TYPE 1
int main()
{
        char rxbuf[100];
        char txuf[100];
        int msgid=msgget(KEY,0666|IPC_CREAT);
        long *ptr=(long*)txbuf;
        ptr[0]=MSG_TYPE;
        ptr[1]=getpid();
        printf("sending the msg");
        scanf("%s",txbuf+16);
        msgsnd(msgid,txbuf,8+strlen(txbuf+16),0);
}
//unidirectional reciving info
#include<stdio.h>
#include<stdlib.h>
#include<sys/msg.h>
#include<sys/ipc.h>
#include<string.h>
#define KEY 1220
#define MSG_TYPE 1
int main()
{
        int msgid=msgget(KEY,0666);
        char txbuf[100];
        printf("client is connected");
        msgrcv(msgid,txbuf,sizeof(txbuf),MSG_TYPE,0);
        printf("recived msg is %s \n",txbuf+16);
}
```
## 48. Write a C program where two processes communicate using message queues.Implement sending and receiving messages between the processes using msgget,msgsnd, and msgrcv. 
```c
//server send and recv
#include<stdio.h>
#include<string.h>
#include<stdlib.h>
#include<sys/msg.h>
#include<sys/ipc.h>
#define KEY 1220
#define MSG_TYPE 6
int main()
{
        char rxbuf[100];
        char txbuf[100];
        int msgid=msgget(KEY,IPC_CREAT|0666);
        printf("msg rcv from client\n");
        msgrcv(msgid,rxbuf,sizeof(rxbuf),MSG_TYPE,0);
        printf("%s \n",rxbuf+16);
        long *ptr1=(long*)txbuf;
        long *ptr2=(long*)rxbuf;
        ptr1[0]=ptr2[1];
        printf("msg is sending to client");
        fgets(txbuf+8,sizeof(txbuf)-8,stdin);
        txbuf[strcspn(txbuf,"\n")]='\0';
        msgsnd(msgid,txbuf,8+strlen(txbuf+8),0);
        printf("sended");
}
//client recv and send
#include<stdio.h>
#include<string.h>
#include<stdlib.h>
#include<sys/msg.h>
#include<sys/ipc.h>
#include<unistd.h>
#define KEY 1220
#define MSG_TYPE 6
int main()
{
        int msgid=msgget(KEY,0);
        char txbuf[100];
        char rxbuf[100];
        fgets(txbuf+16,sizeof(txbuf)-16,stdin);
        txbuf[strcspn(txbuf,"\n")]='\0';
        long *ptr=(long*)txbuf;
        ptr[0]=MSG_TYPE;
        ptr[1]=getpid();
        msgsnd(msgid,txbuf,16+strlen(txbuf+16),0);
        msgrcv(msgid,rxbuf,sizeof(txbuf),getpid(),0);
        printf("%s\n",rxbuf+8);
        printf("recivied");
}
```
## 51. Write a C program that initializes a shared memory segment using shmget.
```c
#include<stdio.h>
#include<stdlib.h>
#include<sys/ipc.h>
#include<sys/shm.h>
#define KEY 1220

int main()
{
        int shmid=shmget(KEY,1024,IPC_CREAT|0666);
        if(shmid==-1)
        {
                perror("shmget error");
                return 0;
        }
        int *shmem=shmat(shmid,NULL,0);
        *shmem=32;
        printf("%d\n",*shmem);

        shmdt(shmem);
}
```
## 52. Develop a program that attaches to a previously created shared memory segment using shmat and detaches using shmdt.
```c
#include<stdio.h>
#include<sys/ipc.h>
#include<sys/shm.h>
#define KEY 1220
int main()
{
        int shmid=shmget(KEY,1024,IPC_CREAT|0666);
        if(shmid==-1)
        {
                perror("shmget error");
        }
        int *shm_mem=shmat(shmid,NULL,0);
        printf("address is %p\n",shm_mem);
        printf("data=%d\n",*shm_mem);
        shmdt(shm_mem);
        if(shmdt<0)
        {
                perror("not detatched");
        }
        printf("shared memory detached");
}
```
## 53. Create a program that forks multiple processes, and each process communicates using shared memory. 
```c
#include<stdio.h>
#include<stdlib.h>
#include<unistd.h>
#include<sys/ipc.h>
#include<sys/shm.h>
#include<sys/wait.h>
#define KEY 1220
int a=1;
int main()
{
        int shmid=shmget(KEY,1024,IPC_CREAT|0666);
        if(shmid<0)
        {
                perror("shmget error");
                exit(1);
        }
        int *shm_mem=shmat(shmid,NULL,0);
        int num;
        int pid;
        printf("enter the numbers");
        scanf("%d",&num);
        for(int i=0;i<num;i++)
        {
                pid=fork();
                if(pid==0)
                {
                        (*shm_mem)++;
                        printf("child->%d its memory is %d\n",getpid(),*shm_mem);
                        shmdt(shm_mem);
                        exit(0);
                }
        }
        for(int i=0;i<num;i++)
        {
                wait(NULL);
        }
}
```
## 54. Write a program that dynamically creates shared memory segments based on user input. 
```c
#include<stdio.h>
#include<stdlib.h>
#include<sys/ipc.h>
#include<sys/shm.h>
#define KEY 1
int main()
{
        int size;
        printf("enter the size of number");
        scanf("%d",&size);
        int shmid=shmget(KEY,size,IPC_CREAT|0666);
        int *shm_mem=shmat(shmid,NULL,0);
        *shm_mem=16;
        printf("the value is %d\n",*shm_mem);
        shmdt(shm_mem);
}
```
## 57. Write a C program that uses semget to create a new semaphore set 
```c
#include<stdio.h>
#include<sys/sem.h>
#include<sys/ipc.h>
#include<stdlib.h>
int main()
{
        int key=ftok("semfile",65);
        int semid=semget(key,1,IPC_CREAT|0666);
        if(semid<0)
        {
                printf("semaphore not created\n");
                exit(1);
        }
        printf("semaphore created successfully-%d\n",semid);
}
```
## 60. Write a program that combines semaphores and shared memory for synchronization between processes. 
```c
//client
#include<stdio.h>
#include<sys/ipc.h>
#include<sys/wait.h>
#include<sys/types.h>
#include<sys/shm.h>
#include<sys/sem.h>
#include<stdlib.h>
#define SHM_KEY 1220
#define SEM_KEY 1221
int main()
{
        int shmid,semid;
        shmid=shmget(SHM_KEY,512,0);
        char *shmptr=shmat(shmid,NULL,0);
        printf("enter the input");
        scanf("%s",shmptr);
        semid=semget(SEM_KEY,2,IPC_CREAT|0666);
        struct sembuf smop;
        smop.sem_num=0;
        smop.sem_op=1;
        smop.sem_flg=0;
        semop(semid,&smop,1);
        printf("messge is sent to server");
}
//server
#include<stdio.h>
#include<stdlib.h>
#include<sys/sem.h>
#include<sys/ipc.h>
#include<sys/shm.h>
#include<sys/wait.h>
#include<sys/types.h>
#define SHM_KEY 1220
#define SEM_KEY 1221
int main()
{
        int shmid,semid;
        shmid=shmget(SHM_KEY,512,IPC_CREAT|0666);
        char *shmptr=shmat(shmid,NULL,0);
        semid=semget(SEM_KEY,2,IPC_CREAT|0666);
        semctl(semid,0,SETVAL,0);
        semctl(semid,1,SETVAL,0);
        struct sembuf smop;
        smop.sem_num=0;
        smop.sem_op=-1;
        smop.sem_flg=0;
        semop(semid,&smop,1);
        printf("message receiviec from client %s \n",shmptr);
}
```
## 66. Write a C program to create a pipe and pass an array of integers from the parent process to the child process through the pipe.
```c
#include<stdio.h>
#include<stdlib.h>
#include<unistd.h>
int main()
{
        int fd[2];
        pipe(fd);
        int rbuf[100];
        int arr[6]={1,2,3,4,5,6};
        int pid=fork();
        if(pid>0)
        {
                close(fd[0]);
                printf("parent is sending to child \n");
                int ret=write(fd[1],arr,sizeof(arr));
                close(fd[1]);
        }
        else
        {
                close(fd[1]);
                printf("child is recieved from parent\n");
                int ret=read(fd[0],rbuf,sizeof(rbuf));
                for(int i=0;i<6;i++)
                {
                        printf("%d\t",rbuf[i]);
                }
                printf("\n");
                close(fd[0]);
        }
}
```
## 67. Implement a program where multiple child processes are created, and each child process communicates with the parent process using pipes. 
```c
#include<stdio.h>
#include<stdlib.h>
#include<unistd.h>
#include<string.h>
#include<sys/wait.h>
int main()
{
        int num;
        printf("enter the no.of child want to create\n");
        scanf("%d",&num);
        int fd[num][2];
        char buf1[100];
        printf("enter the string");
        scanf("%s",buf1);
        for(int i=0;i<num;i++)
        {
                pipe(fd[i]);
        }
        for(int i=0;i<num;i++)
        {
                char buf[100];
                int pid=fork();
                if(pid==0)
                {
                        close(fd[i][1]);
                        int ret=read(fd[i][0],buf,sizeof(buf));
                        printf("%d child output= %s\n",i,buf);
                        close(fd[i][0]);
                        exit(0);
                }
        }
        for(int i=0;i<num;i++)
        {
                for(int i=0;i<num;i++)
                {
                        close(fd[i][0]);
                        int ret=write(fd[i][1],buf1,sizeof(buf1));
                        close(fd[i][1]);
                }
        }
        for(int i=0;i<num;i++)
        {
                wait(NULL);
        }
}
```
## 68. Develop a program that uses pipes for bidirectional communication between two processes, where each process can send and receive messages. 
```c
#include<stdio.h>
#include<stdlib.h>
#include<unistd.h>
#include<string.h>
int main()
{
        int fd1[2];
        int fd2[2];
        pipe(fd1);
        pipe(fd2);
        int pid=fork();
        if(pid==0)
        {
                close(fd1[1]);
                char buf[100];
                printf("child is recving-%d\n",getpid());
                read(fd1[0],buf,sizeof(buf));
                printf("pid=%d of output is %s\n",getpid(),buf);
                close(fd1[0]);
                sleep(2);
                close(fd2[0]);
                char wrt[100];
                printf("enter the input\n");
                scanf("%s",wrt);
                write(fd2[1],wrt,sizeof(wrt));
                close(fd2[1]);
        }
        else
        {
                close(fd1[0]);
                char wrt[100];
                printf("enter the input\n");
                scanf("%s",wrt);
                write(fd1[1],wrt,sizeof(wrt));
                close(fd1[1]);
                sleep(2);
                close(fd2[1]);
                char buf[100];
                printf("child is recving-%d\n",getpid());
                read(fd2[0],buf,sizeof(buf));
                printf("pid=%d of output is %s\n",getpid(),buf);
                close(fd2[0]);
        }
}
```
