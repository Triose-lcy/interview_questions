# Validate Binary Search Tree (BST)
link: https://leetcode.com/problems/validate-binary-search-tree/

Binary search tree is a tree data structure where the value of left child is smaller than the value of root and the value of right child is larger than the root.

**Solution**
Easy. We just do a in-order iteration and store all the values into an array. Then we need to judge if all items' value are larger than it's previous value. 

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
private:
    vector<int> inorder;
public:
    bool isValidBST(TreeNode* root) {
        getInorder(root);
        int len = inorder.size();
        for(int i = 1; i < len; i++) {
            if(inorder[i] <= inorder[i - 1]) return false;
        }
        return true;
    }
    // Here's a template for in-order iteration. 
    void getInorder(TreeNode* root) {
        if(!root) return ;
        getInorder(root->left);
        inorder.push_back(root->val);
        getInorder(root->right);
    }
};
```