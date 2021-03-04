# Problem：https://www.nowcoder.com/practice/e3769a5f49894d49b871c09cadd13a61?tab=answerKey

### this指针

我感觉其实不用this指针，但是保险起见，以后还是要记得加

## Code

本题其实是想设计数据结构：使用双向链表，方便元素的插入，删除和移动。
有题解在双向链表的基础上使用unordered_map进行key，val的哈希，方便查找。但我STL用的不是很熟，并且在数据结构能够进行$O(N)$的查找的情况下感觉不是很需要

```c++
#include<iostream>
#include<queue>
#include<cstdlib>
#include <vector>
using namespace std;


struct DListNode{
    int key, val;
    DListNode* pre;
    DListNode* next;
    DListNode(int k, int v): key(k), val(v), pre(nullptr), next(nullptr){};
};


class Solution {
public:
    /**
     * lru design
     * @param operators int整型vector<vector<>> the ops
     * @param k int整型 the k
     * @return int整型vector
     */
    int currSize = 0;
    int maxSize = 0;
    DListNode *head;
    DListNode *tail;

    void setElem(int key, int val){
        int flag = findElem(key);
        if(flag == -1){
            if(this->currSize == this->maxSize){
                this->tail->pre->pre->next = this->tail;
                this->tail->pre = this->tail->pre->pre;
            } else{
                this->currSize++;
            }

            auto* temp = new DListNode(key, val);
            this->head->next->pre = temp;
            temp->next = this->head->next;
            this->head->next = temp;
            temp->pre = this->head;
        } else{
            head->next->val = val;
        }
    }

    int findElem(int key){
        int retVal = -1;
        auto *tempHead = this->head->next;

        while (tempHead != this->tail){
            if (tempHead->key == key){
                retVal = tempHead->val;

                tempHead->pre->next = tempHead->next;
                tempHead->next->pre = tempHead->pre;
                head->next->pre = tempHead;
                tempHead->pre = head;
                tempHead->next = head->next;
                head->next = tempHead;

                return retVal;
            }
            tempHead = tempHead->next;
        }
        return retVal;
    }

    vector<int> LRU(vector<vector<int> >& operators, int k) {
        // write code here
        // 初始化
        if(k < 1) return {};

        this->head = new DListNode(0, 0);
        this->tail = new DListNode(0, 0);
        this->head->next = tail;
        this->tail->pre = head;
        this->maxSize = k;

        // 只需要swap就行，最常用，最不常用
        vector<int> retVec;

        for(auto & i : operators){
            if(i[0] == 1){
                setElem(i[1], i[2]);
            } else if (i[0] == 2){
                retVec.push_back(findElem(i[1]));
            }
        }
        return retVec;
    }
};
// 测试
int main(){
    Solution s;
    vector<int> step1;
    vector<int> step2;
    vector<int> step3;
    vector<int> step4;
    vector<int> step5;
    vector<int> step6;
    vector<int> step7;

    step1.push_back(1);step1.push_back(1);step1.push_back(1);
    step2.push_back(1);step2.push_back(2);step2.push_back(2);
    step3.push_back(1);step3.push_back(3);step3.push_back(2);
    step4.push_back(2);step4.push_back(1);
    step5.push_back(1);step5.push_back(4);step5.push_back(4);
    step6.push_back(2);step6.push_back(2);

    vector<vector<int>> testVec;
    testVec.push_back(step1);
    testVec.push_back(step2);
    testVec.push_back(step3);
    testVec.push_back(step4);
    testVec.push_back(step5);
    testVec.push_back(step6);

    vector<int> res = s.LRU(testVec, 3);
    cout<< res.size() << endl;
    for(int re : res){
        cout<< re << endl;
    }

    return 0;
}
```

