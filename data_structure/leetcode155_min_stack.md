# Min Stack
link: https://leetcode.com/problems/min-stack/

This problem requires us to develop a stack-like data structure with minimum value accessible.  

**Solution**  

Only 3*10^4 calls will be performed onto this min stack. So we can just use a variable to store the min value.  
This solution only have O(n) pop operation. The rest are all O(1).

```C++
class MinStack {
    deque<int> q;
    int minVal = INT_MAX;
public:
    MinStack() {
        minVal = INT_MAX;
    }
    
    void push(int val) {
        q.push_back(val);
        minVal = val < minVal ? val : minVal;
    }
    
    void pop() {
        // O(n)
        int tmpVal = q.back();
        q.pop_back();
        if(tmpVal == minVal) {
            minVal = INT_MAX;
            for(auto it = q.begin(); it != q.end(); it++) 
                minVal = *it < minVal ? *it : minVal;
        }
    }
    
    int top() {
        return q.back();
    }
    
    int getMin() {
        return minVal;
    }
};
```

However, we can still use some other ways to implement it. For example, use multiset to get the minimum value within O(log n).

```C++
class MinStack {
    stack<int> s;
    multiset<int> ms;
public:
    MinStack() {
    }
    
    void push(int val) {
        s.push(val);
        // O(log(n))
        ms.insert(val);
    }
    
    void pop() {
        // O(log(n))
        int tmp = s.top();
        ms.erase(ms.find(tmp));
        s.pop();
    }
    
    int top() {
        return s.top();
    }
    
    int getMin() {
        // O(1)
        return *ms.begin();
    }
};
```

The above two are intuitive. But the below two are a little tricky.  
Everytime we push a new value, no matter if it's a new minimum value, we can store the current minimum value.  
Once we want get the minimum value, we just return it.   
All the operations are O(1), but needs double memory.  


```C++
class MinStack {
private:
    stack<pair<int, int> > s;
public:
    MinStack() {}
    
    void push(int val) {
        if (s.empty()) {
            s.push(make_pair(val, val));
        } else {
            s.push(make_pair(val, min(s.top().second, val)));
        }
    }
    
    void pop() {
        s.pop();
    }
    
    int top() {
        return s.top().first;
    }
    
    int getMin() {
        return s.top().second;
    }
};
```


The last one is the most efficient solution, with an extra value.  
If the new value is x and x < minVal, we can push on top another value t = 2 * x - minVal, rather than the new value per se.   
After that, we update the minVal with x (we can mark it as curMinVal). So current minVal is x, the top value is t = 2 * x - prevMinVal. And t = 2 * x - prevMinVal < curMinVal = x, since x < prevMinVal.  
When performing pop operation and the top value is less than curMinVal, we should know, current top should be the actual min value, even though we did not store the actual value.  
The actual value x = curMinVal, top t = 2 * x - prevMinVal, so the prevMinVal = 2 * curMinVal - t.  


```C++
class MinStack {
private:
    stack<long> s;
    long minVal;
public:
    MinStack() {
    }
    
    void push(int val) {
        if(s.empty()) {
            s.push((long)val);
            minVal = (long)val;
        } else {
            if (val > minVal) {
                s.push((long)val);
            } else {
                s.push((long) 2 * (long) val - minVal);
                minVal = (long) val;
            }
        }
    }
    
    void pop() {
        if(s.top() < minVal) {
            minVal = (long) 2 * minVal - s.top();
        }
        s.pop();
    }
    
    int top() {
        return s.top() < minVal ? minVal : s.top();
    }
    
    int getMin() {
        return minVal;
    }
};
```
