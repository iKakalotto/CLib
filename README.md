#### 1、链表

> list.h，Linux 内核链表，是一个双向循环链表

~~~c
#include <stdio.h>
#include <stdlib.h>
#include <list.h>

typedef struct node_e {
    int value;
    list_head_t list;
} node_t;

int main() {
    list_head_t head;
    node_t* item;
    node_t* tmp;

    INIT_LIST_HEAD(&head);	// 初始化链表结点
    for (int i = 0; i < 10; i++) {
        item = (node_t *)malloc(sizeof(node_t));
        INIT_LIST_HEAD(&item->list);	// 初始化链表节点
        item->value = i;
        list_add(&item->list, &head);	// 将新节点加入链表
    }

    list_for_each_entry_safe(item, tmp, &head, list) {	// 遍历链表
        if (item != NULL) {
            printf("%d\n", item->value);
            list_del(&item->list);	// 删除节点，有删除操作需
        }
    }

    return 0;
}
~~~

### 2、哈希表

> uthash.h，来源 https://troydhanson.github.io/uthash/

##### 定义结构体

~~~c
typedef struct node_e {
    char* key;
    int value;
    UT_hash_handle hh;	// makes this structure hashable
} node_t;
~~~

##### 声明一个 hash 表

~~~c
node_t* head = NULL;
~~~

##### 新增元素

通过宏定义 `HASH_ADD_XXX()` 新增一个元素到哈希表：

~~~c
HASH_ADD_STR(head, key, elem);	// key 是字符串类型
HASH_ADD_INT(head, key, elem);	// key 是 指针类型
HASH_ADD_INT(head, key, elem);	// key 是 int 类型
~~~

##### 元素获取

~~~c
HASH_ADD_STR(head, key, elem);	// key 是字符串类型
HASH_ADD_INT(head, key, elem);	// key 是 指针类型
HASH_ADD_INT(head, key, elem);	// key 是 int 类型
~~~

##### 查找元素

通过宏定义 `HASH_FIND_XXX()` 从哈希表查找元素：

~~~c
HASH_FIND_STR(head, find_key, item);
HASH_FIND_INT(head, find_key, item);
HASH_FIND_PTR(head, find_key, item)
~~~

通过 `find_key` 查找对应的元素，与新增操作一样，可以通过 key 的类型选择对应的宏。

##### 元素数量

~~~c
HASH_COUNT(head)
~~~

##### 遍历

~~~c
node_t* tmp;
HASH_ITER(hh, head, item, tmp) {
    printf("%s, %d\n", item->key, item->value);
}
~~~

##### 删除元素

~~~c
HASH_DEL(head, item);
~~~

示例代码：

~~~c
#include <uthash.h>

typedef struct node_e {
    char* key;
    int value;
    UT_hash_handle hh;
} node_t;

node_t* head = NULL;

int main() {
    node_t* item;
    node_t tom = {.value = 40, .key = "Tom"};
    node_t jerry = {.value = 39, .key = "Jerry"};

    HASH_ADD_STR(head, key, &tom);
    HASH_ADD_STR(head, key, &jerry);

    char* find_key = "Tom";
    HASH_FIND_STR(head, find_key, item);
    printf("%s, %d\n", item->key, item->value);

    return 0;
}
~~~

### 3、栈

>utstack.h，来源 https://troydhanson.github.io/uthash/
>
>栈（Stack）是一种数据结构，它遵循先进后出（Last In First Out，LIFO）的原则。在栈中，最后插入的元素首先被移除。本文档将介绍如何使用栈进行常见操作。

##### 引用头文件

在使用栈之前，需要包含相应的头文件：

```c
#include <utstack.h>
```

##### 定义结构体

栈中的节点通常由一个自定义的结构体表示：

```c
typedef struct node_e {
    int value;
    struct node_e* next;
} node_t;
```

<span style="color: red">结构体中必须包含 `next` 字段。</span>

##### 初始化栈

要使用栈，需要声明一个指向栈顶的指针。在代码中，`head` 变量即为栈顶指针，并初始化为 `NULL`。

```c
node_t* head = NULL;
```

##### 压入元素

要将元素添加到栈中，可以使用宏定义 `STACK_PUSH()`：

```c
node_t elem;
elem.value = 100;
STACK_PUSH(head, &elem);	// push elem onto stack
```

##### 获取栈中元素数量

要获取栈中元素的数量，可以使用宏定义 `STACK_COUNT()`：

```c
int size;
node_t* tmp;	// A pointer of the same type as elt. Used internally. Need not be initialized.
STACK_COUNT(head, tmp, size);	// store number of elements into count
```

##### 获取栈顶元素

要获取栈中元素的数量，可以使用宏定义 `STACK_TOP()`：

~~~c
printf("the top element is %d.\n", STACK_TOP(head)->value);
~~~

##### 弹出元素

要从栈中移除并返回顶部的元素，可以使用宏定义 `STACK_POP()`：

```c
node_t* result;
while (!STACK_EMPTY(head)) {	// return stack == NULL
    STACK_POP(head, result);	// pop stack and save previous top as element
    printf("pop element: %d.\n", result->value);
}
```

