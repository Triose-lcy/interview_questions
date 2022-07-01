# Binary Tree Cameras
link: https://leetcode.com/problems/binary-tree-cameras/

Every camera can monitor it's parent, itself, and its immediate children. You need to find the minimum number of cameras that all nodes can be monitored.  

**Solution**  

Once we see tree related problems, it's intuitive to consider recursion. And recursion have bottom-to-top and top-to-bottom. Obviously, this problem is more suitable for bottom-to-top. 

The "minimum number of cameras" makes us consider greedy strategy or dp. In this problem, greedy is more close to the context.  

We do have some conditions to consider put or not put a camera on a given tree node.   

1. If current node is a leaf node, it's not worthwhile to put a camera onto it compared with putting a camera onto it's parent.
2. If current node is not a leaf, and one or both of it's child nodes are not monitored, we have to put a camera onto it.  

So, the solution would be from bottom-to-top tree iteration and greedy strategy. 


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
    const int MONITOR = 1;
public:
    int minCameraCover(TreeNode* root) {
        int ans = 0;
        dfsPutMonitorAndCount(root, ans);
        if(!isMonitored(root)) { // still need to judge if root is monitored or not.
            ans++;
        }
        return ans;
    }
    
    void dfsPutMonitorAndCount(TreeNode* curNode, int& ans) {
        if(curNode->left) {
            dfsPutMonitorAndCount(curNode->left, ans);
        } if (curNode->right) {
            dfsPutMonitorAndCount(curNode->right, ans);
        }
        
        if(haveNotBeingMonitoredChild(curNode)) { // this is the key condition.
            putMonitor(curNode);
            ans++;
        }
    }
    
    bool haveNotBeingMonitoredChild(TreeNode* curNode) {
        if(curNode->left && !isMonitored(curNode->left)) return true;
        if(curNode->right && !isMonitored(curNode->right)) return true;
        return false;
    }
    
    bool isMonitored(TreeNode* curNode) {
        if(curNode->left && curNode->left->val == MONITOR) return true;
        if(curNode->right && curNode->right->val == MONITOR) return true;
        return curNode->val == MONITOR;
    }
    
    void putMonitor(TreeNode* curNode) {
        curNode->val = MONITOR;
    }
};
```

