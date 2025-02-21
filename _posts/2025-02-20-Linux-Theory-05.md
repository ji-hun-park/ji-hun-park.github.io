---
title:  "Linux 기초 이론 05 SHELL"
excerpt: "Linux 기초 이론 다섯 번째 페이지"
toc_label: "목차"
categories:
  - Linux
tags:
  - Linux
  - Lecture
  - Theory
last_modified_at: 2025-02-20T24:00:00-00:00
---

# Linux Lecture Theory 05 SHELL
## Introduction
본래 Process 이후의 내용이나, 분량 관계상 앞당겼습니다.

# 5.9 smallsh: a command processor
## basic logic of command processor
```c
while(EOF not typed)
{

	get command line from user  ← fgets()

	assemble command args and execute
     			← excute_cmdline()

}
```
![그림01](https://ji-hun-park.github.io/assets/images/LNXIMG038.jpg "그림01"){: .align-center}

## The strtok(3) system call
```c
#include <string.h>

char *strtok(char *restrict s1, const char *restrict s2);

//Return:
// A pointer to the last token found in string.
// A null pointer is returned if there are no tokens left to retrieve.
```
```c
int main(){
  char str[] = "unix system prog";
  char* delimiters = " \t";
  char* token;

  token = strtok(str, delimiters);

```
![그림02](https://ji-hun-park.github.io/assets/images/LNXIMG039.jpg "그림02"){: .align-center}
```c
  while(token!=NULL){
    printf("%s\n", token);
    token = strtok(NULL, delimiters);
  }
  return 0;
}
```
![그림03](https://ji-hun-park.github.io/assets/images/LNXIMG040.jpg "그림03"){: .align-center}

## The strspn(3) system call
```c
#include <string.h>

size_t strspn(const char *s1, const char *s2);

//Return:
// - The length of the initial portion of s1 containing only characters that appear in s2.
// - if all of the characters in s1 are in s2, the function returns the length of the entire str1 string
// - if the first character in s1 is not in s2, the function returns 0.
```
```c
int a = strspn("aabbcc", "a"); 	        //a = 2
int b = strspn("aabbcc", "b");   	//b = 0
int c = strspn("aabbcc", "d");          //c = 0
int d = strspn("aabbcc", "abc"); 	//d = 6
```

## smallsh – global variable & functions
```c
#include <errno.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/wait.h>
#include <fcntl.h>

#define MAX_CMD_ARG 10
#define MAX_CMD_GRP 10

const char *prompt = "Command> ";

char* cmdgrps[MAX_CMD_GRP];
char* cmdvector[MAX_CMD_ARG];
char  cmdline[BUFSIZ];

void fatal(char *str);
void execute_cmd(char *cmd);
void execute_cmdgrp(char* cmdgrp);
int makelist(char *s, const char *delimiters, 
	                              char** list, int MAX_LIST);
```
![그림04](https://ji-hun-park.github.io/assets/images/LNXIMG041.jpg "그림04"){: .align-center}

## smallsh – main()
```c
int main(int argc, char**argv)
{
  int i=0;
  while (1) {
      
  fputs(prompt, stdout);
  fgets(cmdline, BUFSIZ, stdin);
  cmdline[ strlen(cmdline) -1] ='\0';
        
  execute_cmdline(cmdline);
  printf("\n");

  }
  return 0;
}

void fatal(char *str)
{
  perror(str);
  exit(1);
}
```

## smallsh – execute_cmdline()
```
 cmdline = “ls –l ; ps”
```
```c
void execute_cmdline(char* cmdline)
{
  int count = 0;
  int i=0;
  count = makelist(cmdline, ";", cmdgrps, MAX_CMD_GRP);	
       
  for(i=0; i<count; ++i)
  {
    switch(fork())
    {
      case -1: fatal("fork error");
      case  0: execute_cmdgrp(cmdgrps[i]);
      default:
        wait(NULL);
                    fflush(stdout);
    }
  }
}
```
![그림05](https://ji-hun-park.github.io/assets/images/LNXIMG042.jpg "그림05"){: .align-center}

## smallsh – execute_cmdgrp()
```
 cmdgrp = “ls –l ”
```
```c
void execute_cmdgrp(char *cmdgrp)
{
  int i=0; 
  int count = 0;
	
  count = makeargv(cmdgrp, " \t", cmdvector, MAX_CMD_ARG);
  execvp(cmdvector[0], cmdvector);

  fatal("exec error");
}
```
![그림06](https://ji-hun-park.github.io/assets/images/LNXIMG043.jpg "그림06"){: .align-center}

## smallsh – makelist()
```c
int makelist(char *s, const char *delimiters, 
   					char** list, int MAX_LIST){
  int i = 0;
  int numtokens = 0;
  char *snew = NULL;

  if( (s==NULL) || (delimiters==NULL) ) return -1;

  snew = s + strspn(s, delimiters);	//delimiters를 skip
  if( (list[numtokens]=strtok(snew, delimiters)) == NULL )
    return numtokens;
	
  numtokens = 1;
  while(1){
     if( (list[numtokens]=strtok(NULL, delimiters)) == NULL)
	break;
     if(numtokens == (MAX_LIST-1)) return -1;
     numtokens++;
  }
  return numtokens;
}
```

## 마무리
이상으로 Linux 이론의 셸편을 마치겠습니다.  
긴 글 읽어주셔서 감사합니다!
