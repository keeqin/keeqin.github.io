# 20.有效的括号

给定一个只包括 '('，')'，'{'，'}'，'['，']' 的字符串 s ，判断字符串是否有效。

有效字符串需满足：

左括号必须用相同类型的右括号闭合。
左括号必须以正确的顺序闭合。
```clike
typedef struct stackNode{
    char data;
    struct stackNode *next;
}StackNode,*PStackNode;

struct LinkStack{
    PStackNode top;
    int count;
};

bool isValid(char * s){
    struct LinkStack linkStack;
    PStackNode PSN = NULL;
    linkStack.count = 0;
    linkStack.top = NULL;
    while(*s){
        if((*s) == ')'||
           (*s) == ']'||
           (*s) == '}'){
            if(linkStack.top == NULL)
                return 0;
            if(linkStack.top->data != (*s)-1 && linkStack.top->data != (*s)-2){
                return 0;
            }else{
                linkStack.top = linkStack.top->next;
                --linkStack.count;
                ++s;
                continue;
            }
        }
        PSN = malloc(sizeof (StackNode));
        PSN->next = linkStack.top;
        linkStack.top = PSN;
        PSN->data = *s;
        ++(linkStack.count);
        ++s;
    }
    if(linkStack.count == 2){
        if(linkStack.top->data == linkStack.top->next->data +1 ||
            linkStack.top->data == linkStack.top->next->data +2
        )
        return 1;
        else 
        return 0;
    }else if(linkStack.count == 0){
        return 1;
    }else{
        return 0;
    }
}
```
用链式存储的方式构建一个栈，然后在while循环中判断字符指针指向的字符，如果是右括号的情况下栈为空则返回false，栈顶第一个字符不是它的反括号也返回false，如果是就出栈。如果是左括号的情况下直接进栈。遍历完字符串之后，如果栈中刚好有两个元素且能匹配上则返回true，否则返回false，如果栈为空则返回true，否则返回false。
