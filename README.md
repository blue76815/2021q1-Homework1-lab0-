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

由於 strlen(const char str) 計算字串 str 的長度，<span class="red">**但不包括終止空字符 (NULL)。**</span>


因此在 `q_insert_head(queue_t *q, char *s)`
和 `q_insert_tail(queue_t *q, char *s)` 中

節點內配置新字串空間個數時,為 strlen(s)**+1** 個

多加一格,是為了<span class="blue">**在字串結尾多填一格 NULL 值**</span>

<span class="blue">**避免 strlen(const char str) 計算字串長度時,把字尾相鄰的記憶體殘值也列入字元個數**</span>
```
newh->value=malloc(sizeof(char)*strlen(s)+1);
memset(newh->value,0x00,sizeof(char)*strlen(s)+1);
```
![](https://i.imgur.com/QP6vHbN.png)


[Comparison Sort: Merge Sort(合併排序法)](https://alrightchiu.github.io/SecondRound/comparison-sort-merge-sorthe-bing-pai-xu-fa.html)

[合併排序法](https://openhome.cc/Gossip/AlgorithmGossip/MergeSort.htm)

### q_sort()
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