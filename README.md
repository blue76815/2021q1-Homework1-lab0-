# 2021q1 Homework1 (lab0)
###### tags: `linux2021`

## 開發環境

```
$ uname -a
Linux blue76815-tuf-gaming-fx504ge-fx80ge 5.4.0-66-generic 
#74-Ubuntu SMP Wed Jan 27 22:54:38 UTC 2021 
x86_64 x86_64 x86_64 GNU/Linux

$ gcc --version
gcc (Ubuntu 9.3.0-17ubuntu1~20.04) 9.3.0 
Copyright (C) 2019 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
```

## 下載lab0
從 [sysprog21/lab0-c](https://github.com/sysprog21/lab0-c/network/members) fork後
![](https://i.imgur.com/Dppt3gB.png)

![](https://i.imgur.com/DAQe6fI.png)

[在我的 GitHub 帳號下載 lab0-c 專案](https://github.com/blue76815/lab0-c)

![](https://i.imgur.com/jteSU9L.png)

`$ git clone https://github.com/blue76815/lab0-c.git`

切換到 lab0-c 目錄並嘗試編譯程式碼:

```
blue76185@blue76815-tuf-gaming-fx504ge-fx80ge:~/桌面/Linux_2021$ cd lab0-c/
```
編譯測試

```
blue76185@blue76815-tuf-gaming-fx504ge-fx80ge:~/桌面/Linux_2021/lab0-c$ make

Git hooks are installed successfully.

  CC	qtest.o
  CC	report.o
  CC	console.o
  CC	harness.o
  CC	queue.o
  CC	random.o
  CC	dudect/constant.o
  CC	dudect/fixture.o
  CC	dudect/ttest.o
  CC	linenoise.o
  LD	qtest
  
blue76185@blue76815-tuf-gaming-fx504ge-fx80ge:~/桌面/Linux_2021/lab0-c$ make clean
rm -f qtest.o report.o console.o harness.o queue.o random.o dudect/constant.o dudect/fixture.o dudect/ttest.o linenoise.o .qtest.o.d .report.o.d .console.o.d .harness.o.d .queue.o.d .random.o.d .dudect/constant.o.d .dudect/fixture.o.d .dudect/ttest.o.d .linenoise.o.d *~ qtest /tmp/qtest.*
rm -rf .dudect
rm -rf *.dSYM
(cd traces; rm -f *~)
  
```
## queue_t 結構
修改queue.h

```
typedef struct {
    list_ele_t *head; /* Linked list of elements */
    list_ele_t *tail;
    int size;    
} queue_t;
```
## To do list
 - [ ] 要附上 strlen(const char str) 規格書說明

由於 strlen(const char str) 計算字串 str 的長度，**但不包括終止空字符 (NULL)。**


因此在 `q_insert_head(queue_t *q, char *s)`
和 `q_insert_tail(queue_t *q, char *s)` 中

節點內配置新字串空間個數時,為 strlen(s)**+1** 個

多加一格,是為了**在字串結尾多填一格 NULL 值**

**避免 strlen(const char str) 計算字串長度時,把字尾相鄰的記憶體殘值也列入字元個數**
```
newh->value=malloc(sizeof(char)*strlen(s)+1);
memset(newh->value,0x00,sizeof(char)*strlen(s)+1);
```
![](https://i.imgur.com/QP6vHbN.png)


