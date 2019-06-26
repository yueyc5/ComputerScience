[494. 双队列实现栈](#https://www.lintcode.com/problem/implement-stack-by-two-queues/description)  
利用两个队列来实现一个栈的功能

样例
例1:

输入：
push(1)
pop()
push(2)
isEmpty() // return false
top() // return 2
pop()
isEmpty() // return true
例2:

输入：
isEmpty()

```c++
class Stack {
public:
    queue<int> main;
    queue<int> help;
    /** Initialize your data structure here. */
    Stack() {
        
    }
    
    /** Push element x onto stack. */
    void push(int x) {
        main.push(x);
    }
    
    /** Removes the element on top of the stack and returns that element. */
    int pop() {
      //
        int len = main.size();
        int res = 0;
        for(int i = 0; i < len -1 ; i++)
        {
           res = main.front();
            main.pop();
            help.push(res);
        }
        res = main.front();
        main.pop();
        queue<int> t = main;
        main = help;
        help = t;//这里需要注意的问题是我们每次操作都需要将辅助栈清空，否则会出错
        return res;
    }
    
    /** Get the top element. */
    int top() {
        int len = main.size();
        int res = 0;
        for(int i = 0; i < len ; i++)
        {
           res = main.front();
            main.pop();
            help.push(res);
        }
        queue<int> t = main;
        main = help;
        help = t;//清空辅助栈
        return res;
    }
    
    /** Returns whether the stack is empty. */
    bool isEmpty() {
        return main.empty();
    }
};

/**
 * Your MyStack object will be instantiated and called as such:
 * MyStack* obj = new MyStack();
 * obj->push(x);
 * int param_2 = obj->pop();
 * int param_3 = obj->top();
 * bool param_4 = obj->empty();
 */
```