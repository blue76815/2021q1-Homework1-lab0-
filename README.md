# 2021q1 Homework1 (lab0)
###### tags: `linux2021`
<style>
.blue {
  color: blue; 
}
.red {
  color: red;
}
</style>

## 作業要求

### 1. 開發環境架設
- [x] 安裝 Ubuntu Linux 20.04-LTS(實體安裝 不是用VM虛擬機)
- [x] 安裝 VS code
- [x] 安裝 Cppcheck
- [x] 安裝 Valgrind
- [x] Github fork lab0-c 專案
- [x] 接觸 [Linux Programming Interface](http://man7.org/tlpi/)
### 2. 實做 `queue.c` 和 `queue.h` 功能
- [x]  `q_new`: 建立新的「空」佇列;
- [x]  `q_free`: 釋放佇列所佔用的記憶體;
- [x]  `q_insert_head`: 在佇列開頭 (head) 加入 (insert) 給定的新元素 (以 LIFO 準則);
- [x]  `q_insert_tail`: 在佇列尾端 (tail) 加入 (insert) 給定的新元素 (以 FIFO 準則);
- [x]  `q_remove_head`: 在佇列開頭 (head) 移去 (remove) 給定的元素。
- [x]  `q_size`: 計算佇列中的元素數量。
- [x]  `q_reverse`: 以反向順序重新排列鏈結串列，該函式不該配置或釋放任何鏈結串列元素，換言之，它只能重排已經存在的元素;
- [x]  ==`q_sort`==: 「[Linux 核心設計](http://wiki.csie.ncku.edu.tw/linux/schedule)」課程所新增，以==遞增順序==來排序鏈結串列的元素，可參閱 [Linked List Sort](https://npes87184.github.io/2015-09-12-linkedListSort/) 得知實作手法;


---
- [ ] 3. 開啟 [Address Sanitizer](https://github.com/google/sanitizers/wiki/AddressSanitizer)，修正 `qtest` 執行過程中的錯誤
 
- [ ] 4. 運用 Valgrind 排除 `qtest` 實作的記憶體錯誤，並透過 Massif 視覺化 "simulation" 過程中的記憶體使用量

- [ ] 5. 在 `qtest` 中實作 [coroutine](https://en.wikipedia.org/wiki/Coroutine)，並提供新的命令 `web`，提供 web 伺服器功能

- [ ] 6. 解釋 [select](http://man7.org/linux/man-pages/man2/select.2.html) 系統呼叫在本程式的使用方式，並分析 [console.c](https://github.com/sysprog21/lab0-c/blob/master/console.c) 的實作

- [ ] 7. 說明 [antirez/linenoise](https://github.com/antirez/linenoise) 的運作原理，注意到 [termios](http://man7.org/linux/man-pages/man3/termios.3.html) 的運用

- [ ] 8. 研讀論文 [Dude, is my code constant time?](https://eprint.iacr.org/2016/1123.pdf)，解釋本程式的 "simulation" 模式是如何透過以實驗而非理論分析

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
## 實做 `queue.c` 和 `queue.h` 功能
### queue_t 結構
修改 `queue.h` 加入 `int size`

```clike=26
typedef struct {
    list_ele_t *head; /* Linked list of elements */
    list_ele_t *tail;
    int size;    
} queue_t;
```
### q_size

```clike=166
int q_size(queue_t *q)
{
    /* TODO: You need to write the code for this function */
    /* Remember: It should operate in O(1) time */
    /* TODO: Remove the above comment when you are about to implement. */
    if (!q || !q->head)
        return 0;
    return q->size;
}
```

### q_new

```clike=13
queue_t *q_new()
{
    queue_t *q = malloc(sizeof(queue_t));
    /* TODO: What if malloc returned NULL? */
    if (!q) {
        return NULL;
    }
    q->head = NULL;
    q->tail = NULL;
    q->size = 0;
    return q;
}
```

### q_free

```clike=27
void q_free(queue_t *q)
{
    /* TODO: How about freeing the list elements and the strings? */
    /* Free queue structure */
    if (!q) //先檢查queue是否存在
        return;

    list_ele_t *Node;
    list_ele_t *Next_Node;
    Node = q->head;//step1.先從 quene的 q->head取得第一個節點的位址
    while (Node) { //step2.檢查取得的節點位址是否為null(最後一個節點位址)
        free(Node->value);//step3.釋放舊節點的字串資料
        Next_Node = Node->next;//step4.先從原來節點(Node->next)  備份next新節點位址(Next_Node)
        free(Node);//step5.釋放掉舊節點
        Node = Next_Node;//step6.更新成下一個節點位址
    }
    free(q);
}
```

### q_insert_head

在字串複製時,我使用 memset(), 
也能達成 strncpy() 的功能

```clike=53
bool q_insert_head(queue_t *q, char *s)
{
    if (q == NULL) //先檢查queue是否存在
        return false;
    /* TODO: What should you do if the q is NULL? */
    list_ele_t *newh = malloc(sizeof(list_ele_t));
    if (!newh)
        return false;
    /* Don't forget to allocate space for the string and copy it */
    /* What if either call to malloc returns NULL? */
    newh->value = (char *) malloc(sizeof(char) * strlen(s) + 1);
    if (!newh->value) {
        free(newh);
        return false;
    }
    memset(newh->value, 0x00, sizeof(char) * strlen(s) + 1);
    memcpy(newh->value, s, sizeof(char) * strlen(s));

    if (q->size == 0) {
        newh->next = NULL;//建立第1個節點時  節點next位址為null沒有新節點位址
        q->head = newh;
        q->tail = newh;
    } else {
        newh->next = q->head;//先從quene的head中 取到原來head位址(q->head）存到新節點（newh）的next成員中
        q->head = newh;//quene中的head （q->head )==>換成新節點newh 位址
    }
    q->size++;
    return true;
}
```

### q_insert_tail

```clike=92
bool q_insert_tail(queue_t *q, char *s)
{
    if (q == NULL)
        return false;
    list_ele_t *new_node;
    /* TODO: You need to write the complete code for this function */
    /* Remember: It should operate in O(1) time */
    /* TODO: Remove the above comment when you are about to implement. */
    new_node = malloc(sizeof(list_ele_t));
    if (!new_node)
        return false;

    new_node->value = malloc(sizeof(char) * strlen(s) + 1);
    if (!new_node->value) {
        free(new_node);
        return false;
    }
    new_node->next = NULL;
    memset(new_node->value, 0x00, sizeof(char) * strlen(s) + 1);
    memcpy(new_node->value, s, sizeof(char) * strlen(s));

    if (q->size == 0) {
        q->head = new_node;
        q->tail = new_node;
    } else {
        q->tail->next = new_node;//還沒覆蓋掉的舊tail節點 的next填上 新節點位址 new_node
        q->tail = new_node;//最後q->tail再填上 新節點位址 new_node
    }
    q->size++;
    return true;
}
```

### q_remove_head
沒加  `free(remove_head->value);` 
則節點全移完後 下次再 new 會出現    
```
cmd> new
Freeing old queue
ERROR: Attempted to free unallocated block.  Address = 0x5555555555555555
匯流排錯誤 (核心已傾印)
```

```clike=132
bool q_remove_head(queue_t *q, char *sp, size_t bufsize)
{
    /* TODO: You need to fix up this code. */
    /* TODO: Remove the above comment when you are about to implement. */
    if (q == NULL || sp == NULL || q->head == NULL || q->size == 0)
        return false;

    size_t value_size = strlen(q->head->value);
    memset(sp, 0x00, bufsize);
    if (bufsize <= value_size) // 若輸入的 bufsize 小於head節點字串長度（value_size）
        memcpy(sp, q->head->value, bufsize - 1);
    else
        memcpy(sp, q->head->value, value_size);//若輸入的 bufsize超過  配置的 value_size 則只能填入 value_size 容量資料

    list_ele_t *remove_head;
    remove_head = q->head;
    /*head位址 換成next節點位址*/
    q->size--;
    if (q->size == 0) { //剩最後一個節點後 則 head和 tail  位址都要填 NULL
        q->head = NULL;
        q->tail = NULL;
    } else {
        q->head = q->head->next;
    }
    free(remove_head->value);
    free(remove_head);
    return true;
}
```


### q_reverse
q->size 可用在

計次 for 迴圈的 index 索引長度

```clike=184
void q_reverse(queue_t *q)
{
    /* TODO: You need to write the code for this function */
    /* TODO: Remove the above comment when you are about to implement. */
    if (!q || !q->head || q->size == 1)
        return;
    list_ele_t *Node = q->head;
    list_ele_t *Old_Node;
    list_ele_t *Next_Node = q->head->next;
    for (int i = 1; i <= (q->size); i++) { 
        if (i == 1) {
            Node->next = NULL;
            q->tail = Node;//tail節點位址 改成 Node-addr
        } else if (i == q->size) {
            Node->next = Old_Node;
            q->head = Node;
            return;
        } else {
            Node->next = Old_Node;
        }
        Old_Node = Node;
        Node = Next_Node;
        Next_Node = Next_Node->next;
    }
}
```

## To do list
 - [x] 要附上 strlen(const char str) 規格書說明

在終端機中 輸入 `man strlen` 查詢

或是用 [Linux Programming Interface 查詢 strlen](https://man7.org/linux/man-pages/man3/strlen.3.html)
有提到

> ## STRLEN(3)   Linux Programmer's Manual  
> 
> ### NAME
>        strlen - calculate the length of a string
> 
> ### SYNOPSIS
> ```
>         #include <string.h>
>  
>         size_t strlen(const char *s);
> ```
> 
> ### DESCRIPTION
> The strlen() function calculates the length of the string pointed to by s, <span class="red">**excluding the terminating null byte ('\0').**</span>
> 
> ### RETURN VALUE
> The strlen() function returns the number of bytes in the string pointed to by s.

strlen() 計算字元個數到 <span class="red">**null byte ('\0')**</span> 才停止

由於 strlen(const char str) 計算字串 str 的長度時，<span class="red">**str 字串資料不包括終止空字符 (NULL)。**</span>


因此在 `q_insert_head(queue_t *q, char *s)`
和 `q_insert_tail(queue_t *q, char *s)` 中

節點內配置新字串空間個數時,為 strlen(s)<span class="red">**+1**</span> 個

多加一格,是為了<span class="blue">**在字串結尾多填一格 NULL 值**</span>

<span class="blue">**避免 strlen(const char str) 計算字串長度時,把字尾相鄰的記憶體殘值也列入字元個數**</span>
```
newh->value=malloc(sizeof(char)*strlen(s)+1);
memset(newh->value,0x00,sizeof(char)*strlen(s)+1);
```
![](https://i.imgur.com/QP6vHbN.png)


### q_sort()
[Comparison Sort: Merge Sort(合併排序法)](https://alrightchiu.github.io/SecondRound/comparison-sort-merge-sorthe-bing-pai-xu-fa.html)

[合併排序法](https://openhome.cc/Gossip/AlgorithmGossip/MergeSort.htm)

參照 [Merge two sorted linked lists](https://www.geeksforgeeks.org/merge-two-sorted-linked-lists/) 的寫法

由於 lab0 作業的 link list 節點資料為字串
因此比較時改為 `strcmp(a->value, b->value)`

```clike=216
void MoveNode(list_ele_t **destRef, list_ele_t **sourceRef)
{
    /* the front source node  */
    list_ele_t *newNode = *sourceRef;
    assert(newNode != NULL);

    /* Advance the source pointer */
    *sourceRef = newNode->next;

    /* Link the old dest off the new node */
    newNode->next = *destRef;

    /* Move dest to point to the new node */
    *destRef = newNode;
}

list_ele_t *sorted_merge(list_ele_t *a, list_ele_t *b)
{
    // a dummy first node to hang the result on
    list_ele_t dummy;
    // tail points to the last result node
    list_ele_t *tail = &dummy;

    dummy.next = NULL;

    while (1) {
        if (a == NULL) {
            tail->next = b;
            break;
        } else if (b == NULL) {
            tail->next = a;
            break;
        }
        if (strcmp(a->value, b->value) < 0) {
            MoveNode(&(tail->next), &a);
        } else {
            MoveNode(&(tail->next), &b);
        }

        tail = tail->next;
    }
    return (dummy.next);
}

void front_back_split(list_ele_t *head,
                      list_ele_t **front_ref,
                      list_ele_t **back_ref)
{
    // if length is less than 2
    if (head == NULL || head->next == NULL) {
        *front_ref = head;
        *back_ref = NULL;
        return;
    }

    list_ele_t *slow = head;
    list_ele_t *fast = head->next;

    while (fast != NULL) {
        fast = fast->next;
        if (fast != NULL) {
            slow = slow->next;
            fast = fast->next;
        }
    }
    *front_ref = head;
    *back_ref = slow->next;
    slow->next = NULL;
}
void merge_sort(list_ele_t **head)
{
    if (*head == NULL || (*head)->next == NULL)
        return;

    list_ele_t *a;
    list_ele_t *b;

    front_back_split(*head, &a, &b);

    merge_sort(&a);
    merge_sort(&b);

    *head = sorted_merge(a, b);
}

void q_sort(queue_t *q)
{
    /* TODO: You need to write the code for this function */
    /* TODO: Remove the above comment when you are about to implement. */
    if (q == NULL || q->head == NULL)
        return;

    merge_sort(&q->head);

    list_ele_t *tmp_node = q->head;
    while (tmp_node->next) {
        tmp_node = tmp_node->next;
    }
    q->tail = tmp_node;
}
```