# 2.两数相加
给你两个非空的链表，表示两个非负的整数。它们每位数字都是按照逆序的方式存储的，并且每个节点只能存储一位数字。 

请你将两个数相加，并以相同形式返回一个表示和的链表。 

你可以假设除了数字 0 之外，这两个数都不会以 0 开头。

```cddd
struct ListNode* addTwoNumbers(struct ListNode* l1, struct ListNode* l2){
    struct ListNode *head = NULL,*tail = NULL;
    int carry = 0;
    int sum,sum1,sum2;
    while(l1 || l2){
        sum1 = l1?l1->val:0;
        sum2 = l2?l2->val:0;
        if(l1)
            l1 = l1->next;
        if(l2)
            l2 = l2->next;
        sum = sum1 + sum2 + carry;
        if(!head){//第一次进入循环时，初始化头节点并存数
            head = tail = malloc(sizeof(struct ListNode));
            tail->next = NULL;
            tail->val = sum%10;
        }else{
            if(carry){ //前面有进位的话就什么也不用干，如果不要这个if判断，可能会导致内存泄漏
            }else{
                tail->next = malloc(sizeof(struct ListNode));
            }
            tail = tail->next;
            tail->next = NULL;
            tail->val = sum%10;
        }
        carry = sum / 10;
        if(carry > 0){//如果有进位，就分配存储空间把进位值放进去
            tail->next = malloc(sizeof(struct ListNode));
            tail->next->val = carry;
            //设置为尾节点，防止两个链表循环结束，我们自己的链表没设置结束
            tail->next->next = NULL;
        }
    }
    return head;
}
```
给定的两个链表都是逆序存储的，因此只需要从第一个节点开始把对应位置的数加起来就行。对于长度不相等的两个链表，
可以把短出的那部分看成0。初始时进位值为0，讲两个链表对应的数和进位值加起来，然后跟10取余数存到新链表里，再跟
10取除数存到进位变量里，接着对两个链表的下一个对应位置的数继续与进位值求和，跟前面的操作一样，如此循环直到加完所有数。
最终返回头节点。

# 19. 删除链表的倒数第N给个节点
给你一个链表，删除链表的倒数第n个结点，并且返回链表的头结点。

```clike
struct ListNode* removeNthFromEnd(struct ListNode* head, int n){
    int i = 0;
    struct ListNode* node = head;
    while(node){
        ++i;
        node = node->next;
    }
    i = i-n-1;
    node = head;
    while(i > 0){
        node = node->next;
        --i;
    }
    struct ListNode* temp;
    if(i < 0){ 
        temp = head;
        head = head->next;
    }else{
        temp = node->next;
        node->next = node->next->next;
    }
    free(temp);
    return head;
}
```
    看到这个题目首先想到的是进行两次循环，第一次循环获取链表的长度，第二次循环用链表长度len减去倒数的个数n再减1找到要删除
    节点的上一个节点，然后进行删除。

    进阶的做法是，使用双指针，第一个指针先走n+1步，然后第二个指针开始走。当第一个指针走到末端时，第二个指针恰好指在要
    删除节点的上一个节点，然后进行删除操作。如果第一个指针已经遍历的最后一个，第二个指针还没开始遍历，则说明要删除的是
    头节点。程序如下：

```clike
struct ListNode* removeNthFromEnd(struct ListNode* head, int n){
    struct ListNode* node1 = head;
    struct ListNode* node2 = head;
    while(n>0){
        node1 = node1->next;
        --n;
    }
    if(!node1){//node1已经遍历结束了，说明要删除的是第一个
        node2 = node2->next;
        free(head);
        head = node2;
    }else{//node1还没遍历完
        node1 = node1->next; //node1提前走n+1步，好让node2指向要删除节点的前一个节点
        while(node1){
            node2 = node2->next;
            node1 =node1->next;
        }
        struct ListNode* temp;
        temp = node2->next;
        node2->next = node2->next->next;
        free(temp);
    }
    return head;
}
```
# 21.合并两个有序链表
将两个升序链表合并为一个新的 升序 链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。 

```clike
struct ListNode* mergeTwoLists(struct ListNode* l1, struct ListNode* l2){
    if(!l1){
        return l2;
    }
    if(!l2){
        return l1;
    }
    if(l1->val < l2->val){
        l1->next = mergeTwoLists(l1->next,l2);
        return l1;
    }else{
        l2->next = mergeTwoLists(l1,l2->next);
        return l2;
    }
}
```
一开始没想到用递归的方法，但是为啥要把递归的方法放到上面，是因为递归法函数更简洁更不容易想到,希望自己下次遇到类似题目可以首先想到递归。
下面的方法是改进后的迭代法，本来有40行，后来被我缩减到25行。题目很简单没啥说的。

```clike
struct ListNode* mergeTwoLists(struct ListNode* l1, struct ListNode* l2){
    struct ListNode head ;
    struct ListNode *temp = &head;
    if(!l2){
        temp->next = l1;
    }else if(!l1){
        temp->next = l2;
    }else if(l1 && l2){
        while(l1 && l2){
            if(l1->val > l2->val){
                temp->next = l2;
                l2 = l2->next;
            }else{
                temp->next = l1;
                l1 = l1->next;
            }
            temp = temp->next;
        }
        if(l1){
                temp->next = l1;
        }else if(l2){
                temp->next = l2;
        }
    }
    return head.next;
}
```

# 24.两两交换链表中的节点
给定一个链表，两两交换其中相邻的节点，并返回交换后的链表。

你不能只是单纯的改变节点内部的值，而是需要实际的进行节点交换。
```clike
struct ListNode* swapPairs(struct ListNode* head){
    if(!head) return NULL;
    if(!(head->next)) return head;
    struct ListNode* p1 = head,*p2 = head->next;
    struct ListNode* temp = p2->next;
    p2->next = p1;
    p1->next = swapPairs(temp);
    return p2;
}
```
类似与21题的递归法，递归函数中先将头两个节点进行交换，返回排在前面那个节点，后一个节点的next节点就是下一个递归函数的返回值。
退出条件为当前节点为NULL或者当前节点的next节点为NULL。

# 61.旋转链表
给你一个链表的头节点head，旋转链表，将链表每个节点向右移动k个位置。
```clike
struct ListNode* rotateRight1(struct ListNode* head){
    struct ListNode* first,*last,*prelast;
    if(!head) return NULL;
    first = head;
    prelast = head;
    last = head->next;
    if(!last) return first;
    while(last->next){
        last = last->next;
        prelast = prelast->next;
    }
    if(first == last) return first;
    last->next = first;
    prelast->next = NULL;
    return last;
}

struct ListNode* rotateRight(struct ListNode* head, int k){
    struct ListNode* node = head;
    int len = 0;
    while(head){
        head = head->next;
        ++len;
    }
    if(len == 0) return NULL;
    k = k % len;
    while(k){
        node = rotateRight1(node);
        --k;
    }
    return node;
}
```
根据这道题目的描述，首先想到的是写一个能把链表右旋一位的函数rotateRight1，然后根据要移的位数来决定调用几次这个函数。很快写出来之后查看解析，发现解析用了
一个很巧妙的办法。具体方法是先算出旋转之后的链表将在哪里断开，然后断开这里再把链表首位相接就完成了。这样节省了很多时间。上面的方法时间复杂度为O(n²)，下面
的方法时间复杂的为O(n)。
```clike
struct ListNode* rotateRight(struct ListNode* head, int k){
    if(k==0 || head == NULL || head->next == NULL)
        return head;
    struct ListNode* node = head,*node2,*node3;
    int len = 0;
    while(node){
        node3 = node;
        node = node->next;
        ++len;
    }
    k = k % len;
    k = len -k;
    if(k == len) return head;
    node2 = head;
    while(--k){
        node2 = node2->next;//node2移动到断开处的前面那个节点
    }
    node = node2->next; //此时node就是新链表的头节点
    node2->next = NULL;//此时node2是新链表的尾节点
    node3->next = head;//将原链表首位相接
    return node;
}
```

# 83.删除排序链表中的重复元素
存在一个按升序排列的链表，给你这个链表的头节点head，请你删除所有重复的元素，使每个元素只出现一次 。

返回同样按升序排列的结果链表。
```clike
struct ListNode* deleteDuplicates(struct ListNode* head){
    if(head == NULL || head->next == NULL)
        return head;
    struct ListNode* node = head;
    struct ListNode* temp = NULL;
    while(node->next){
            if(node->val == node->next->val){
                temp = node->next->next;
                free(node->next);
                node->next = temp;
            }else{
                node = node->next;
            }
    }
    return head;
}
```
从头节点开始，用当前节点node1与下一个节node2点比较，如果val相等，则用node1与下下一个节点比较，并释放node2内存。一次类推，直到发现一个val值与node1不想等
的节点，让node1的next的指针指向这个节点，然后再把这个节点当作node1用相同的方法比较，直到结束。

# 82. 删除排序链表中的重复元素II
存在一个按升序排列的链表，给你这个链表的头节点head，请你删除链表中所有存在数字重复情况的节点，只保留原始链表中没有重复出现的数字。

返回同样按升序排列的结果链表
```clike
struct ListNode* deleteDuplicates(struct ListNode* head){
    if(head == NULL || head->next == NULL)
        return head;
    struct ListNode phead;
    phead.next = head;  
    struct ListNode* cur = &phead;
    int val = 200;
    while(cur->next && cur->next->next){
        if(cur->next->val == cur->next->next->val){
            val = cur->next->val;
            while(cur->next&& cur->next->val == val){
                cur->next = cur->next->next;
            }
        }else{
                cur = cur->next;    
        }
    }
    return phead.next;
}
```
原本的想法是两个节点两个节点的进行判断，如果这两个节点的值相等，就让一个中间指针 temp指向后一个节点的下一个节点，把相等的值赋给一个变量val,然后释放这两个节点的内存。接着判断temp指向的节点的值是否与val相等，不想等的话就判断就与下一个节点判断相不相等，相等的话就让中间指针指向后一个节点点下一个节点，把相等的值赋给一个变量val，如此循环。如果不相等，就让一个指针指向前面哪个节点......实在说不下去了，太复杂了。

看来leetcode官方的解说后，发现我并没有理清思路，很混乱以至于表述思路时都说不清楚。官方的思路是这样：定义一个实体节点，该节点的next指针最终指向要返回的链表的头节点。再定义一个链表指针cur并指向实体节点，用该指针遍历整个链表。比较当前节点的值和下一个节点的值，如果相等则保持这个值，cur->next指向下一个节点然后比较cur->next指向的值是否与前面的值相等，如果相等就继续往后遍历，直到发下一个不相等的节点，然后从这个不相等的节点开始与下一个节点比较重复开头的步骤。如果不相等cur就指向下一个节点。直到遍历结束。

# 86.分隔链表
给你一个链表的头节点head和一个特定值x，请你对链表进行分隔，使得所有小于x的节点都出现在大于或等于x的节点之前。

你应当保留两个分区中每个节点的初始相对位置
```clike
struct ListNode* partition(struct ListNode* head, int x){
    if(!head || !(head->next)) return head;
    struct ListNode* p1,* p2,*p;
    struct ListNode large,small;
    p = head;
    p1 = &large;
    p2 = &small;
    while(p){
        if(p->val < x){
            p2->next = p;
            p2 = p2->next;
        }else{
            p1->next = p;
            p1 = p1->next;
        }
        p = p->next;
    }
    p1->next = NULL;
    p2->next = large.next;
    return small.next;
}
```
只需要维护两个链表，一个链表存小于x的节点，另一个链表存大于等于x的节点，遍历结束后把两个链表拼起来，并把大于等于x的链表的最后一个节点的next置空。

*笨比(我)的想法：*
要定义两个指针，一个指针p1用于发现大于特定值的节点，另一个指针p2用于发现小于特定知道节点。p1从第一个节点开始遍历，直到发现p1->next的值大于等于特定值，然后p2从p1的位置开始遍历，直到发现p2-next小于特定值的节点。此时定义两个个中间指针temp，temp2，temp指向p1->next。p1->next指向p2，temp2指向p2->next->next，p2->next->next指向temp，p2->next指向temp2。

# 206.反转链表
给你单链表的头节点head请你反转链表，并返回反转后的链表。
```clike
struct ListNode* reverseList(struct ListNode* head){
    if(!head || !(head->next)) return head;
    struct ListNode node;
    struct ListNode* p1,*p2,*temp;
     p2 = head;
     p1 = head->next;
    while(p1){
        node.next = p1;
        temp = p1->next;
        p1->next = p2;
        if(p2 == head)
            p2->next = NULL;
        p2= node.next;
        p1 = temp;
    }
    return node.next;
 }
```