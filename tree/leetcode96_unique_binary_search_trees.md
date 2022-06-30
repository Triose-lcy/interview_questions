# Unique Binary Search Trees
link: https://leetcode.com/problems/unique-binary-search-trees/

This problem asks how many different BST can be constructed by values from 1 to n. 

**Solutions**   


A straightforward solution is resursively enumerate all scenarios and count them. But it will be TLE for sure (just like Unique BST II).   

  
Another faster but also intuitive solution is also recursion. Suppose we have a resursive method called `int numTrees_(int s, int cur, int e) { // [s, e] inclusive.` which returns the count of different BST in range [s, e] inclusive, with root value = cur. So the implementation would be like this: 

```C++
class Solution {
public:
    int numTrees(int n) {
        int cnt = 0;
        for(int rootVal = 1; rootVal <= n; rootVal++)
            cnt += numTrees_(1, rootVal, n);
        return cnt;
    }
    
    int numTrees_(int s, int cur, int e) { // [s, e] inclusive.
        if(s == e) {
            return 1;
        }
        int leftSubTrees = 0;
        int rightSubTrees = 0;
        for(int i = s; i < cur; i++)
            leftSubTrees += numTrees_(s, i, cur - 1);
        for(int j = cur + 1; j <= e; j++)
            rightSubTrees += numTrees_(cur + 1, j, e);
        
        if(!leftSubTrees)
            return rightSubTrees;
        if(!rightSubTrees)
            return leftSubTrees;
        return leftSubTrees * rightSubTrees;
    }
};
```

However, solution 2 is still not fast enough, since it contains many repetitive function call. For example, inside numTrees_(1, 9, 15), numTrees_(1, 5, 8) and numTrees_(10, 13, 15) will be called, but these two are also called inside, for example, numTrees_(1, 9, 16). So this solution contains too much repetitive process.   
A good way to avoid it is using an array to store the return of such method. When we found we've already gotten the return value before for current method, we just return it, rather than calculate it again. 
Here's the new solution: 

```C++
class Solution {
private:
    const static int maxn = 25;
    int memory[maxn][maxn][maxn]; // a memory array. 
public:
    int numTrees(int n) {
        init();
        int cnt = 0;
        for(int rootVal = 1; rootVal <= n; rootVal++)
            cnt += numTrees_(1, rootVal, n);
        return cnt;
    }
    
    void init() {
        // set all value of memory array to 0
        // if not 0, then the numTrees_(s, cur, e) was calcuated before.
        memset(memory, 0, sizeof(memory));
        for(int i = 0; i < maxn; i++) memory[i][i][i] = 1;
    }
    
    int numTrees_(int s, int cur, int e) { // [s, e] inclusive.
        if(s == e) {
            return memory[s][s][s]; // init value
        }
        // already had the answer.
        // return it directly
        if(memory[s][cur][e] != 0) {
            return memory[s][cur][e];
        }
        int leftSubTrees = 0;
        int rightSubTrees = 0;
        for(int i = s; i < cur; i++)
            leftSubTrees += numTrees_(s, i, cur - 1);
        for(int j = cur + 1; j <= e; j++)
            rightSubTrees += numTrees_(cur + 1, j, e);
        
        // everytime we get an answer, just store it in that array.
        // the = operator have a return value which can be return. 
        if(!leftSubTrees) {
            return memory[s][cur][e] = rightSubTrees;
        }
        if(!rightSubTrees) {
            return memory[s][cur][e] = leftSubTrees;
        }
        return memory[s][cur][e] = leftSubTrees * rightSubTrees;
    }
};
```

Even though, it's not fast enough. We can use dp to save time and memory.
> dp[i][j] represents the number of different trees with value [i, j]  
> The init value of dp is dp[i][i] = 1.  
> dp[left][right] = dp[left][right] + dp[left][right - 1] + dp[left + 1][right] + (dp[left][mid - 1] * dp[mid + 1][right]). 

dp[left][right - 1] represents the tree with root value = right, and all nodes is in left sub-tree.
dp[left + 1][right] represents the tree with root value = left, and all nodes is in right sub-tree.
and (dp[left][mid - 1] * dp[mid + 1][left]) represents all sub-trees with root value = mid. 
So the implementation should be like this: (since the maxn is 20. we can assume the time complexity for this solution is O(1))

```C++
class Solution {
private:
    const static int maxn = 20;
    int dp[maxn][maxn];
public:
    int numTrees(int n) {
        init();
        solve();
        return dp[1][n];
    }
    
    void init() {
        memset(dp, 0, sizeof(dp));
        for(int i = 0; i < maxn; i++) {
            dp[i][i] = 1;
        }
    }
    
    void solve() {
        for(int len = 1; len < maxn; len++) {
            for(int left = 1; left + len < maxn; left++) {
                int right = left + len;
                dp[left][right] += (dp[left][right - 1] + dp[left + 1][right]);
                for(int mid = left + 1; mid < right; mid++) {
                    dp[left][right] += (dp[left][mid - 1] * dp[mid + 1][right]);
                }
            }
        }
    }
};
```