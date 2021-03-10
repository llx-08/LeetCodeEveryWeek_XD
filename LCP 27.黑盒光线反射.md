Link：https://leetcode-cn.com/problems/IQvJ9i/

[原题地址]:



### Code

不知道为什么过不去，总是在报很奇怪的错误，我自己测试时没有问题的。
有空看看题解
我的思路就是在创建BlackBox对象的时候就把它不同方向的下一个点计算出。在open时只需要不停的查看下一个点直到找到open的即可。

```c++
class BlackBox {
public:
    struct node{
//        int rank;
        bool isOpen;
        int nextPoint_x;
        int nextPoint_reverseX;
    };
    int high;
    int wide;
    int pointNum;
    vector<node> openRecVec;

    BlackBox(int n, int m) {
        this->high = n;
        this->wide = m;
        this->pointNum = 2*(m+n);

        int leftMargin   ;
        int rightMargin  ;
        int topMargin    ;
        int bottomMargin ;

        for(int i = 0 ; i < this->pointNum ; i++){
            node tempNode{};
//            tempNode.rank = i;
            tempNode.isOpen = false;

            if (i >= 0 and i < m){
                leftMargin   = i;
                rightMargin  = m-i;
                topMargin    = 0;
                bottomMargin = n;

                tempNode.nextPoint_x        = i - 2*leftMargin + 2*(m+n);
                tempNode.nextPoint_reverseX = i + 2*rightMargin;

            } else if (i >= m and i < m+n){
                leftMargin   = m;
                rightMargin  = 0;
                topMargin    = i - m;
                bottomMargin = m+n-i;

                tempNode.nextPoint_x        = i + 2*bottomMargin;
                tempNode.nextPoint_reverseX = i - 2*topMargin;
            } else if (i >= m+n and i < 2*m+n) {
                leftMargin   = 2*m + n - i;
                rightMargin  = i - m - n;
                topMargin    = n;
                bottomMargin = 0;

                tempNode.nextPoint_x        = i - 2*rightMargin;
                tempNode.nextPoint_reverseX = i + 2*leftMargin;
            } else {
                leftMargin   = 0;
                rightMargin  = n;
                topMargin    = 2*(m+n) - i;
                bottomMargin = i - (2*m+n);

//                cout << "bot " << bottomMargin << endl;
                tempNode.nextPoint_x        = i + 2*topMargin - 2*(m+n);
                tempNode.nextPoint_reverseX = i - 2*bottomMargin;
            }

            if (tempNode.nextPoint_reverseX >= 2*(m+n))
                tempNode.nextPoint_reverseX -= 2*(m+n);

            if (tempNode.nextPoint_x >= 2*(m+n))
                tempNode.nextPoint_x -= 2*(m+n);

            if (tempNode.nextPoint_reverseX <= 0)
                tempNode.nextPoint_reverseX += 2*(m+n);
            if (tempNode.nextPoint_x <= 0)
                tempNode.nextPoint_x += 2*(m+n);

//            if (i == 0 or i == m+n)
//                tempNode.nextPoint_x = -1;
//
//            if (i == m or i == 2*m + n)
//                tempNode.nextPoint_reverseX = -1;
            this->openRecVec.push_back(tempNode);

            cout <<"Num "<< i << "pointCheck: x&-x " << tempNode.nextPoint_x << " " << tempNode.nextPoint_reverseX << endl;
        }
    }

    int open(int index, int direction) {
        cout << index << " opening" << endl;
        if(!this->openRecVec[index].isOpen)
            this->openRecVec[index].isOpen = true;

        int nextPoint = index;

        while (nextPoint != -1) {
            cout << "nextPoint&direction : " << nextPoint << " " << direction << endl;

            if (direction == 1){
                nextPoint = this->openRecVec[nextPoint].nextPoint_x;
            } else {
                nextPoint = this->openRecVec[nextPoint].nextPoint_reverseX;
            }

            if (nextPoint != 0 and nextPoint != this->wide
                               and nextPoint != this->wide+this->high
                               and nextPoint != 2*this->wide+this->high )
                direction = -direction;

            if (openRecVec[nextPoint].isOpen)
                return nextPoint;
        }

        return -1;
    }

    void close(int index) {
        this->openRecVec[index].isOpen = false;
    }
};

int main(){
    auto* obj = new BlackBox(2, 3);
    int param_1 = obj->open(6,-1);
    int param_2 = obj->open(4,-1);
    int param_3 = obj->open(0,-1);
    obj->close(6);
    int param_4 = obj->open(0,-1);
//    int param_5 = obj->open(1,-1);
//    obj->close(1);
//    int param_6 = obj->open(6,1);
//    obj->close(5);
    cout << "res: " << endl
            << param_1 << endl
            << param_2 << endl
            << param_3 << endl
            << param_4 << endl;
//            << param_5 << endl
//            << param_6 << endl;
}
```

参考题解

```c++
class BlackBox {
private:
    int n, m, cnt;

    //辅助数组
    vector<int> group;              //每个index对应的组（线路）的序号
    vector<int> seq;                //每个index在自己组里的序号
    vector<int> direct;             //每个index在自己组里正序时的光线方向
    vector<vector<int>> table;      //由组号和组内的序号反查index的表
    void record(int index, int g, int s, int d) {
        group[index] = g;
        seq[index] = s;
        direct[index] = d;
        table[g].push_back(index);
    }

    //核心数组
    vector<set<int>> T;             //用于快速找下一个出口
   
    //辅助函数
    int getNext(int index, int d) {
        if (d == 1) return ((cnt - index) % cnt + cnt) % cnt;
        else return ((2 * m - index) % cnt + cnt) % cnt;
    }

    //生成线路
    void generate(int start, int d) {
        if (group[start] != -1) return;
        int g = T.size(), index = start, s = 0;     //g取T.size()，每生成一条线路，序号加1
        T.push_back({});
        table.push_back({});
        record(index, g, s, d);
        while (true) {
            index = getNext(index, d);
            if (index == start) return;
            s++;
            d = (d == 1 ? -1 : 1);
            record(index, g, s, d);
            if (index == 0 || index == m || index == m+n || index == 2*m+n) {
                return;
            }
        }
    }

public:
    BlackBox(int n, int m) : n(n), m(m), cnt(2*(m+n)) {
        group = seq = direct = vector<int>(cnt, -1);

        //生成包含顶点的两条线路，注意只会生成两条。因为是最先生成的，所以group值为0和1，下面会用到
        generate(0, -1);
        generate(m, 1);
        generate(m+n, -1);
        generate(2*m+n, 1);
    }
    
    int open(int index, int direction) {
        generate(index, 1);                             //在需要的时候生成，没必要在初始化时生成所有线路
        int g = group[index];                           //获取组号
        T[g].insert(seq[index]);                        //添加开孔
        auto it = T[g].find(seq[index]);
        if (direction == direct[index]) {               //正向搜索
            it = next(it);
            if (it == T[g].end()) {
                if (g == 0 || g == 1) return index;     //如果是包含顶点的线路，直接折返
                it = T[g].begin();                      //否则循环到开头
            }
        } else {                                        //反向搜索
            if (it == T[g].begin()) {
                if (g == 0 || g == 1) return index;     //如果是包含顶点的线路，直接折返
                it = prev(T[g].end());                  //否则循环到结尾
            } else {
                it = prev(it);
            }
        }
        return table[g][*it];
    }
    
    void close(int index) {
        T[group[index]].erase(seq[index]);
    }
};
```

