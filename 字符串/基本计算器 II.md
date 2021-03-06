[原题链接]:https://leetcode-cn.com/problems/basic-calculator-ii/

### Code

先处理掉空格，接着遇到数字则进行转换，遇到符号位进行上一个符号位的运算并更新符号位。

字符串转整数：

```
string s = "458";

int n = 0;
for (int i = 0; i < s.size(); i++) {
    char c = s[i];
    n = 10 * n + (c - '0');
}
```

要注意的是，string->int直接强制类型转换会转换成ascii码，只能通过上述代码转换。
'0'的asc码为48，减掉后才为真实的int值。
这个还是很简单的吧，老套路了。但是即便这么简单，依然有坑：**(c - '0')的这个括号不能省略，否则可能造成整型溢出。**

因为变量c是一个 ASCII 码，如果不加括号就会先加后减，想象一下s如果接近 INT_MAX，就会溢出。所以用括号保证先减后加才行。



```c++
class Solution {// 栈
public:

    int calculate(string s) {
        stack<int>  numStack;
        stack<char> opStack;

        int currNum = 0;
        char sign = '+';

        string s1;

        for (char i : s){
            if (i == ' '){
                cout << " space " << endl;
                continue;
            } else {
                s1.push_back(i);
            }
        }

        for (int i = 0; i < s1.size(); ++i) {

            if (isdigit(s1[i])) {
                currNum = currNum * 10 + (s1[i] - '0');
            }


            if (!isdigit(s1[i]) or i == s1.size() - 1) { // 遇到符号位或结束才会执行
                cout<<"curr: "<<currNum<<" curr sign: " <<sign<<endl;

                switch (sign) {
                    case '+': {
                        numStack.push(currNum);
                        break;
                    }
                    case '-': {
                        cout << s1[i] << " ???" << endl;
                        numStack.push(-currNum);
                        break;
                    }
                    case '*': {
                        cout << "reach *" << endl;
                        int num1 = numStack.top();
                        numStack.pop();
                        numStack.push(num1*currNum);
                        cout << num1 << " " << currNum << endl;
                        break;
                    }
                    case '/': {
                        int num1 = numStack.top();
                        numStack.pop();
                        numStack.push(num1/currNum);
                        break;
                    }

                }
                currNum = 0;
                // 末尾更新运算符，这样保证2个数字对应一个运算符
                sign = s1[i];
            }   // 如果遇到了运算符号或者数字到达了最末尾
                // 1. 把运算符号放入栈中 2. 把数字放入栈中
        }

        int retVal = 0;

        while (!numStack.empty()) {
            cout << "top" << numStack.top() << endl;
            retVal += numStack.top();
            numStack.pop();
        }

        return retVal;
    }
};
```

