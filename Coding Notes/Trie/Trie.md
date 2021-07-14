# Trie 
Trie is an efficient information reTrieval data structure. 

**Applications of Trie:**

1. Using Trie, search complexities can be brought to optimal limit (key length).
2. We can easily print all words in alphabetical order which is not easily possible with hashing.
3. We can efficiently do prefix search (or Auto-complete) with Trie.

**Issues with Trie:**

The main disadvantage of tries is that they need a lot of memory for storing the strings.

#### 1. Implement Trie (Prefix Tree)

```cpp
class Trie {
public:
    
    struct TrieNode{
        vector<TrieNode*> children;
        bool isTerminal;
        
        TrieNode(){
            children = vector<TrieNode*> (26, NULL);    
            isTerminal = false;
        }
    };
    
    TrieNode* root;
    
    Trie(){
        root = new TrieNode();
    }
    
    /* ------------------------ Inserts a word into the trie ------------------------- */
    
    void insert(string word) {
        TrieNode* temp = root;
        
        for(char ch : word){
            int idx = ch-'a';
            
            if(!temp->children[idx])
                temp->children[idx] = new TrieNode();
            
            temp = temp->children[idx];
        }
        
        temp->isTerminal = true;
    }
    
    /* ----------------------- Returns if the word is in the trie --------------------- */
    
    bool search(string word) {
        TrieNode* temp = root;
        
        for(char ch : word){
            int idx = ch-'a';
            
            if(!temp->children[idx])
                return false;
            
            temp = temp->children[idx];
        }
        
        return temp->isTerminal;
    }
    
    /* -------- Returns true if there is any word in the trie that starts with the given prefix -------- */
    
    bool startsWith(string prefix) {
        TrieNode* temp = root;
        
        for(char ch : prefix){
            int idx = ch-'a';
            
            if(!temp->children[idx])
                return false;
            
            temp = temp->children[idx];
        }
        
        return true;
    }
};
```

#### 2. Search Suggestions System
Given an array of strings products and a string searchWord. We want to design a system that suggests at most three product names from products after each character of searchWord is typed. Suggested products should have common prefix with the searchWord. If there are more than three products with a common prefix return the three lexicographically minimums products. Return list of lists of the suggested products after each character of searchWord is typed. 

```cpp
struct TrieNode{
    vector<TrieNode*> children;
    bool isTerminal;

    TrieNode(){
        children = vector<TrieNode*> (26, NULL);
        isTerminal = false;
    }
};


void insert (TrieNode* root, string word){
    TrieNode* temp = root;

    for(char ch : word){
        int idx = ch-'a';

        if(!temp->children[idx])
            temp->children[idx] = new TrieNode();

        temp = temp->children[idx];
    }

    temp->isTerminal = true;
} 


void fetch_words (TrieNode* temp, int & count, vector<string> & suggestions, string & s){        
    if(count==3) return;           // --> we will not do further dfs after finding 3 words

    if(temp->isTerminal){
        suggestions.push_back(s);
        count++;
    }

    for(int i=0; i<26; i++){
        if(temp->children[i]){
            s.push_back('a' + i);
            fetch_words (temp->children[i], count, suggestions, s);
            s.pop_back();
        }
    }
}


vector<vector<string>> give_suggestion (TrieNode* root, string word){
    TrieNode* temp = root;
    vector<vector<string>> res;
    string s = "";

    for(char ch : word){
        int idx = ch - 'a';

        if(!temp->children[idx]) break;

        temp = temp->children[idx];
        s.push_back(ch);

        vector<string> suggestions;
        int count = 0;

        fetch_words(temp, count, suggestions, s);
        res.push_back(suggestions);
    }

    /* adding empty vectors to res if no suggestions found */

    while(res.size()!=word.size())
        res.push_back(vector<string> ());

    return res;
}


vector<vector<string>> suggestedProducts(vector<string>& products, string searchWord) {
    TrieNode* root = new TrieNode();

    for(string word : products)
        insert(root, word);

    return give_suggestion (root, searchWord);
}
```

#### 3. Replace Words
Given a dictionary consisting of many roots and a sentence consisting of words separated by spaces, replace all the successors in the sentence with the root forming it. If a successor can be replaced by more than one root, replace it with the root that has the shortest length. Return the sentence after the replacement.

Input: dictionary = ["cat","bat","rat"],  sentence = "the cattle was rattled by the battery"

Output: "the cat was rat by the bat"

```cpp
struct TrieNode{
    vector<TrieNode*> children;
    bool isTerminal;

    TrieNode(){
        children = vector<TrieNode*> (26, NULL);
        isTerminal = false;
    }
};


void insert (TrieNode* root, string word){
    TrieNode* temp  = root;

    for(auto ch : word){
        int idx = ch - 'a';

        if(!temp->children[idx])
            temp->children[idx] = new TrieNode();

        temp = temp->children[idx];
    }

    temp->isTerminal = true;
}


string find_rootWord (TrieNode* root, string word){
    TrieNode* temp = root;
    string rootWord = "";

    for(auto ch : word){
        int idx = ch - 'a';

        if(!temp->children[idx])
            return word;

        temp = temp->children[idx];
        rootWord.push_back(ch);

        if(temp->isTerminal)
            return rootWord;
    }

    return rootWord;
}


string replaceWords(vector<string>& dictionary, string sentence) {
    string res = "";
    TrieNode* root = new TrieNode();

    stringstream ss(sentence);  
    string token;
    vector<string> tokens;

    while(getline(ss, token, ' '))
        tokens.push_back(token);

    for(string word : dictionary)
        insert(root, word);

    for(int i=0; i<tokens.size()-1; i++)
        res += find_rootWord(root, tokens[i]) + " ";

    res += find_rootWord(root, tokens.back());
    return res;
}
```

#### 4. Maximum XOR of Two Numbers in an Array
Given an integer array nums, return the maximum result of nums[i] XOR nums[j], where 0 <= i <= j < n.

```cpp
struct TrieNode{
    vector<TrieNode*> children;
    bool isTerminal;

    TrieNode(){
        children = vector<TrieNode*> (2, NULL);
        isTerminal = false;
    }
};


void insert (TrieNode* root, int num){
    TrieNode* temp = root;

    for(int i=31; i>=0; i--){
        int idx = (num>>i) & 1;          //--> checking ith bit of num

        if(!temp->children[idx])
            temp->children[idx] = new TrieNode();

        temp = temp->children[idx];
    }

    temp->isTerminal = true;
}


int find_max_xor (TrieNode* root, int num){
    TrieNode* temp = root;
    int ans = 0;

    for(int i=31; i>=0; i--){
        int idx = (num>>i) & 1;

        /* For making max_xor with num, first preference will be given to opposite bit */

        if(temp->children[!idx]){
            ans = ans | (1<<i);             //--> Setting ith bit of answer to 1
            temp = temp->children[!idx];
        }      

        else temp = temp->children[idx];
    }

    return ans;
}


int findMaximumXOR(vector<int>& nums) {
    TrieNode* root = new TrieNode();
    int XOR = 0;

    for(int n : nums)
        insert(root, n);

    for(int n : nums)
        XOR = max(XOR, find_max_xor(root, n));

    return XOR;
}
```

#### 5. Design Add and Search Words Data Structure
Design a data structure that supports adding new words and finding if a string matches any previously added string. Implement the WordDictionary class in which:

1. WordDictionary() : Initializes the object.
2. void addWord(word) : Adds word to the data structure, it can be matched later.
3. bool search(word) : Returns true if there is any string in the data structure that matches word or false otherwise. word may contain dots '.' where dots can be matched with any letter.

```cpp
struct TrieNode{
    vector<TrieNode*> children;
    bool isTerminal;
    TrieNode (){
        children = vector<TrieNode*> (26, NULL);
        isTerminal = false;
    }
};

TrieNode* root;

WordDictionary() {
    root = new TrieNode();
}

/* --------------------------------------------------------------------------------------- */

void addWord(string word) {
    TrieNode* temp = root;

    for(char ch : word){
        int idx = ch - 'a';

        if(!temp->children[idx])
            temp->children[idx] = new TrieNode();

        temp = temp->children[idx];
    }

    temp->isTerminal = true;
}

/* --------------------------------------------------------------------------------------- */


bool search_helper(string word, int i, TrieNode* temp){
    if(i==word.size()) return temp->isTerminal;

    /* If we get dot at any index i of word, then we will check further in all 26 children */

    if(word[i]=='.'){
        bool found = false;

        for(int j=0; j<26; j++){
            if(temp->children[j]){
                found = search_helper(word, i+1, temp->children[j]);
                if(found) return true;
            }
        }
        return false;
    }

    int idx = word[i]-'a';

    if(temp->children[idx])
        return search_helper(word, i+1, temp->children[idx]);

    return false;
}

bool search(string word) {
    TrieNode* temp = root;
    return search_helper(word, 0, temp);
}
```

#### 6. Stream of Characters
Implement the StreamChecker class as follows:

1. StreamChecker(words): Constructor, init the data structure with the given words.
2. query(letter): returns true if and only if for some k >= 1, the last k characters queried (in order from oldest to newest, including this letter just queried) spell one of the words in the given list.

```cpp
class StreamChecker {
public:
    
    struct TrieNode{
        vector<TrieNode*> children;
        bool isTerminal;
        
        TrieNode(){    
            children = vector<TrieNode*> (26, NULL);
            isTerminal = false;
        }
    };
    
    TrieNode* root;
    string s;
    
    
    /* Insert all words in Trie in reverse order */
    
    void insert(string word){
        TrieNode* temp = root;
        
        for(int i=word.length()-1; i>=0; i--){
            char ch = word[i];
            int idx = ch-'a';
            
            if(!temp->children[idx])
                temp->children[idx] = new TrieNode();
            
            temp = temp->children[idx];
        }
        
        temp->isTerminal = true;
    }
    
    StreamChecker(vector<string>& words) {
        root = new TrieNode();
        for(string word : words)
            insert(word);
    }
    
    bool query(char letter) {
        s.push_back(letter);
        TrieNode* temp = root;

        for(int i=s.length()-1; i>=0; i--){
            char ch = s[i];
            int idx = ch-'a';
            
            if(temp->children[idx])
                temp = temp->children[idx];
            
            else break;
            
            if(temp->isTerminal) return true;
        }
        
        return false;
    }
};

/**
 * Your StreamChecker object will be instantiated and called as such:
 * StreamChecker* obj = new StreamChecker(words);
 * bool param_1 = obj->query(letter);
 */
```


#### 7. Find shortest unique prefix for every word in a given list
Given an array of words, find all shortest unique prefixes to represent each word in the given array. Assume that no word is prefix of another. 

Input: arr[] = {"zebra", "dog", "duck", "dove"}

Output: dog, dov, du, z

Hint: Use one more field for storing word frequency in TrieNode structure

```cpp
struct TrieNode{
    vector<TrieNode*> children;
    bool isTerminal;
    int freq;

    TrieNode(){
        children.assign(26, NULL);
        isTerminal = true;
        freq = 1;
    }
};

void insert(TrieNode* root, string word){
    TrieNode* temp = root;

    for(char ch : word){
        int idx = ch-'a';
        
        if(!temp->children[idx])
            temp->children[idx] = new TrieNode();
        else
            temp->children[idx]->freq++;

        temp = temp->children[idx];
    }

    temp->isTerminal = true;
}

void dfs (TrieNode* root, string & s, vector<string> & res){
    if(root->freq==1){
        res.push_back(s);
        return;
    }

    for(int i=0; i<26; i++){
        if(root->children[i]){
            s.push_back('a'+i);
            dfs (root->children[i], s, res);
            s.pop_back();
        }
    }
}

vector<string> find_all_unique_prefixes (vector<string> & words){
    TrieNode* root = new TrieNode(); 
    root->freq = words.size();

    for(string word : words)
        insert(root, word);

    vector<string> res;
    string s = "";
    dfs (root, s, res);

    return res;
}
```

#### 8. Prefix and Suffix Search (Tricky)
Design a special dictionary with some words that searchs the words in it by a prefix and a suffix. Implement the WordFilter class:

1. WordFilter(string[] words) Initializes the object with the words in the dictionary.
2. f(string prefix, string suffix) Returns the index of the word in the dictionary, which has the prefix prefix and the suffix suffix. If there is more than one valid index, return the largest of them. If there is no such word in the dictionary, return -1.

Approach:
1. Add the word along with all its possible suffixes to trie. (suffix + "#" + word)
2. Now for a [prefix,suffix] pair --> search suffix + "#" + prefix.

```cpp
struct TrieNode{
    vector<TrieNode*> children;
    bool isTerminal;
    int wid;

    TrieNode(){
        children.assign(27, NULL);          // --> last index for '#'
        isTerminal = false;
    }
};

TrieNode* root;

void insert(string word, int wid){
    TrieNode* temp = root;

    for(char ch : word){
        int idx = ch=='#' ? 26 : ch-'a';

        if(!temp->children[idx])
            temp->children[idx] = new TrieNode();

        temp = temp->children[idx];
    }

    temp->isTerminal = true;
    temp->wid = wid;
}


WordFilter(vector<string>& words) {
    root = new TrieNode();
    int wid = 0;
    
    for(string word : words){
        for(int i=0; i<word.length(); i++){
            string suffix = word.substr(i, word.length()-i+1); 
            string s = suffix + "#" + word;
            insert(s, wid);
        }
        wid++;
    }
}

/* dfs will traverse all path below temp node and check for all words and keeps updating mx_wid on reaching every terminal node */

void dfs (TrieNode* temp, int & mx_wid){
    if(temp->isTerminal)
        mx_wid = max(mx_wid, temp->wid);

    for(int i=0; i<26; i++)
        if(temp->children[i])
            dfs (temp->children[i], mx_wid);
}

/* It will serach till suffix + '#' + prefix, after that we will apply dfs on temp node */

int search (string s){
    TrieNode* temp = root;
    int mx_wid = -1;

    for(char ch : s){
        int idx = ch=='#' ? 26 : ch-'a';

        if(!temp->children[idx]) 
            return -1;

        temp = temp->children[idx];
    }

    dfs(temp, mx_wid);
    return mx_wid;
}


int f(string prefix, string suffix) {
    string s = suffix + "#" + prefix;
    return search(s);
}
```

#### 9. Longest Duplicate Substring
Given a string s, consider all duplicated substrings: (contiguous) substrings of s that occur 2 or more times. The occurrences may overlap. Return any duplicated substring that has the longest possible length. If s does not have a duplicated substring, the answer is "".

Input: s = "banana"

Output: "ana"

Hint: Run a binary search over all range of length of substring window. For every mid, find all substrings of size mid, If any substring exists in trie return that string else insert it in trie. Shift start and end ptr of binary search accodingly.

Other Approaches:
1. Using DP (Variation of longest common substring) - Slowest
2. Using Rolling Hash - Fastest

[Video Explaination](https://www.youtube.com/watch?v=FQ8hcOOzQMU)

```cpp
struct TrieNode{
    vector<TrieNode*> children;
    bool isTerminal;

    TrieNode(){
        children.assign(26, NULL);
        isTerminal = false;
    }
};


TrieNode* root;


void insert (string & word){
    TrieNode* temp = root;

    for(char ch : word){
        int idx = ch-'a';

        if(!temp->children[idx])
            temp->children[idx] = new TrieNode();

        temp = temp->children[idx];
    }

    temp->isTerminal = true;
}


bool search (string & word){
    TrieNode* temp = root;

    for(char ch : word){
        int idx = ch-'a';

        if(!temp->children[idx]) 
            return false;

        temp = temp->children[idx];
    }

    return temp->isTerminal;
}


string isDuplicateExist (int & substr_size, string & word){

    /* Find every substring of size substr_size */

    for(int i=0; i<=word.length()-substr_size; i++){
        string sub = word.substr(i, substr_size);

        /* We will search if that substr exists in trie, if yes return true */

        if(search(sub))
            return sub;

        /* If not, insert it into trie and search for other substrings */

        insert(sub);
    }

    return "";
}


/* Simple binary search on length of substrings */

string binarySearch(int start, int end, string & word){
    string ans = "";

    while(start<=end){
        int mid = start + (end-start)/2;
        string sub = isDuplicateExist(mid, word);

        if(sub!=""){
            ans = sub;
            start = mid+1;
        }

        else end = mid-1;    
    }

    return ans;
}


string longestDupSubstring(string s) {
    root = new TrieNode();
    return binarySearch(1, s.length(), s);
}
```


#### 10. Concatenated Words (Tricky)
Given an array of strings words (without duplicates), return all the concatenated words in the given list of words. A concatenated word is defined as a string that is comprised entirely of at least two shorter words in the given array.

Input: words = ["cat","cats","catsdogcats","dog","dogcatsdog","hippopotamuses","rat","ratcatdogcat"]

Output: ["catsdogcats","dogcatsdog","ratcatdogcat"]

Hint: Iterate for whole word, if we get any terminal node in between then checks whether the remaining part is complete word OR concatenated word. 

```cpp
struct TrieNode{
    vector<TrieNode*> children;
    bool isTerminal;

    TrieNode(){
        children.assign(26, NULL);
        isTerminal = false;
    }
};

/* Simply insert given word in trie */

void insert(TrieNode* root, string word){
    TrieNode* temp = root;

    for(char ch : word){
        int idx = ch-'a';

        if(!temp->children[idx])
            temp->children[idx] = new TrieNode();

        temp = temp->children[idx];
    }

    temp->isTerminal = true;
}


/* It checks whether the subpart of given word (starts from index i) exists in trie or not */

bool isWord (TrieNode* root, string word, int start){
    if(start>=word.size()) return false;
    TrieNode* temp = root;

    for(int i=start; i<word.size(); i++){
        char ch = word[i];
        int idx = ch-'a';

        if(!temp->children[idx]) 
            return false;

        temp = temp->children[idx];
    }

    return temp->isTerminal;
}

/* It will check whether the given word is concatenated or not*/

bool isConcatenatedWord (TrieNode* root, string word, int start){
    if(start>=word.size()) return false;
    TrieNode* temp = root;

    /* Simple iterate through whole word from index start */

    for(int i=start; i<word.length(); i++){
        char ch = word[i];
        int idx = ch-'a';

        if(!temp->children[idx])
            return false;

        temp = temp->children[idx];

        /* 
            If we reach some terminal node, then check whether the remaining part is complete word
            OR a concatenated word that lies in trie
        */

        if(temp->isTerminal)
            if(isWord(root, word, i+1) or isConcatenatedWord(root, word, i+1))
                return true;

    }

    return false;
}


vector<string> findAllConcatenatedWordsInADict(vector<string>& words) {
    TrieNode* root = new TrieNode();
    vector<string> res;

    for(string word : words)
        insert(root, word);

    for(string word : words)
        if(isConcatenatedWord(root, word, 0))
            res.push_back(word);

    return res;
}
```

#### 11. Palindrome Pairs (Tricky)
Given a list of unique words, return all the pairs of the distinct indices (i, j) in the given list, so that the concatenation of the two words words[i] + words[j] is a palindrome.

```
Approach: Following are the two cases for making palindrome pairs:

Case 1: Whole rev(word1) matches with suffix of word2 (such that len(word2) >= len(word1))
Eg1: word1 = lls, word2 = absll 
Eg2: word1 = abcd, word2 = dcba

We will handle this case by doing DFS call for remaining part of word2 and check whether it is palindrome or not. 
(Empty string will be treated as palindrome, like in Eg2)

Case 2: rev(word1) matches with suffix of whole word2 (such that len(word1) >= len(word2))
Eg1: word1 = abbab, word2 = ba

We will handle this case by simply checking whether the remaining part of word1 is palindrome or not.
```

```cpp
struct TrieNode{
    vector<TrieNode*> children;
    bool isTerminal;
    int wordIndex;        // --> It will store the index of word in input array

    TrieNode(){
        children.assign(26, NULL);
        isTerminal = false;
    }
};

/* ------------------------------------------------------------------------------------------------ */

/* Below function will simply insert the word in reverse order in trie */

void insert (TrieNode* root, string & word, int & wordIndex){
    TrieNode* temp = root;

    for(int i=word.length()-1; i>=0; i--){
        char ch = word[i];
        int idx = ch-'a';

        if(!temp->children[idx])
            temp->children[idx] = new TrieNode();

        temp = temp->children[idx];
    }

    temp->isTerminal = true;
    temp->wordIndex = wordIndex;
}

/* ------------------------------------------------------------------------------------------------ */

/* This helper function check whether string s in palindrome or not */

bool isPalindrome (string & s){ 
    int i=0, j=s.length()-1;

    while(i<j){
        if(s[i]!=s[j]) return false;
        i++; j--;
    }

    return true;
}

/* ------------------------------------------------------------------------------------------------ */

/* It performs dfs on all paths starting from temp node and store all pairs in vector */

void dfs (TrieNode* temp, string & s, vector<vector<int>> & pairs, string & word, int & first_idx){

    /* 
        if temp is a terminal node and string 's' is palindrome, then this word forms a pair 
        's' is a remaining part of the second word after removing the suffix (similar to the first word)
    */

    if(temp->isTerminal and isPalindrome(s)){

        /* Checking if first and second word are not same */

        if(temp->wordIndex != first_idx){
            pairs.push_back({first_idx, temp->wordIndex});

            /* Boundary Case : when first word is empty */

            if(word=="") pairs.push_back({temp->wordIndex, first_idx});
        }
    }

    /* Doing DFS on its children */

    for(int i=0; i<26; i++){

        if(temp->children[i]){
            s.push_back('a'+i);
            dfs(temp->children[i], s, pairs, word, first_idx);
            s.pop_back();
        }            
    }
}

/* ------------------------------------------------------------------------------------------------ */

vector<vector<int>> search_pair(TrieNode* root, string & word, int & first_idx){
    TrieNode* temp = root;
    vector<vector<int>> pairs;
    string s = "";
    int wlen = word.length();

    for(int i=0; i<word.length(); i++){
        char ch = word[i];
        int idx = ch-'a';

        /* 
            Case 2: If we found any terminal node in between and if the remaining part of word1 is palindrome
            then it will also be added as pair.
        */

        if(temp->isTerminal){
            string sub = word.substr(i, wlen-i+1);

            if(isPalindrome(sub) and temp->wordIndex!=first_idx)
                pairs.push_back({first_idx, temp->wordIndex});
        }

        /* If there is no suffix matches with the word simply return pairs */

        if(!temp->children[idx]) 
            return pairs;

        temp = temp->children[idx];
    }

    /* Case 1: If we found a suffix matches with the whole word1, do DFS */

    dfs (temp, s, pairs, word, first_idx);
    return pairs;
}

/* ------------------------------------------------------------------------------------------------ */

vector<vector<int>> palindromePairs(vector<string>& words) {
    TrieNode* root = new TrieNode();
    vector<vector<int>> res;

    for(int i=0; i<words.size(); i++)
        if(words[i].size()>0) 
            insert(root, words[i], i);

    for(int i=0; i<words.size(); i++){
        auto pairs = search_pair (root, words[i], i);
        for(auto p : pairs)
            res.push_back(p);
    }

    return res;
}
```
