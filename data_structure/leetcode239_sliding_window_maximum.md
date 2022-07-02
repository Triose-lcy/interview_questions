# Sliding Window Maximum

link: https://leetcode.com/problems/sliding-window-maximum/

You are given an array, and a window with size K. The window will slide from the begin to the end. You need get the maximum value with in each window. 

**Solution**  

The array length is [1, 1e+5], that means if we need to run our solution within 1 second, we should develop a at least O(nlogn) solution. The most rudimental solution with be iter all values in a window and get the maximum value (whose time complexity is O(n^2)).  

To optimize it from O(n^2) to O(nlogn), we need to use some data structure which can maintain a order within it.  
So we can use std::set (which is implemented by balance trees -- maybe red black tree or AVL or B-tree), whose elements are in sequence and we can insert or search an element with O(logn).  
However, set only save one copy of one value, if our window contains 2 or more same values, this solution may get a wrong answer. So we should use multiset.   
Here's the O(nlogn) solution. 

```C++
class Solution {
public:
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {
        vector<int> ret;
        multiset<int> wd;
        int len=nums.size();
        for(int i=0; i<k; ++i) {
            wd.insert(nums[i]);
        }
        
        ret.push_back(*wd.rbegin());
        for(int i=k; i<len; i++) {
            wd.erase(wd.find(nums[i-k]));
            wd.insert(nums[i]);
            // cout << "++++++++\n";
            // cout << "erase:" << nums[i-k] << ", insert:" << nums[i] << endl;
            // print(wd);
            ret.push_back(*wd.rbegin()); // maximum value is at the rbegin()
        }
        return ret;
    }
    void print(multiset<int> &s) {
        for(auto iter = s.begin(); iter != s.end(); iter++) {
            cout << *iter << " ";
        }
        cout << endl;
    }
};
```

A faster solution would be O(n), if we can use some data structure to access the maximum value within O(1).  
We can use deque (emulating a window) to construct a decreasing sequence with the values in the actual sliding window with the following principles.  

1. while current value is larger than the value at the end of the deque, pop back all smaller values and push current value at the end. So all values in front of current value is larger or equal to current value.  
2. when the current value's index is i and i >= k, the emulating sliding window may or may not be full (since we don't know if we poped other values which should be in the actual sliding window.). So we need to judge if the front most value is a[i - k] which is the value should be poped in the actual sliding window.  
3. if i >= k - 1, we should push the largest value (the front value in deque) into the answer vector.   

So the solution would be: 

```C++
class Solution {
public:
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {
        vector<int> ans;
        deque<int> slidingWindow;
        int len = nums.size();
        
        for(int i = 0; i < len; i++) {
            while(!slidingWindow.empty() && slidingWindow.back() < nums[i]) // nums[i] is the cur value.
                slidingWindow.pop_back();	// pop all values smaller than it since they can never be the largest value.
            slidingWindow.push_back(nums[i]);	// put current value at the end.
            if (i >= k && nums[i - k] == slidingWindow.front()) {	// need to pop front value. 
                slidingWindow.pop_front();
            }
            if (i >= k - 1) {
                ans.push_back(slidingWindow.front());
            }
        }
        
        return ans;
    }
};
```

