# Unique Binary Search Trees II
link: https://leetcode.com/problems/unique-binary-search-trees-ii/

This problem asks us to generate all structurally different BSTs with [1, n].   

Once we see a tree related problem, we first consider recursion, since trees are recursive data structure. (So as linked list, but linked list related problems often use different pointers to do something).

**Solution**  

We can construct a tree (or a sub-tree) by specifying it's root node's value and left and right sub-tree's value range.   
Suppose we have a method called `generateTrees(int s, int cur, int e)`. This method generates all the trees whose root value is `cur`, and min value is `s`, max value is `e`.   
Then the implementation should be like this:

```C++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    vector<TreeNode*> generateTrees(int n) {
        vector<TreeNode*> ret;
        for(int rootVal = 1; rootVal <= n; rootVal++) {
            vector<TreeNode*> tmp = generateTrees_(1, rootVal, n);
            // like the append in python
            // append all elements in one vector to another.
            ret.insert(ret.end(), tmp.begin(), tmp.end());
        }
        return ret;
    }
    
    vector<TreeNode*> generateTrees_(int s, int cur, int e) {
        vector<TreeNode*> ret;
        
        if (s == e) {
            // every time just new a node if cur value == s == e
            ret.push_back(new TreeNode(cur));
            return ret;
        }
        
        vector<TreeNode*> treeLeftSubTrees;
        vector<TreeNode*> treeRightSubTrees;
        
        for(int leftSonVal = s; leftSonVal < cur; leftSonVal++) {
            // constructing all left sub-trees
            vector<TreeNode*> tmpLeft = generateTrees_(s, leftSonVal, cur - 1);
            treeLeftSubTrees.insert(treeLeftSubTrees.end(),
                                   tmpLeft.begin(),
                                   tmpLeft.end());
        }
        
        for(int rightSonVal = cur + 1; rightSonVal <= e; rightSonVal++) {
            // constructing all right sub-trees
            vector<TreeNode*> tmpRight = generateTrees_(cur + 1, rightSonVal, e);
            treeRightSubTrees.insert(treeRightSubTrees.end(),
                                    tmpRight.begin(),
                                    tmpRight.end());
        }
        
        int leftLen = treeLeftSubTrees.size();
        int rightLen = treeRightSubTrees.size();
        
        // put left and right sub-trees together with current root.
        if(!leftLen) {
            for(int i = 0; i < rightLen; i++) {
                ret.push_back(new TreeNode(cur, 
                                          NULL,
                                          treeRightSubTrees[i]));
            }
        }
        if(!rightLen) {
            for(int i = 0; i < leftLen; i++) {
                ret.push_back(new TreeNode(cur,
                                          treeLeftSubTrees[i],
                                          NULL));
            }
        }
        
        for(int i = 0; i < leftLen; i++) {
            for(int j = 0; j < rightLen; j++) {
                ret.push_back(new TreeNode(cur,
                                          treeLeftSubTrees[i],
                                          treeRightSubTrees[j]));
            }
        }
        
        return ret;
    }
};
```