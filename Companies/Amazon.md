# Amazon

<br>

### [Maximum Units on a Truck](https://leetcode.com/problems/maximum-units-on-a-truck/)

```cpp
static bool comp (vector<int> &v1, vector<int> &v2){
    return v1[1]>=v2[1];
}


int maximumUnits(vector<vector<int>>& boxTypes, int truckSize) {
    sort(boxTypes.begin(), boxTypes.end(), comp);
    int ans = 0;

    for(auto b : boxTypes){

        if(b[0]<=truckSize){
            ans += b[0]*b[1];
            truckSize -= b[0];
        }

        else{
            ans += truckSize*b[1];
            return ans;
        }
    }

    return ans;
}
```

<br>

### [Reorder Data in Log Files](https://leetcode.com/problems/reorder-data-in-log-files/)

```cpp
static bool comp(pair<string, string> a, pair<string, string> b){
     return a.second == b.second ? a.first < b.first : a.second < b.second;
}


vector<string> reorderLogFiles(vector<string>& logs) {
    vector<string> digitLogs, ans;
    vector<pair<string, string>> letterLogs;

    for (string s : logs) {
        int i = 0;
        while (s[i] != ' ') i++;

        if (isalpha(s[i + 1])) 
            letterLogs.push_back({s.substr(0, i), s.substr(i + 1)});
        else 
            digitLogs.push_back(s);
    }

    sort(letterLogs.begin(), letterLogs.end(), comp);

    for (auto p : letterLogs) 
        ans.push_back(p.first + " " + p.second);

    for (string s : digitLogs) 
        ans.push_back(s);

    return ans;
}
```
