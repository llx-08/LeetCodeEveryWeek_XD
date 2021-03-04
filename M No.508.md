## M No.508

对每个结点更新一遍，再进行排序求解



```c++
class Solution {
    struct node{
        int value;
        int times;
    };
    vector<node> v;

public:

    static int cmp(node a, node b){
        return a.times >b.times;
    }

    vector<int> findFrequentTreeSum(TreeNode* root) {
        vector<int> returnList;

        if (root == NULL){
            return returnList;
        }
        rebuildTree(root);

        sort(v.begin(), v.end(), cmp);


        returnList.push_back(v[0].value);

        for(int i = 1 ; i < v.size() ; i++){
            if (v[i].times == v[0].times){
                returnList.push_back(v[i].value);
            } else
                break;
        }
        return returnList;
    }

    int rebuildTree(TreeNode* root){
        if (root){
            int leftV = rebuildTree(root->left);
            int rightV = rebuildTree(root->right);

            root->val = leftV + rightV + root->val;

            int flag = 0;
            for(int i = 0 ; i < v.size() ; i++){
                if (v[i].value == root->val){
                    v[i].times++;
                    flag = 1;
                }
            }
            if(flag == 0){
                node temp;
                temp.value = root->val;
                temp.times = 1;
                v.push_back(temp);
            }

            return root->val;
        } else
            return 0;
    }

};
```

