[495. 实现栈](#https://www.lintcode.com/problem/implement-stack/description)  
实现一个栈，可以使用除了栈之外的数据结构
样例
例1:

输入：
push(1)  

pop()
  
  push(2)
  
  top()  // return 2
  
  pop()
  
  isEmpty() // return true
  
  push(3)
  
  isEmpty() // return false
例2:

输入：
isEmpty()

```c++
class Stack {
public:
    /*
     * @param x: An integer
     * @return: nothing
     */
     std::deque<int> d ;
    void push(int x) {
        // write your code here
        d.push_back(x);
    }

    /*
     * @return: nothing
     */
    void pop() {
        // write your code here
        d.pop_back();
    }

    /*
     * @return: An integer
     */
    int top() {
        // write your code here
        return d.back();
    }

    /*
     * @return: True if the stack is empty
     */
    bool isEmpty() {
    
    return d.empty();
        // write your code here
    }
};
```