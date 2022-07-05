[Kadane's Algorithm Reference](https://en.wikipedia.org/wiki/Maximum_subarray_problem)

# Maximum Subarray

link: https://leetcode.com/problems/maximum-subarray/  

Give you an array, find a subarray whose sum is the max sum.  

**Solution**  

The brutal solution would be O(n^2). We just simply enumerate all start point and end point for the given array to get the best resut.  

However, we can use dp to optimize the brutal solution.  
First, the **status representation**.  
dp[i] means the max sum of subarraies ends with a[i].   
Then the **init values** will be: dp[i] = a[i].  
Last, the **status transformation**.  
dp[i] = max(dp[i - 1] + a[i], a[i])  

So the answer should be max(dp, dp + n).  

```C++
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        vector<int> dp;
        int n = nums.size();
        
        // init
        for(auto x : nums) dp.push_back(x);
        
        for(int i = 1; i < n; i++) {
            dp[i] = max(dp[i - 1] + nums[i], dp[i]);
        }
        
        return *max_element(dp.begin(), dp.end());
    }
};
```

However, we don't need the dp array. Every element in dp is used only one time. We can use a variable to replace it to save memory.  
That's the Kadane's Algorithm's standard implementation.  
```C++
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        int curSum = 0;
        int bestSum = -INT_MAX;
        
        for(auto x: nums) {
            curSum = max(x, curSum + x);
            bestSum = max(bestSum, curSum);
        }
        
        return bestSum;
    }
};
```
In above, curSum stands for the max subarray sum ending with nums[i]. And the bestSum is the maximum value of all curSum. 



# Maximum Score Of Spliced Array
link: https://leetcode.com/problems/maximum-score-of-spliced-array/

Then we need to discuss another question related to Kadane's Algo.  

This problem gives us 2 arrays. We need to swap a subarray to maximize one's sum.  

**Solution**  

Actually, the first solution I consider is sliding window and prefix sum.  
But there is a more direct solution.  
Suppose we have array d1 and d2, where `d1[i] = (nums1[i] - nums2[i])` and `d2[i] = (nums2[i] - nums1[i])`.  
Then this problem can be transferred to get the maximum subarray of d1 and d2. Suppose that are maxSum1 and maxSum2.  
The final answer should be `max(nums1Sum + maxSum2, nums2Sum + maxSum1)`.  
So the solution uses Katane's Algorithm to calculate max subarray for d1 and d2.  

```C++
class Solution {
public:
    int maximumsSplicedArray(vector<int>& nums1, vector<int>& nums2) {
        int n = nums1.size();
        int sum1 = 0, sum2 = 0;
        int curSum1 = 0, curSum2 = 0;
        int bestSum1 = INT_MIN, bestSum2 = INT_MIN;
        
        // init
        for(int i = 0; i < n; i++) sum1 += nums1[i], sum2+= nums2[i];
        
        // Kadane's Algo
        for(int i = 0; i < n; i++) {
            int curDiff12 = nums1[i] - nums2[i];
            int curDiff21 = nums2[i] - nums1[i];
            
            curSum1 = max(curDiff21, curSum1 + curDiff21); // here should be curDiff21
            curSum2 = max(curDiff12, curSum2 + curDiff12); // here should be curDiff12
            
            bestSum1 = max(bestSum1, curSum1);
            bestSum2 = max(bestSum2, curSum2);
        }
        
        return max(sum1 + bestSum1, sum2 + bestSum2);
    }
};
```
