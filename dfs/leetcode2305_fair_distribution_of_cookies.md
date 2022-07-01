# Fair Distribution of Cookies
link: https://leetcode.com/problems/fair-distribution-of-cookies/  

This problem asks us to assign cookies to K children. The unfairness is the maximum cookies one child gets.  
The return value should be the minimum unfairness of all distribution. 

**Solution**  

We just need to enumerate all possible scenario by dfs or so-called backtracking, since the data range of cookies is very small.
Here's a dfs/backtracking template.

```C++
class Solution {
private:
    int markCookies[10]; // from 0 - cookies.size() exclusive.
    int childCookies[10]; // from 0 - k exclusive.
    int cookiesLen;
    int childCnt;
    vector<int> cks;
    int kidSize;
public:
    int distributeCookies(vector<int>& cookies, int k) {
        cookiesLen = cookies.size();
        childCnt = k;
        cks = cookies;
        kidSize = k;

        return dfsDistribute(0);
    }
    
    int dfsDistribute(int curCookieIdx) { // assign current bag of cookies to different kids.
        if(curCookieIdx == cookiesLen) {
            return calcUnfairness();
        }
        int minUnfairness = INT_MAX;	// compare to get the minimum unfairness when current bag is assigned to kid i.
        for(int i = 0; i < kidSize; i++) {
            markCookies[curCookieIdx] = i;
            minUnfairness = mmin(minUnfairness, dfsDistribute(curCookieIdx + 1));
        }
        return minUnfairness;		// return the minimum unfairness
    }
    
    int calcUnfairness() {
        int unfairness = 0;
        memset(childCookies, 0, sizeof(childCookies));
        for(int i = 0; i < cookiesLen; i++) {
            childCookies[markCookies[i]] += cks[i];
            unfairness = mmax(unfairness, childCookies[markCookies[i]]);
        }
        return unfairness;
    }
    
    int mmax(int a, int b) {
        return a > b ? a : b;
    }
    int mmin(int a, int b) {
        return a < b ? a : b;
    }
};
```

The time complexity for this problem is very high.
If the data range of cookies was larger, we'd better consider a better solution (maybe greedy strategy or dp).
