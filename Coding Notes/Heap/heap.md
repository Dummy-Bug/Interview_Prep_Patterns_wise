# Heap (Priority Queue)

**Maxheap using priority queue**

`priority_queue<int> maxheap;`

**Minheap using priority queue**

`priority_queue<int, vector<int>, greater<int>> minheap;`

<br>

### 1. Sort a nearly sorted (or K sorted) array
Given an array of n elements, where each element is at most k away from its target position. Sort the array.

```cpp
vector<int> sort_k_sorted_array(vector<int> & v, int k){
    vector<int> res;
    priority_queue<int, vector<int>, greater<int>> minheap;
    int i=0;
    
    for( ; i<k-1; i++)
        minheap.push(v[i]);

    for( ; i<v.size(); i++){
        minheap.push(v[i]);
        res.push_back(minheap.top());
        minheap.pop();
    }
    
    while(!minheap.empty()){
        res.push_back(minheap.top());
        minheap.pop();
    }
    
    return res;
}
```

<br>

### 2. Last Stone Weight
You are given an array of integers stones where stones[i] is the weight of the ith stone. On each turn, we choose the heaviest two stones and smash them together. 
1. If x == y, both stones are destroyed, and
2. If x != y, the stone of weight x is destroyed, and the stone of weight y has new weight y - x.

At the end of the game, there is at most one stone left. Return the weight of the left stone. If there are no stones left, return 0.

```cpp
int lastStoneWeight(vector<int>& stones) {
    priority_queue<int> maxHeap;

    for(int s : stones)
        maxHeap.push(s);

    while(maxHeap.size()>1){
        int s1 = maxHeap.top(); maxHeap.pop();
        int s2 = maxHeap.top(); maxHeap.pop();

        if(s1!=s2)
            maxHeap.push(abs(s1-s2));
    }

    if(maxHeap.empty()) return 0;
    return maxHeap.top();
}
```

<br>

### 3. Top K Frequent Elements
Given an integer array nums and an integer k, return the k most frequent elements. You may return the answer in any order.

```cpp
vector<int> topKFrequent(vector<int>& nums, int k) {

    unordered_map<int,int> mp;
    priority_queue<pair<int,int>, vector<pair<int,int>>, greater<pair<int,int>> > minheap;
    vector<int> res;

    for(int n : nums)
        mp[n]++;

    for(auto p : mp){
        minheap.push({p.second, p.first});

        if(minheap.size()>k)
            minheap.pop();
    }

    while(!minheap.empty()){
        auto p = minheap.top(); minheap.pop();
        res.push_back(p.second);
    }

    return res;
}
```

<br>

### 4. Sort Characters By Frequency
Given a string s, sort it in decreasing order based on the frequency of characters, and return the sorted string.

Input: s = "tree"

Output: "eert"

```cpp
string frequencySort(string s) {
    unordered_map<char, int> mp;
    priority_queue<pair<int,char>> maxheap;
    string res = "";

    for(char ch : s)
        mp[ch]++;

    for(auto p : mp)
        maxheap.push({p.second, p.first});

    while(!maxheap.empty()){
        auto p = maxheap.top(); maxheap.pop();
        for(int i=0; i<p.first; i++)
            res.push_back(p.second);
    }

    return res;
}
```

<br>

### 5. Top K Frequent Words (using Customized Comparator)
Given an array of strings words and an integer k, return the k most frequent strings. Return the answer sorted by the frequency from highest to lowest. Sort the words with the same frequency by their lexicographical order.

Input: words = ["the","day","is","sunny","the","the","the","sunny","is","is"], k = 4

Output: ["the","is","sunny","day"]

```cpp
/* 
    user defined comparator for set and priority queues will be defined like this 
    REMEMBER : Use opposite sign
*/

struct comp{
  bool operator()(pair<int, string> p1, pair<int, string> p2){
      if(p1.first>p2.first) return true;         
      if(p1.first==p2.first and p1.second < p2.second) return true;
      return false;
  }  
};


vector<string> topKFrequent(vector<string>& words, int k) {
    unordered_map<string, int> mp;
    priority_queue<pair<int, string>, vector<pair<int, string>>, comp> minheap;
    vector<string> res;

    for(string s : words)
        mp[s]++;

    for(auto p : mp){
        minheap.push({p.second, p.first});

        if(minheap.size()>k)
            minheap.pop();
    }

    while(!minheap.empty()){
        res.push_back(minheap.top().second);
        minheap.pop();
    }

    reverse(res.begin(), res.end());
    return res;
}
```

<br>

### 6. K Closest Points to Origin
Given an array of points where points[i] = [xi, yi] and an integer k, return the k closest points to the origin (0, 0). You may return the answer in any order. 

```
vector<vector<int>> kClosest(vector<vector<int>>& points, int k) {

    priority_queue<pair<int, pair<int,int>>> maxheap;
    vector<vector<int>> res;

    for(auto p : points){
        int x = p[0], y = p[1];
        int dist = x*x + y*y;

        maxheap.push({dist, {x, y}});

        if(maxheap.size()>k)
            maxheap.pop();
    }

    while(!maxheap.empty()){
        auto p = maxheap.top(); maxheap.pop();
        res.push_back({p.second.first, p.second.second});
    }

    return res;
}
```

<br>

### 7. Car Pooling
You are given the integer capacity and an array trips where trip[i] = [numPassengersi, fromi, toi] indicates that the ith trip has numPassengersi passengers and the locations to pick them up and drop them off are fromi and toi respectively. The locations are given as the number of kilometers due east from the car's initial location.

Return true if it is possible to pick up and drop off all passengers for all the given trips, or false otherwise.

Input: trips = [[3,2,7],[3,7,9],[8,3,9]], capacity = 11

Output: true

Hint : Use heap to keep track of seat occupancy at each intant. Before giving seats to new passengers, pop all passengers from the heap whose endTime less then startTime of new passengers.

```cpp
static bool comp (vector<int> & v1, vector<int> & v2){
    return v1[1]<=v2[1];
}

bool carPooling(vector<vector<int>>& trips, int capacity) {
    int n = trips.size();
    sort(trips.begin(), trips.end(), comp);

    priority_queue<pair<int, int>, vector<pair<int,int>>, greater<pair<int,int>> > occupied;      // --> pair {end_location, num_passengers}

    for(int i=0; i<n; i++){
        int num_passengers = trips[i][0], start_loc = trips[i][1], end_loc = trips[i][2];

        while(!occupied.empty() and occupied.top().first <= start_loc){
            capacity += occupied.top().second;
            occupied.pop();
        }

        if(capacity < num_passengers) return false;

        occupied.push({end_loc, num_passengers});
        capacity -= num_passengers;
    }

    return true;
}
```

<br>

### 8. Longest Happy String
A string is called happy if it does not have any of the strings 'aaa', 'bbb' or 'ccc' as a substring. Given three integers a, b and c, return any string s, which satisfies following conditions:

1. s is happy and longest possible.
2. s contains at most a occurrences of the letter 'a', at most b occurrences of the letter 'b' and at most c occurrences of the letter 'c'.
3. s will only contain 'a', 'b' and 'c' letters.
4. If there is no such string s return the empty string "".

Input: a = 1, b = 1, c = 7

Output: "ccaccbcc"

Hint: Use most frequent char at every instant which satisfies the condition.

```cpp
string longestDiverseString(int a, int b, int c) {

    priority_queue<pair<int,char>> maxheap;

    if(a>0) maxheap.push({a, 'a'});
    if(b>0) maxheap.push({b, 'b'});
    if(c>0) maxheap.push({c, 'c'});

    char prev = '#';
    int cnt = 0;
    string s = "";

    while(!maxheap.empty()){
        auto p = maxheap.top(); maxheap.pop();

        if(p.second==prev) {
            cnt++;
            if(cnt==3){
                if(maxheap.empty()) return s;

                auto p2 = maxheap.top(); maxheap.pop();
                s.push_back(p2.second);
                prev = p2.second;
                cnt = 1;

                if(p2.first>1) maxheap.push({p2.first-1, p2.second});   
                maxheap.push(p);
            }
            else{
                s.push_back(p.second);
                if(p.first>1) maxheap.push({p.first-1, p.second});   
            }
        }
        
        else{
            s.push_back(p.second);
            prev = p.second;
            cnt = 1;

            if(p.first>1) maxheap.push({p.first-1, p.second});   
        }
    }

    return s;
}
```

<br>

### 9. Reorganize String
Given a string s, rearrange the characters of s so that any two adjacent characters are not the same. Return any possible rearrangement of s or return "" if not possible.

Input: s = "aab"

Output: "aba"

Hint: Try to place max freq char as early as possible such that no two adjacent are same.

```cpp
string reorganizeString(string s) {
    string res = "";

    unordered_map<char, int> mp;
    for(char ch : s)
        mp[ch]++;

    priority_queue<pair<int,char>> maxheap;         // --> maxheap of pair {freq, char}

    for(auto p : mp)
        maxheap.push({p.second, p.first});

    while(!maxheap.empty()){
        auto p1 = maxheap.top(); maxheap.pop();

        if(maxheap.empty()){

            if(p1.first==1){
                res.push_back(p1.second);
                return res;
            }
            else return "";
        }

        auto p2 = maxheap.top(); maxheap.pop();

        res.push_back(p1.second);
        res.push_back(p2.second);

        if(p1.first>1) maxheap.push({p1.first-1, p1.second});
        if(p2.first>1) maxheap.push({p2.first-1, p2.second});
    }

    return res;
}
```

<br>

## Hard and Tricky Problems

### 1. Task Scheduler (Tricky)
Given a characters array tasks, representing the tasks a CPU needs to do, where each letter represents a different task. Tasks could be done in any order. Each task is done in one unit of time. For each unit of time, the CPU could complete either one task or just be idle. However, there is a non-negative integer n that represents the cooldown period between two same tasks. Return the least number of units of times that the CPU will take to finish all the given tasks.

Input: tasks = ["A","A","A","B","B","B"], n = 2

Output: 8

Explanation: A -> B -> idle -> A -> B -> idle -> A -> B. There is at least 2 units of time between any two same tasks.

Hint: Use Two heaps, one to fetch task with max freq and other for keeping track of forbidden tasks till cooldown period.

```cpp
int leastInterval(vector<char>& tasks, int cooldown_period) {

    unordered_map<char, int> mp;    // --> for storing frequency of each task

    /* maxheap will contain task sorted by their freq -->  pair of {freq, char} */

    priority_queue<pair<int, char>> maxheap;  

    /* minheap will store forbidden tasks till cooldown period --> pair of {prev_time, char} */

    priority_queue<pair<int, char>, vector<pair<int, char>>, greater<pair<int, char>> > minheap;       


    for(char ch : tasks)
        mp[ch]++;

    for(auto p : mp)
        maxheap.push({p.second, p.first});

    int time = 0;

    while(!maxheap.empty() or !minheap.empty()){

        /* pop all tasks from minheap whose cooldown period gets over and push them to maxheap */

        while(!minheap.empty() and minheap.top().first+cooldown_period<time){
            auto ch = minheap.top().second; minheap.pop();
            maxheap.push({mp[ch], ch});
        }


        /* pop one task with highest freq from maxheap and allocate to cpu */

        if(!maxheap.empty()){
            auto task = maxheap.top(); maxheap.pop();
            mp[task.second]--;

            /* If freq of curr task > 0 push it into minheap for cooldown period */

            if(mp[task.second]>0)
                minheap.push({time, task.second});
        }

        time++;
    }

    return time;
}
```

<br>

### 2. The Number of the Smallest Unoccupied Chair (Tricky)
There is a party where n friends numbered from 0 to n - 1 are attending. There is an infinite number of chairs in this party that are numbered from 0 to infinity. When a friend arrives at the party, they sit on the unoccupied chair with the smallest number.

For example, if chairs 0, 1, and 5 are occupied when a friend comes, they will sit on chair number 2. When a friend leaves the party, their chair becomes unoccupied at the moment they leave. 

You are given a 0-indexed 2D integer array times where times[i] = [arrivali, leavingi], indicating the arrival and leaving times of the ith friend respectively, and an integer targetFriend. All arrival times are distinct. Return the chair number that the friend numbered targetFriend will sit on.

Input: times = [[1,4],[2,3],[4,6]], targetFriend = 1

Output: 1

```cpp
static bool comp (vector<int> v1, vector<int> v2){
    return v1[0]<v2[0];
}

int smallestChair(vector<vector<int>>& times, int targetFriend) {
    int target_friend_arrival = times[targetFriend][0];

    sort(times.begin(), times.end(), comp);

    priority_queue<pair<int,int>, vector<pair<int,int>>, greater<pair<int, int>>> booked;            //--> {endTime, chairNumber}
    priority_queue<int, vector<int>, greater<int>> free_slot;

    bool isTarget = false;

    for(int i=0; i<times.size(); i++){

        if(times[i][0]==target_friend_arrival) isTarget = true;
        int chair;

        while(!booked.empty() and booked.top().first<=times[i][0]){
            free_slot.push(booked.top().second);
            booked.pop();
        }

        if(free_slot.empty())
            chair = booked.size();
        else{
            chair = free_slot.top();
            free_slot.pop();
        }

        if(isTarget) return chair;
        else booked.push({times[i][1], chair});
    }

    return 0;
}
```

<br>

### 3. Maximum Number of Events That Can Be Attended (Tricky)
Given an array of events where events[i] = [startDayi, endDayi]. Every event i starts at startDayi and ends at endDayi. You can attend an event i at any day d where startTimei <= d <= endTimei. Notice that you can only attend one event at any time d. Return the maximum number of events you can attend.

Input: events= [[1,2],[2,3],[3,4],[1,2]]

Output: 4

Hint: Sort events increased by start time. Minheap keeps the current open events. Iterate from the day 1 to day 100000, Each day, we add new events starting on day d to minheap. Also we remove the events that are already closed. Then we greedily attend the event that ends soonest.

```cpp

/* Time Complexity : O(d+nlogn) where d is range of days */

static bool comp (vector<int> v1, vector<int> v2){
    return v1[0]<v2[0];
}


int maxEvents(vector<vector<int>>& events) {
    int n = events.size();
    sort(events.begin(), events.end(), comp);                   // --> sort events by their startDay

    priority_queue<int, vector<int>, greater<int>> minheap;     // --> minheap of (endDays)

    int i=0;        // --> will iterate on events 
    int cnt = 0;

    /* Iterate for every day */

    for(int day=1; day<=100000; day++){

        /* push all events in minheap that will start on current day */

        while(i<n and events[i][0]==day){
            minheap.push(events[i][1]);
            i++;
        }

        /* pop all events whose deadline gets over */

        while(!minheap.empty() and minheap.top()<day)
            minheap.pop();

        /* pop one event with minimum deadline from minheap and increament cnt */

        if(!minheap.empty()){
            minheap.pop();
            cnt++;
        }

        if(i==n and minheap.empty()) break;     
    }

    return cnt;
}
```

<br>

### 4. Furthest Building You Can Reach (Tricky)
You are given an integer array heights representing the heights of buildings, some bricks, and some ladders. You start your journey from building 0 and move to the next building by possibly using bricks or ladders.

While moving from building i to building i+1 (0-indexed),
1. If the current building's height is greater than or equal to the next building's height, you do not need a ladder or bricks.
2. If the current building's height is less than the next building's height, you can either use one ladder or (h[i+1] - h[i]) bricks.

Return the furthest building index (0-indexed) you can reach if you use the given ladders and bricks optimally.

Input: heights = [4,2,7,6,9,14,12], bricks = 5, ladders = 1

Output: 4

Hint: Think upto when we can use ladders, and if jumps crosses number of ladders, use bricks for minimum jumps taken so far and save those ladder for future.

```cpp
int furthestBuilding(vector<int>& heights, int bricks, int ladders) {
    int n = heights.size();

    priority_queue<int, vector<int>, greater<int>> minheap;     // --> It will store all jumps taken so far

    for(int i=0; i<n-1; i++){
        int diff = heights[i+1] - heights[i];

        if(diff<=0) continue;       // --> If next building is smaller, we will simply continue

        minheap.push(diff);

        /* 
            If numbers of jumps taken exceeds ladder count, then 
            take out the min jump from heap, and use bricks for it     
        */

        if(minheap.size()>ladders){
            int minjump = minheap.top(); minheap.pop();

            /* If jump require more bricks, then we can't go further hence we will simply return i */

            if(minjump > bricks) return i;

            bricks -= minjump;
        }

    }

    return n-1;
}
```

<br>

### 5. Find K Pairs with Smallest Sums (Too Tricky)
You are given two integer arrays nums1 and nums2 sorted in ascending order and an integer k. Define a pair (u, v) which consists of one element from the first array and one element from the second array. Return the k pairs (u1, v1), (u2, v2), ..., (uk, vk) with the smallest sums.

Input: nums1 = [1,7,11], nums2 = [2,4,6], k = 3

Output: [[1,2],[1,4],[1,6]]

[GFG Link](https://www.geeksforgeeks.org/find-k-pairs-smallest-sums-two-arrays/)

**Approach 1 : Using maxheap - O(n1 * n2 * logk)**

```cpp
vector<vector<int>> kSmallestPairs(vector<int>& nums1, vector<int>& nums2, int k) {

    priority_queue<pair<int, pair<int,int>>> maxheap;
    vector<vector<int>> res;

    for(int i=0; i<nums1.size(); i++){
        for(int j=0; j<nums2.size(); j++){

            maxheap.push({nums1[i]+nums2[j], {nums1[i], nums2[j]}});   

            if(maxheap.size() > k)
                maxheap.pop();
        }
    }


    while(!maxheap.empty()){
        res.push_back({maxheap.top().second.first, maxheap.top().second.second});
        maxheap.pop();
    }


    return res;
}
```

**Approach 2 : Using index array - O(n1*k)**

```cpp
vector<vector<int>> kSmallestPairs(vector<int>& nums1, vector<int>& nums2, int k) {

    /* This vector store the indexes for nums2 processed so far for each element of nums1 */

    vector<int> index2 (nums1.size(), 0);       // --> Initially zero elements are processed
    vector<vector<int>> res;

    while(k--){

        /* Everytime iterate whole nums1 and find the minSum pair */

        int minSum = INT_MAX;
        int idx1 = 0, idx2 = 0;
        bool flag = false;          // --> for cases where k > nums1.size() * nums2.size()

        for(int i=0; i<nums1.size(); i++){

            /* If for some index1, if we already processed whole nums2 then continue */

            if(index2[i]==nums2.size()) continue;     

            if(minSum > nums1[i] + nums2[index2[i]]){
                minSum = nums1[i] + nums2[index2[i]];
                idx1 = i;
                idx2 = index2[i];
                flag = true;
            }
        }

        if(!flag) break;

        res.push_back({nums1[idx1], nums2[idx2]});
        index2[idx1]++;
    }

    return res;
}
```

**Approach 3 : Using index array with MinHeap - O(k * logn1)** 

Hint: Similar to approach 2, just use minheap in place of loop for finding minsum pair everytime.

```cpp
vector<vector<int>> kSmallestPairs(vector<int>& nums1, vector<int>& nums2, int k) {

    vector<vector<int>> res;
    priority_queue<pair<int,int>, vector<pair<int,int>>, greater<pair<int,int>> > minheap;      // --> pair {sum, idx1}
    vector<int> index2 (nums1.size(), 0);

    /* push sum of every pair of all elements of nums1 with 0th element of nums2 in minheap */

    for(int i=0; i<nums1.size(); i++){
        int sum = nums1[i] + nums2[0];
        minheap.push({sum, i});
    }

    /* Iterate till we get k smallest sums or minheap becomes empty */

    while(k-- and !minheap.empty()){

        /* pop pair with min sum from min heap */

        auto p = minheap.top(); minheap.pop();
        int idx = p.second;
        res.push_back({nums1[idx], nums2[index2[idx]]});

        /* increment index2 for that idx1 and push it again into the heap */

        index2[idx]++;

        if(index2[idx]<nums2.size()){
            int sum = nums1[idx] + nums2[index2[idx]];
            minheap.push({sum, idx});
        }
    }

    return res;
}
```

<br>

### 6. Find Median from Data Stream (**IMP**)

[Explaination](https://www.youtube.com/watch?v=1LkOrc-Le-Y)

Approach:
1. Divide whole stream of array in two parts.
2. First part will be stored in maxheap
3. Second part will be stored in minheap
4. If number of elements are odd --> Median lies in either top of maxheap or minheap whoever has more elements
5. If numbers of elements are even --> Both heaps will have equal elements and Median will be equals to (maxheap.top() + minheap.top())/2

```cpp
class MedianFinder {
public:
    

    priority_queue<int> maxheap;                                // --> for storing 1st half stream
    priority_queue<int, vector<int>, greater<int>> minheap;     // --> for storing 2nd half stream     
    
    
    MedianFinder() {
        
    }
    
    
    void addNum(int num) {
        
        /* If num is less then largest element of first half, we will push it into maxheap */
        
        if(!maxheap.empty() and num<=maxheap.top())
            maxheap.push(num);
        
        /* Else we will push it into minheap */
            
        else minheap.push(num);
        
        /* After pushing, balance both the heaps */
        
        if(maxheap.size()>minheap.size()+1){
            minheap.push(maxheap.top());
            maxheap.pop();
        }
        else if(minheap.size()>maxheap.size()+1){
            maxheap.push(minheap.top());
            minheap.pop();
        }
        
    }
    
    
    double findMedian() {
        
        if(maxheap.size()>minheap.size())               
            return maxheap.top();
        
        else if(minheap.size()>maxheap.size())
            return minheap.top();
        
        else
            return (minheap.top() + maxheap.top())/2.0;
    }
};
```

<br>

### 7. Merge k Sorted Lists (**IMP**)
You are given an array of k linked-lists lists, each linked-list is sorted in ascending order. Merge all the linked-lists into one sorted linked-list and return it. 

Input: lists = [[1,4,5],[1,3,4],[2,6]]

Output: [1,1,2,3,4,4,5,6]

```cpp
ListNode* mergeKLists(vector<ListNode*>& lists) {

    ListNode* dummy = new ListNode(0);
    ListNode* temp = dummy;

    priority_queue< pair<int,ListNode*>, vector<pair<int,ListNode*>>, greater<pair<int,ListNode*>> > minheap;

    /* Initially insert heads of all linked list to minheap */

    for(auto ptr : lists)
        if(ptr) minheap.push({ptr->val, ptr});

    /* 
        Iterate till heap becomes empty 
        pop minVal everytime and append it to ans by creating newNode 
        If next node exists for poped node, then push its next node to min heap
    */

    while(!minheap.empty()){
        auto p = minheap.top(); minheap.pop();    

        temp->next = new ListNode(p.first);
        temp = temp->next;

        if(p.second->next)
            minheap.push({p.second->next->val, p.second->next});
    }

    ListNode* head = dummy->next;
    return head;
}
```

