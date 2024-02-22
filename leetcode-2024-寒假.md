```
二分法:
我的:
执行时间约为 18ms

class Solution {
public:
    int search(vector<int>& nums, int target) {
        int bigger = nums.size()-1;
        int smaller = 0;

        while (!(nums.at(bigger) == target || nums.at(smaller) == target)) {
            if (abs(bigger - smaller) <= 1) { return -1; }
            int med = (bigger + smaller) / 2;
           /* cout << med << endl;*/

            if (target > nums.at(med)) { smaller = med; }
            else { bigger = med; }
        }
        if (nums.at(bigger) == target) { return bigger; }
        else { return smaller; }
    }
};

所给的代码:
执行时间约为27ms

public:
    int search(vector<int>& nums, int target) {
        int left = 0;
        int right = nums.size(); // 定义target在左闭右开的区间里，即：[left, right)
        while (left < right) { // 因为left == right的时候，在[left, right)是无效的空间，所以使用 <
            int middle = left + ((right - left) >> 1);
            if (nums[middle] > target) {
                right = middle; // target 在左区间，在[left, middle)中
            } else if (nums[middle] < target) {
                left = middle + 1; // target 在右区间，在[middle + 1, right)中
            } else { // nums[middle] == target
                return middle; // 数组中找到目标值，直接返回下标
            }
        }
        // 未找到目标值
        return -1;
    }
};


可以吸收的点:
1.int middle = left + ((right - left) >> 1);     -》该计算方式比单纯的使用除法慢
计算中值的时候获取这种方法比单纯的除法快
2.从结构来看给的代码还简单一点，为什么我的快一点：
数据量的问题
```



```
35.搜索插入位置-》与二分法关联
我的3ms，太慢
基本逻辑结构:
1.特殊情况长度为1->：二值比较
2.其它：
	1.不在区间:
	2.在区间->使用二分法
	
class Solution {
public:
    int searchInsert(vector<int>& nums, int target) {
    int bigger = nums.size()-1;
    int smaller = 0;
    //考虑数据在区间外
    if(nums.size()==1){
        if(target>nums[0]){return 1;}
        else{return 0;}
    }
    else{

        if(nums[bigger]<target){return bigger+1;}
        else if(nums[smaller]>target){
            return smaller;}
        else{
    //以下是确保在区间内
             while (!(nums.at(bigger) == target || nums.at(smaller) == target)) {
                if (abs(bigger - smaller) <= 1) {
                    return smaller+1;
                 }
                int med = (bigger + smaller) / 2;
                if (target > nums.at(med)) { smaller = med; }
                else { bigger = med; }
                }
            if (nums.at(bigger) == target) { return bigger; }
            else { return smaller; }
            }
        }
    }
};

//出现错误：排错
public:
    int searchInsert(vector<int>& nums, int target) {
        int bigger = nums.size() - 1;
        int smaller = 0;
        //考虑数据在区间外
        cout << bigger << endl;
        if (target <= nums[smaller]) { return 0; }
        else if (target >= nums[bigger]) { return bigger + 1; }
        else {
            cout << bigger << endl;
            while (bigger > smaller){
                if (nums[bigger] == target || nums[smaller] == target) {
                    if (nums[bigger] == target)return bigger;
                    else return smaller;
                }
                int med = (bigger + smaller) / 2;
                cout << med << endl;

                if (nums[med] >= target) { bigger = med; }
                else {smaller=med;}
            }
        }

        cout << endl;
        return smaller;
    };
};

官方：
1.二分法
2.暴力查找(直接比较):
class Solution {
public:
    int searchInsert(vector<int>& nums, int target) {
        int n = nums.size();
        int left = 0;
        int right = n - 1; // 定义target在左闭右闭的区间里，[left, right]
        while (left <= right) { // 当left==right，区间[left, right]依然有效
            int middle = left + ((right - left) / 2);// 防止溢出 等同于(left + right)/2
            if (nums[middle] > target) {
                right = middle - 1; // target 在左区间，所以[left, middle - 1]
            } else if (nums[middle] < target) {
                left = middle + 1; // target 在右区间，所以[middle + 1, right]
            } else { // nums[middle] == target
                return middle;
            }
        }
        // 分别处理如下四种情况
        // 目标值在数组所有元素之前  [0, -1]
        // 目标值等于数组中某一个元素  return middle;
        // 目标值插入数组中的位置 [left, right]，return  right + 1
        // 目标值在数组所有元素之后的情况 [left, right]， 因为是右闭区间，所以 return right + 1
        return right + 1;
    }
};
```



```
34.在排序数组中查找元素的第一个和最后一个位置
我的：
class Solution {
public:

    vector<int> searchRange(vector<int>& nums, int target){
        int leftBorder = getLeftBorder(nums, target);
        int rightBorder = getRightBorder(nums, target);
        // 情况一
        if (leftBorder == -2 || rightBorder == -2) return { -1, -1 };
        // 情况三
        if (rightBorder - leftBorder > 1) return { leftBorder + 1, rightBorder - 1 };
        // 情况二
        return { -1, -1 };
    };

    int getLeftBorder(vector<int>& nums, int target) {
        int left = 0;
        int right = nums.size() - 1; // 定义target在左闭右闭的区间里，[left, right]
        int leftBorder = -2; // 记录一下leftBorder没有被赋值的情况
        while (left <= right) {
            int middle = left + ((right - left) / 2);
            if (nums[middle] >= target) { // 寻找左边界，就要在nums[middle] == target的时候更新right
                right = middle - 1;
                leftBorder = right;
            }
            else {
                left = middle + 1;
            }
        }
        return leftBorder;
    }

    int getRightBorder(vector<int>& nums, int target) {
        int left = 0;
        int right = nums.size() - 1; // 定义target在左闭右闭的区间里，[left, right]
        int rightBorder = -2; // 记录一下rightBorder没有被赋值的情况
        while (left <= right) { // 当left==right，区间[left, right]依然有效
            int middle = left + ((right - left) / 2);// 防止溢出 等同于(left + right)/2
            if (nums[middle] > target) {
                right = middle - 1; // target 在左区间，所以[left, middle - 1]
            }
            else { // 当nums[middle] == target的时候，更新left，这样才能得到target的右边界
                left = middle + 1;
                rightBorder = left;
            }
        }
        return rightBorder;
    }
};


最优的：


解法1：左右分别二分法找左右交点
class Solution 
{
public:
    vector<int> searchRange(vector<int>& nums, int target) 
    {
        if(nums.size()==0) return {-1,-1};
        int begin=0;
        //1.二分求左端点
        int left=0,right=nums.size()-1;
        while(left<right)
        {
            int mid=left+(right-left)/2;
            if(nums[mid]<target) left=mid+1;
            else right=mid;
        } //最后left和right会指向同一点，该点为左端点
        //判断是否有结果
        if(nums[left] != target) return {-1,-1};
        begin=left;  //记录左端点

        //2.二分求右端点
        left=begin,right=nums.size()-1;
        while(left<right)
        {
            int mid=left+(right-left+1)/2;   
            //"left+(right-left+1)/2" 与"left+(right-left)/2"仅在个数为偶数时结果不一样，一个算出左边的，一个算出右边的
            if(nums[mid]<=target) left=mid;
            else if(nums[mid]>target) right=mid-1; 
        }
        return {begin,right};
    }
};
```



```
69.x的平方根

方法1:
袖珍计算器算法：将表达式转化为其它函数
class Solution {
public:
    int mySqrt(int x) {
        if(x==0)return 0;
        int y=exp(0.5*log(x));
        return ((long long)(y+1)*(y+1)<=x ? y+1:y);
    }
};

二分法查找：下界为0，上界为x
我的:
class Solution {
public:
    int mySqrt(int x) {
        int min = 0;
        int max = x;

        while (max - min >= 1) {
            int mid = (min + max) / 2;
            cout << min<<" ";
            cout << mid <<" ";
            cout << max << endl;
            if ((min*mid) == x || (max^2) == x) {
                if ((min*mid) == x)return min;
                else return max;
            }
            if (x< (mid*mid)) { max = mid; }
            else { min = mid; }
        }
        return min;
    }
};

改的：
class Solution {
public:
    int mySqrt(int x) {
        int min = 0;
        int max = x;
        int ans=-1;
        while (max>=min) {
            int mid = (min + max) / 2;
            if (x>=(long long)mid*mid){min=mid+1;ans=mid;}
            else {max=mid-1;}
        }
        return ans;
    }
};



给的：
class Solution {
public:
    int mySqrt(int x) {
        int min=0;
        int max=x;
        int ans=-1;
        while(max>=min){
            int mid=(min+max)/2;
            if(x>=(long long)mid*mid){min=mid+1;ans=mid;}
            else{max=mid-1;}
        }
        return ans;
    }
};
```



```
27. 移除元素
方法1：
将元素与要求元素比较然后移动
->失败
-》未考虑位移元素后仍然是要求比较元素
->适用于无重复元素

class Solution {
public:
    int removeElement(vector<int>& nums, int val) {
        int num = 0;
        int i = 0;
        while (i != nums.size()-num) {
            Print_vec(nums);
            cout << nums[i] << endl;
            cout << i << endl;
            cout << num << endl;

            if (nums[i] == val) {num++;}
            nums[i] = nums[i + num];
            if (nums[i] != val) {i++;}
            Print_vec(nums);
        }
        for (int i = 0; i < num+1; i++) {
            nums.pop_back();
        }
        Print_vec(nums);
        return nums.size();
    }

};

给的：
相似的思路
相当于建立了一个新的数组来存，但是这个新的数组是使用了原来的数组存储空间
class Solution {
public:
    int removeElement(vector<int>& nums, int val) {
        int i = 0;
        for(int j = 0;j < nums.size();j++)
        {
            if(nums[j] != val)
            nums[i++] = nums[j];
        }
        return i;
    }
};


方法二：
从后往前遍历，如果是则与末尾交换，然后删除，末尾值保留到原位置。
在电脑上成功但是与网站的输出结果不同
class Solution {
public:
    int removeElement(vector<int>& nums, int val){
        int num = 0;
        for (int i = nums.size()-1; i >= 0; i--) {
            if (nums[i] == val) {
                num++;
                nums[i] = nums[nums.size()-1];
                nums.pop_back();
            }
        }
        return num;
    };
};
```



```
26.删除有序数组中的重复项

我的：
使用双指针的方法
寻找边界：（保留边界的左边），存到慢指针位置，然后最终剩余的右边界插入到慢指针位置

class Solution {
public:
    int removeDuplicates(vector<int>& nums){
        int j = 0;
        if(nums.size()<=1){return nums.size();}
        for (int i = 0; i <= nums.size() - 2; i++) {
            if (nums[i] != nums[i+1]) {
                nums[j++] = nums[i];
            }
        }
        nums[j] = nums[nums.size()-1];
        return j+1;
    }
};

给的：也是双指针，但是核心思想更简单

class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
        int idx1 = 1;
        for(int idx2 = 1; idx2 < nums.size(); ++idx2) {
            if(nums[idx2] != nums[idx1 - 1]) {
                nums[idx1++] = nums[idx2];
            }
        }
        return idx1;
    }
};

我写的：与给的比较
class Solution {
public:
    int removeDuplicates(vector<int>& nums){
        int j = 1;
        for(int i=0;i<nums.size();i++){
            if(nums[j-1] != nums[i]){
                nums[j++]=nums[i];
            }
        }
        return j;
    }
};

```



```
283.移动零

我的
使用双指针的方法：
在最后将末尾剩余数全置零；
class Solution {
public:
    void moveZeroes(vector<int>& nums) {
        int j = 0;
        for (int i = 0; i < nums.size(); i++) {
            if (nums[i]) {
                nums[j++] = nums[i];
            }
        }
        for (int i = 0; i < nums.size()-j; i++) {
            nums[nums.size() - i-1] = 0;
        }
    }
};


给的：
class Solution {
public:
    void moveZeroes(vector<int>& nums) {
        int left = 0, right = 0;
        while(right < nums.size()){
            if(nums[right]){
                swap(nums[left],nums[right]);
                left++;
            }
            right++;
        }
    }
};

SWAP函数
```



```
844.比较含退格的字符串

我的：

在遇到连续的退格时会出现问题

    bool backspaceCompare(string s, string t) {
        int j = t.size() - 1;
        int i = s.size() - 1;
        while(i >= 0 && j >= 0){
            if (s[i] == '#'){
        
                i -= 2; }
            if (t[j] == '#') { j -= 2; }
            if (s[i] == t[j]) {j--; i--;}
            else {
                if (t[j] == '#' || s[i] == '#')continue;
                else return false;
            }
            cout << i << " " << j << endl;
        }
        if (j >= 0||i>=1)return false;
        return true;
    };
    
新方法：
        int j = t.size() - 1;
        int i = s.size() - 1;

        while (i < 0 || j < 0) {
            if (s[i] = '#') {
                int k = 0;
                while (s[i - k] == '#') {
                    k++;
                }
                i = i - 2 * k;
                continue;
            }
            if (t[j] = '#') {
                int k = 0;
                while (t[j - k] == '#') {
                    k++;
                }
                j = j - 2 * k;
                continue;
            }
            if (t[j] == s[i]) { j--; i--; }
            else { return false; }
            cout << i << " " << j << endl;
        }
        if (i < 0 && j < 0) {
            return true;
        }
        else {
            return false;
        }
        
给的：
class Solution {
public:
    bool backspaceCompare(string S, string T) {
        int i = S.length() - 1, j = T.length() - 1;
        int skipS = 0, skipT = 0;

        while (i >= 0 || j >= 0) {
            while (i >= 0) {
                if (S[i] == '#') {
                    skipS++, i--;
                } else if (skipS > 0) {
                    skipS--, i--;
                } else {
                    break;
                }
            }
            while (j >= 0) {
                if (T[j] == '#') {
                    skipT++, j--;
                } else if (skipT > 0) {
                    skipT--, j--;
                } else {
                    break;
                }
            }
            if (i >= 0 && j >= 0) {
                if (S[i] != T[j]) {
                    return false;
                }
            } else {
                if (i >= 0 || j >= 0) {
                    return false;
                }
            }
            i--, j--;
        }
        return true;
    }
};

作者：力扣官方题解

使用堆栈：
class Solution {
public:
    bool backspaceCompare(string S, string T) {
        return build(S) == build(T);
    }

    string build(string str) {
        string ret;
        for (char ch : str) {
            if (ch != '#') {
                ret.push_back(ch);
            } else if (!ret.empty()) {
                ret.pop_back();
            }
        }
        return ret;
    }
};
```



```
977.有序数组的平方

我的：
假设数组是可分的：可分为正负两部分
缺点：只能处理可分以及长度为1的数组
vector<int> sortedSquares(vector<int>& nums) {
        int j=0;
        int i=0;
        vector<int> new_nums;
        if(nums.size()<2){new_nums.push_back(nums[0]*nums[0]);return new_nums;}
        while(nums[i]<=0){i++;}
        j=i-1;
        while(i<nums.size()){
            if(j>=0){
                if(nums[i]+nums[j]>0){new_nums.push_back(nums[j]*nums[j]);j--;}
                else if(nums[i]+nums[j]==0)
                    {new_nums.push_back(nums[i]*nums[i]);
                    new_nums.push_back(nums[i]*nums[i]);
                    i++;
                    j--;}
                else{
                    new_nums.push_back(nums[i]*nums[i]);i++;
                }
            }
            else{
                break;
            }
        }
        while(i<nums.size())
        {new_nums.push_back(nums[i]*nums[i]);
            i++;}
        while(j>=0)
        {new_nums.push_back(nums[j]*nums[j]);
            j--;}
        return new_nums;
    };

    
二：
    vector<int> sortedSquares(vector<int>& nums) {
        int i=0;
        int j=0;
        vector<int> new_nums;
        for(;i<nums.size() && nums[i]>0;i++);
        i--;
        if(!i){new_nums.push_back(nums[i]*nums[i]);return new_nums;}
        else if(i==nums.size()-1){
            for(int j=0;j<=i;j++)
            new_nums.push_back(nums[j]*nums[j]);
            return new_nums;
        }
        else{
            j=i-1;
            while(new_nums.size()!=nums.size()){
                if(j==-1){
                    while(i<nums.size()){
                        new_nums.push_back(nums[i]*nums[i]);
                        i++;
                    }
                    break;
                }
                if(i==nums.size()){
                     while(j>=0){
                        new_nums.push_back(nums[j]*nums[j]);
                        j--;
                    }
                    break;
                }
                if(nums[i]+nums[j]>0){new_nums.push_back(nums[j]*nums[j]);j--;}
                else if(nums[i]+nums[j]==0){new_nums.push_back(nums[j]*nums[j]);j--;
                new_nums.push_back(nums[j]*nums[j]);i++;
                }
                else{
                    new_nums.push_back(nums[i]*nums[i]);i++;
                }
            }
        }
        return new_nums;
    };
    
    
改正确：
class Solution {
public:
    vector<int> sortedSquares(vector<int>& nums) {
        int i=0;
        int j = 0;
        vector<int> new_nums;
        while (i < nums.size()) { if (nums[i] <= 0)i++; else{break;}}
        if (!i) {
            while (i < nums.size()) {
                new_nums.push_back(nums[i] * nums[i]);
                i++;
            }
        }
        else if (i == nums.size()) {
            for (int j = i-1; j >= 0; j--)
                new_nums.push_back(nums[j] * nums[j]);
            return new_nums;
        }
        else {
            j = i - 1;
            while (new_nums.size() != nums.size()) {
                if (j == -1) {
                    while (i < nums.size()) {
                        new_nums.push_back(nums[i] * nums[i]);
                        i++;
                    }
                    break;
                }
                if (i == nums.size()) {
                    while (j >= 0) {
                        new_nums.push_back(nums[j] * nums[j]);
                        j--;
                    }
                    break;
                }
                if (nums[i] + nums[j] > 0) {new_nums.push_back(nums[j] * nums[j]); j--; }
                else if (nums[i] + nums[j] == 0) {
                    new_nums.push_back(nums[j] * nums[j]); 
                    new_nums.push_back(nums[j] * nums[j]);
                    j--;
                    i++;
                }
                else {
                    new_nums.push_back(nums[i] * nums[i]); i++;
                }
            }
        }
        return new_nums;
    };
    
};


我写的：
两头遍历：
class Solution {
public:
    vector<int> sortedSquares(vector<int>& nums) {
        int i=0;
        int j=nums.size()-1;
        int g=nums.size()-1;
        vector<int> new_nums=nums;
        while(i<j){
            int k=nums[j]*nums[j]-nums[i]*nums[i];
            if(k>0){
                new_nums[g]=nums[j]*nums[j];
                j--;
            }
            else if(k==0){
                new_nums[g--]=nums[i]*nums[i];
                new_nums[g]=nums[i]*nums[i];
                i++;
                j--;
            }
            else{
                new_nums[g]=nums[i]*nums[i];
                i++;
            }
            g--;
        }
        new_nums[0]=nums[i]*nums[i];
        return new_nums;
    };
    
};
```



```
209.长度最小的子数组

我的：
1.暴力解法
class Solution {
public:
    int minSubArrayLen(int target, vector<int>& nums) {
        int result=INT32_MAX;
        int sum=0;
        int sublength=0;
        for(int i=0;i<nums.size();i++){
            sum=0;
            for(int j=i;j<nums.size();j++)
            {
                sum+=nums[j];
                if(sum>=target){
                    sublength=j-i+1;
                    result=result<sublength ? result:sublength;
                    break;
                }
            }
        }
        return result==INT32_MAX? 0:result;
    }
};
2.滑动框
我的：
部分案例错误，-》思路有错

    int minSubArrayLen(int target, vector<int>& nums) {
        int sum = 0;
        int sublength = nums.size();
        int i = 0;
        int j = 0;
        while (i < nums.size()) {
            cout << i << " ";
            cout << j << " ";

            cout << sublength << " ";
            cout << sum << " "<<endl;
            if (sum < target) {
                sum += nums[i];
                i++;
            }
            else {
                sum = 0;
                if (sublength > (i - j)) { sublength = (i - j); }
                i--;
                j = i;
            }
        }
        if (sum >= target){
            if (sublength > (i - j)) { sublength = (i - j); }
        }
        if (sublength == nums.size() && i == nums.size()) {return 0; }
        return sublength;
    };

看了思路后重新码的：->通过但效率低
 int minSubArrayLen(int target, vector<int>& nums) {
        int start = 0;
        int end = 0;
        int sum = 0;
        int min_len =-1;
        while (end < nums.size()) {
            sum += nums[end];
            if (sum < target) {
                end++;
            }
            else {
                if (min_len > (end-start+1) || min_len==-1) { min_len = end - start+1;}
                while (sum >= target && start<=end) {
                    sum -= nums[start];
                    if (min_len > (end - start + 1) || min_len == -1) { min_len = end - start + 1; }
                    start++;
                }
                end++;
            }
        }
        if (min_len ==-1) return 0;
        return min_len;
    }；
    
->还有前缀和+二分查找的方式没有看
```



```
水果成篮

我的：
class Solution {
public:
    int totalFruit(vector<int>& fruits) {
               vector<int> Lan = { -1,-1 };//用来存放水果的篮子
       int start = 0; //采集的起始树
       int end = 0; //采集的最终树
       int same_t = 0;//结尾连续种类树的前端
       int same_pos = -1;
       int max_len = -1;//最大的水果数目
       while (end < fruits.size()) {
           //检查目前水果种类篮子里是否存在
           //以上均是假设水果的种类大于等于二
           //存在
/*           cout << Lan[0] <<" "<<Lan[1] << endl;
           cout << start << " " << same_pos << " " << end <<" "<<':'<< max_len << endl;
           cout << endl;*/

           if (fruits[end] == Lan[0] || fruits[end] == Lan[1]){
               //与第一类水果种类相同
               if (fruits[end] == Lan[0]) {
                   //检查前一位水果是否存在
                   //存在
                       //需要更新连接水果的种类以及位置
                       //前一个水果的种类与记录的连续水果不同
                   if (fruits[end] != same_t) {
                       same_t = fruits[end];
                       same_pos = end;
                   }
                   //不存在
               }
               //与第二种水果相同
               //只需要更新连续的种类以及数量
               else {
                   //需要更新连接水果的种类以及位置
                   //前一个水果的种类与记录的连续水果不同
                   if (fruits[end] != same_t) {
                       same_t = fruits[end];
                       same_pos = end;
                   }
               }
           }
           //目前的篮子中的种类都不同
           else {
               //篮子中是否二种种类齐全
               if (Lan[1] == -1) {
                   //第一种以及还未齐全
                   if (Lan[0] == -1) {
                       Lan[0] = fruits[end];
                       same_t = Lan[0];
                       same_pos = end;
                   }
                   //第二种还未齐全
                   else {
                       Lan[1] = fruits[end];
                       //检查目前的连续种类所在种类以及位置并以此为根据更新
                       //篮子中的种类以及位置
                       if (fruits[end] != fruits[same_pos]) {
                           same_pos = end;
                           same_t = fruits[end];

                       }
                   }
               }
               //两种种类均已齐全
               //根据连续种类以及位置更新位置同时确定长度是否保存
               else {
                   //对比长度
                   int k = end - start;
                   if (k > max_len) { max_len = k; };
                   //更新位置以及种类
                   start = same_pos;
                   //更新篮子中的种类
                   if (Lan[0] == same_t) {
                       Lan[1] = fruits[end];
                   }
                   else {
                       Lan[0] = fruits[end];
                   }
                   //更新连续种类以及数量
                   same_t = fruits[end];
                   same_pos = end;
               }
           }
           //来确定最大长度有没有更新
           //
           end++;

       }
       //来确定最大长度有没有更新
       //更新则返回，未更新来确定未更新情况
       if (max_len == -1) {
           if (Lan[0] == -1) { return 0; }
           else {
               return fruits.size();
           }
       }
       else {
           if (end - start> max_len) {
               return end - start;
           }
           else {
               return max_len;
           }
       };
    };
};

给的解答：
使用移动窗口+哈希表
C++容器中的unordered_map 哈希映射
1.关联容器，用于存储键值以及映射值组合而成的元素，键值唯一地标识元素
二映射值是与该键值关联的对象

函数:
1.大小->size():容器大小 empty:判断空
2.访问元素:operator[] + at
3.元素查询:find 找到键值为指定值的迭代器，count 统计关键子出现次数,有为1，没有为0
4.erase:擦除元素
5.clear:清除内容

auto:关键字用来自动识别变量的类型

class Solution {
public:
    int totalFruit(vector<int>& fruits) {
        //使用哈希表加移动框进行计算
        unordered_map<int,int> Lan;
        int left=0;
        int ans=0;
        int n=fruits.size();
        for(int right=0;right<n;++right){
            ++Lan[fruits[right]];
            while(Lan.size()>2){
                auto it=Lan.find(fruits[left]);
                --it->second;
                if(it->second==0){
                    Lan.erase(it);
                }
                ++left;
            }
            ans=max(ans,right-left+1);
        }
        return ans;
    };
};

我写的：
class Solution {
public:
    int totalFruit(vector<int>& fruits) {
        //使用哈希表加移动框进行计算
        unordered_map<int,int> Lan;
        int left=0;
        int max_length=0;
        int length=fruits.size();
        for(int right=0;right<length;++right){
            ++Lan[fruits[right]];
            while(Lan.size()>2){
                auto it=Lan.find(fruits[left]);
                --it->second;
                if(it->second==0){
                    Lan.erase(it);
                }
                ++left;
            }
            max_length=max(max_length,right-left+1);
        }
        return max_length;
    };
};
```



```
76.最小覆盖字串

我的：某些特殊情况无法通过：

class Solution {
public:
    string minWindow(string s, string t) {
           //利用哈希表+滑动框的方法求解
    //记录字符以及出现的位置
    string result = "";   //用来存储遍历字符串的位置
    int left = 0;         //用来存储左边界
    int s_length = s.size();
    int t_length = t.size();
    bool if_manzhu = false;
    unordered_map<char, int> hash_target; //统计s中字符出现的次数
    unordered_map<char, int> hash_compare;//统计t中字符出现的次数

    vector<int> subs_boundary = {-1,-1 }; //记录边界
    int min_length = INT_MAX;             //最小的长度

    //要查找的字符比目标字符长
    if (t.size() > s.size()) return result;

    //开始统计t中字符出现的次数
    for (int right = 0; right < t_length; ++right) {
        ++hash_compare[t[right]];}


    //按字符串逐个进行扫描
    for (int right = 0; right < s_length; ++right){
        //扫描到的字符是目标字符串
        cout << right << endl;
        cout << left << " " << right << endl;
        cout << subs_boundary[0] <<" "<< subs_boundary[1] << endl;
        cout << min_length << endl;
        cout << endl;

        if (hash_compare.count(s[right])) {
            ++hash_target[s[right]];
            if (!left) {                                 //left最开始的初始化
                if (!hash_compare.count(s[left])) {
                    left = right;
                }
            }
            if (hash_compare.size() == hash_target.size()) {                         //判断是否字符种类齐全
                for (auto i = hash_target.begin(); i != hash_target.end(); ++i) {    // 判断数量是否正确
                    if (hash_compare[i->first] > i->second) { if_manzhu = true; break; }                 //存在某一个数组的数量不满足要求
                }
                if (!if_manzhu) {

                    int k = right - left + 1;                                          //数量上满足要求
                    if (k < min_length) {
                        subs_boundary[0] = left;
                        subs_boundary[1] = right;
                        min_length = k;
                    }
                    --hash_target[s[left]];

                    ++left;
                    while (!hash_compare.count(s[left]) && left < right) {
                        ++left;
                    }
                    if (right == s_length - 1) {
                        if (hash_target.count(s[right]) && left < right) {
                            subs_boundary[0] = left;
                            subs_boundary[1] = right;
                        }

                    }

                }
                if_manzhu = false;
            }
            cout << right << endl;
            cout << left << " " << right << endl;
            cout << subs_boundary[0] << " " << subs_boundary[1] << endl;
            cout << min_length << endl;
            cout << endl;
        }
    }
        if (subs_boundary[0]!=-1 && t_length == 1) { result = t; }
        else if (subs_boundary[0] != -1) { result = s.substr(subs_boundary[0], subs_boundary[1]+ 1);}            //边界未更新，说明：未找到种类以及对应的数量符合条件的边界返回空字符串;
    return result;
    };
};

给的：
方法：滑动窗口

class Solution {
public:
    unordered_map <char, int> ori, cnt;

    bool check() {
        for (const auto &p: ori) {
            if (cnt[p.first] < p.second) {
                return false;
            }
        }
        return true;
    }

    string minWindow(string s, string t) {
        for (const auto &c: t) {
            ++ori[c];
        }

        int l = 0, r = -1;
        int len = INT_MAX, ansL = -1, ansR = -1;

        while (r < int(s.size())) {
            if (ori.find(s[++r]) != ori.end()) {
                ++cnt[s[r]];
            }
            while (check() && l <= r) {
                if (r - l + 1 < len) {
                    len = r - l + 1;
                    ansL = l;
                }
                if (ori.find(s[l]) != ori.end()) {
                    --cnt[s[l]];
                }
                ++l;
            }
        }

        return ansL == -1 ? string() : s.substr(ansL, len);
    }
};


改了还是有bug,直接参照参考答案

    string minWindow(string s, string t) {
        //滑动串口+哈希表
        
        unordered_map<char,int> hash_target;
        unordered_map<char,int> hash_compare;
        int left=0;
        int s_length=s.size();
        int t_length=t.size();
        int min_length=INT_MAX;
        int flag=false;
        int str_left=-1;
        int str_right=-1;
        if(s_length<t_length) return "";

        for(int i=0;i<t_length;++i){
            ++hash_compare[t[i]];
        }
        for(int right=0;right<s_length;++right){
            cout<<left<<" "<<right<<endl;
            cout<<str_left<<" "<<str_right<<endl;
            cout<<endl;
            ++hash_target[s[right]];
            if(hash_compare.count(s[right])){
                for(auto it=hash_compare.begin();it!=hash_compare.end();it++)
                {
                    if(hash_target[it->first] < it->second){
                        flag=false;
                        break;
                    }
                    flag=true;
                }
                if(flag){
                    if(!left && !hash_compare.count(s[left]))
                    {left=right;}

                    int k=right-left+1;
                    if(k<min_length){
                        str_left=left;
                        str_right=right;
                        min_length=k;
                    }
                    do
                        {--hash_target[s[left]];
                        if(left>=right){break;}
                        left++;
                        }while(left<right && !hash_compare.count(s[left]));
                }

                if(hash_compare.count(s[right]) && hash_compare.count(s[left]) && right==s_length-1)
                {
                    int k=right-left+1;
                    if(k<min_length){
                        str_left=left;
                        str_right=right;
                        min_length=k;
                    }
                }

                flag=false;
                cout<<left<<" "<<right<<endl;
                cout<<str_left<<" "<<str_right<<endl;
                cout<<endl;
            }
        }
        if(str_left!=-1){
            if(str_left!=str_right)return s.substr(str_left,str_right+1);
            else return t;

        }
        else return "";
    };
};

官方将right》left条件写在最前，防止出现left>right 的情况,能够避免在满足条件还未更新前导致的弹出循环
增加代码复杂度
思路以及方法基本相似
```



```
59.螺旋矩阵||

1.方法1：模拟矩阵生成过程将生成过程分为四个组成部分对应四个方向

矩阵能正确出来，但是方向相反
vector<vector<int>> generateMatrix(int n) {
    vector<vector<int>> mat(n, vector<int>(n, 0));
    int x_add = 1;
    int y_add = 0;
    int pos_x = 0;
    int pos_y = 0;
    cout << "循环" << endl;
    for (int i = 1; i < n * n+1; ++i) {
        cout << i << endl;
        cout << pos_x << " " << pos_y << endl;
        if (pos_x<0 || pos_x>=n || pos_y >= n || mat[pos_x][pos_y]) {
            //修正 错误 坐标
            //修正方向
            if (x_add == 1) {
                pos_x -= 1;
                pos_y += 1;
                x_add = 0;
                y_add = 1;
            }
            else if (x_add == -1) {
                pos_x += 1;
                pos_y -= 1;
                x_add = 0;
                y_add = -1;
            }
            else {
                if (y_add == 1) {
                    pos_y -= 1;
                    pos_x -= 1;
                    y_add = 0;
                    x_add = -1;
                }
                else {
                    pos_y += 1;
                    pos_x += 1;
                    y_add = 0;
                    x_add = 1;
                }
            }
        }
        cout << pos_x << " " << pos_y << endl;
        cout << endl;
        mat[pos_x][pos_y] = i;
        pos_x += x_add;
        pos_y += y_add;
    }
    return mat;
};

官方给的：
基本思路相同，都是模拟生成过程，唯一的不同在于其使用了数组来存储方向循环遍历则方向改变，而我是利用了方向本身的特性辨别方向以及修改方向

class Solution {
public:
static constexpr int directions[4][2] = {{0, 1}, {1, 0}, {0, -1}, {-1, 0}};
public:
    vector<int> spiralOrder(vector<vector<int>>& matrix) {
        if (matrix.size() == 0 || matrix[0].size() == 0) {
            return {};
        }
        
        int rows = matrix.size(), columns = matrix[0].size();
        vector<vector<bool>> visited(rows, vector<bool>(columns));
        int total = rows * columns;
        vector<int> order(total);

        int row = 0, column = 0;
        int directionIndex = 0;
        for (int i = 0; i < total; i++) {
            order[i] = matrix[row][column];
            visited[row][column] = true;
            int nextRow = row + directions[directionIndex][0], nextColumn = column + directions[directionIndex][1];
            if (nextRow < 0 || nextRow >= rows || nextColumn < 0 || nextColumn >= columns || visited[nextRow][nextColumn]) {
                directionIndex = (directionIndex + 1) % 4;
            }
            row += directions[directionIndex][0];
            column += directions[directionIndex][1];
        }
        return order;
   }
   
方法二：逐层处理：
给的：
vector<int> spiralOrder(vector<vector<int>>& matrix) {
        if (matrix.size() == 0 || matrix[0].size() == 0) {
            return {};
        }

        int rows = matrix.size(), columns = matrix[0].size();
        vector<int> order;
        int left = 0, right = columns - 1, top = 0, bottom = rows - 1;
        while (left <= right && top <= bottom) {
            for (int column = left; column <= right; column++) {
                order.push_back(matrix[top][column]);
            }
            for (int row = top + 1; row <= bottom; row++) {
                order.push_back(matrix[row][right]);
            }
            if (left < right && top < bottom) {
                for (int column = right - 1; column > left; column--) {
                    order.push_back(matrix[bottom][column]);
                }
                for (int row = bottom; row > top; row--) {
                    order.push_back(matrix[row][left]);
                }
            }
            left++;
            right--;
            top++;
            bottom--;
        }
        return order;
    }
```



```
54.螺旋矩阵：+剑指offer 29:顺时针打印矩阵
我的：
1.按顺序旋转（使用四个方向）+按层模拟（使用left+right+top+bottom 限定边界）
class Solution {
public:
    vector<int> spiralArray(vector<vector<int>>& array) {
        if(array.size() == 0 || array[0].size() == 0) return {};
        vector<int> arr;
        int top = 0;
        int bottom = array.size() - 1;
        int left = 0;
        int right = array[0].size() - 1;
        int x = 0;
        int y = 0;
        int x_add = 0;
        int y_add = 1;
        int length=(bottom+1)*(right+1);
        while (arr.size()<length) {
            if (y > right || y < left || x<top || x>bottom) {
                if (y > right) {
                    y -= 1; x += 1; top += 1;
                    y_add = 0; x_add = 1;
                }

                else if (y < left) {
                    y += 1; x -= 1; bottom -= 1;
                    y_add = 0; x_add = -1;
                }
                else if (x < top) {
                    x += 1; y += 1; left += 1;
                    y_add = 1; x_add = 0;
                }
                else {
                    x -= 1; y -= 1; right -= 1;
                    y_add = -1; x_add = 0;
                }
            }
            cout << x << " " << y << endl;
            cout << left << " " << right << endl;
            cout << top << " " << bottom << endl;
            cout << endl;

            arr.push_back(array[x][y]);
            x += x_add;
            y += y_add;
        }

        return arr;
    }
};
```



```
242.有效的字母异位词
我的：
1.使用两个hash表完成
class Solution {
public:
    bool isAnagram(string s, string t) {
        if(s.size()!=t.size())return false;
        unordered_map<char,int> s_hash;
        unordered_map<char,int> t_hash;
        for(int i=0;i<s.size();i++){
            ++s_hash[s[i]];
        }
        for(int i=0;i<t.size();i++){
            ++t_hash[t[i]];
        }
        if(s_hash.size()!=t_hash.size())return false;
        for(auto it=s_hash.begin();it!=s_hash.end();it++){
            if(it->second!=t_hash[it->first]){
                return false;
            }
        }
        return true;
    }
};

2.一个hash表完成
通过增减，最后hash表为空则true;
class Solution {
public:
    bool isAnagram(string s, string t) {
        if(s.size()!=t.size())return false;
        unordered_map<char,int> s_hash;
        for(int i=0;i<s.size();i++){
            ++s_hash[s[i]];
        }
        for(int i=0;i<t.size();i++){
            --s_hash[t[i]];
            if(!s_hash[t[i]]){
                s_hash.erase(t[i]);
            }
        }
        if(s_hash.empty()){
            return true;
        }
        else{
            return false;
        }
    }
};
知识： for(char ch :s)
m[ch-'a']++；



给的：
相当于自己建立了一个hash表，很有意思
class Solution {
public:
    bool isAnagram(string s, string t) {
      vector<int>m(26,0);
      for(char ch :s)
      {
          m[ch-'a']++;
      }
      for(char ch :t)
      {
          m[ch-'a']--;
      }
      bool mygo = true;
      for(int z =0;z<26;z++)
      {
if(m[z]!=0)
mygo = false;
      }
      return mygo;
    }
};

3.使用排序：
class Solution {
public:
    bool isAnagram(string s, string t) {
        //试一试排序
        if(s.length()!=t.length())return false;
        sort(s.begin(),s.end());
        sort(t.begin(),t.end());
        return s==t;
    }
};
```



```
383 赎金信:

xxxxxxxxxx 1.哈希表+滑动框class Solution {public:    bool Hash_Compare(unordered_map<char,int> s,unordered_map<char,int> t){        for(auto it=t.begin();it!=t.end();it++){           if(it->second > s[it->first]){               return false;           }         }        return true;    }    vector<int> findAnagrams(string s, string p) {        unordered_map<char,int> p_hash;        unordered_map<char,int> s_hash;        vector<int> result;        for(char c:p) ++p_hash[c];​        int right=0;        int left=0;        int p_length=p.length();        int s_length=s.length();        if(p_length>s_length) return {};                while(right >= left && right < s_length){            ++s_hash[s[right]];            if(Hash_Compare(s_hash,p_hash)){            while(Hash_Compare(s_hash,p_hash)){--s_hash[s[left]];++left;}            cout<<left <<" "<<right<<endl;            if(right-left+1<p_length)result.push_back(left-1);}            ++right;        };        return result;    };};​改进意见：限制条件为left与right的差距必须是p的长度，在满足s字串的字母的数量以及种类与t较大刚好时，使left->right-p_length;可以减少复杂度
```



```
349 两个数组的交集

1.两个hash表对比、统计
class Solution {
public:
    vector<int> intersection(vector<int>& nums1, vector<int>& nums2) {
        unordered_map<int,int> hash1;
        unordered_map<int,int> hash2;
        vector<int> result;

        for(int i=0;i<nums1.size();i++) ++hash1[nums1[i]];
        for(int i=0;i<nums2.size();i++) ++hash2[nums2[i]];

        for(auto it=hash1.begin();it!=hash1.end();++it){
            if(hash2[it->first]) result.push_back(it->first);
        }
        return result;
    }
};
```



```
202 快乐数

class Solution {
public:


    int getSum(int n){
        int sum=0;
        while(n){
            sum+=(n%10)*(n*10);
            n=n/10;
        }
        return sum;
    }

    bool isHappy(int n){
        unordered_set<int,int> num;
        if(n<0) return false; 
        int sum=getSum(n);
        while(1){
            int sum=getSum(n);
            if(sum==1)return true;
            if (num.find(sum) !=num.end()) return false;
            else {num.insert(sum);
                return isHappy(sum);
            }
        }
    }
};
```



```
454.两数相加
我的:
class Solution {
public:
    int fourSumCount(vector<int>& nums1, vector<int>& nums2, vector<int>& nums3, vector<int>& nums4) {
        unordered_map<int, int> first;
        unordered_map<int, int>second;
        int num = 0;

        for (auto i:nums1) {
            for (auto j : nums2) ++first[i + j];
        }
        for (auto i : nums3) {
            for (auto j : nums4) ++second[i + j];
        }

        for (auto i = first.begin(); i != first.end(); ++i) {
            auto j = second.find(-(i->first));
            if (j != second.end()) num+=j->second*i->second;
        }
        return num;
    }
};

给的
const int maxn = 1e5 + 11;
int ht[maxn], val[maxn];

void add(int x) {
    unsigned key = ((unsigned long long)x * 111111 + 111111u) % maxn;
    while (ht[key] && val[key] != x) {
        ++key;
        if(key >= maxn)
            key = 0;
    }
    ++ht[key], val[key] = x;
}

int query(int x) {
    unsigned key = ((unsigned long long)x * 111111 + 111111u) % maxn;
    while (ht[key] && val[key] != x) {
        ++key;
        if(key >= maxn)
            key = 0;
    }
    return ht[key];
}

class Solution {
   public:
    int fourSumCount(vector<int>& nums1, vector<int>& nums2, vector<int>& nums3, vector<int>& nums4) {
        memset(ht, 0, sizeof(ht));
        for (int x : nums1)
            for (int y : nums2)
                add(x + y);
        int ans = 0;
        for (int x : nums3)
            for (int y : nums4)
                ans += query(-(x + y));
        return ans;
    }
};
```



```
15.三数之和
我的:
class Solution {
public:
    int fourSumCount(vector<int>& nums1, vector<int>& nums2, vector<int>& nums3, vector<int>& nums4) {
        unordered_map<int, int> first;
        unordered_map<int, int>second;
        int num = 0;

        for (auto i:nums1) {
            for (auto j : nums2) ++first[i + j];
        }
        for (auto i : nums3) {
            for (auto j : nums4) ++second[i + j];
        }

        for (auto i = first.begin(); i != first.end(); ++i) {
            auto j = second.find(-(i->first));
            if (j != second.end()) num+=j->second*i->second;
        }
        return num;
    }
};

给的
const int maxn = 1e5 + 11;
int ht[maxn], val[maxn];

void add(int x) {
    unsigned key = ((unsigned long long)x * 111111 + 111111u) % maxn;
    while (ht[key] && val[key] != x) {
        ++key;
        if(key >= maxn)
            key = 0;
    }
    ++ht[key], val[key] = x;
}

int query(int x) {
    unsigned key = ((unsigned long long)x * 111111 + 111111u) % maxn;
    while (ht[key] && val[key] != x) {
        ++key;
        if(key >= maxn)
            key = 0;
    }
    return ht[key];
}

class Solution {
   public:
    int fourSumCount(vector<int>& nums1, vector<int>& nums2, vector<int>& nums3, vector<int>& nums4) {
        memset(ht, 0, sizeof(ht));
        for (int x : nums1)
            for (int y : nums2)
                add(x + y);
        int ans = 0;
        for (int x : nums3)
            for (int y : nums4)
                ans += query(-(x + y));
        return ans;
    }
};
```



```
16.四位数之和

1.越改bug越多：

vector<vector<int>> fourSum(vector<int>& nums, int target) {
    //解法:设置两边边界,将其转化为两个整数的和与目标值的关系

    vector<vector<int>> result;
    if (nums.size() < 4) return {};
    sort(nums.begin(), nums.end());
    for (int s : nums)cout << s << " ";
    cout << endl;


    int first = 0;
    int forth = nums.size() - 1;
    int second = 0;
    int third = forth - 1;
 
    for (;first<forth; ++first){
        
        //设置两边边界
        
        if (first) while (nums[first] == nums[first - 1] && first < nums.size() - 1) { first++;}
        if(forth!=nums.size()-1) while(nums[forth] == nums[forth + 1]) forth--;
        //设置两数
        second = first + 1;
        third = forth - 1;
        cout << first << " " << second << " " << third << " " << forth << endl;
        if (first >= forth) break;

        int new_target = target - nums[first] - nums[forth];

        if (2 * nums[first] > new_target) {forth--; continue;}

        while (second < third && nums[second] <= nums[third]) {
            
            cout << first << " " << second << " " << third << " " << forth << endl;
        
            //second 在边界
            while (nums[second] == nums[second-1] && second < third) second++;
            //third 在边界
            while (nums[third] == nums[third+1] && third > second) third--;
            //条件
            cout << first << " " << second << " " << third << " " << forth << endl;
            
           
            if (nums[second] + nums[third] == new_target) {
                result.push_back({ nums[first],nums[second],nums[third],nums[forth] });
                second++;
            }
            else if (nums[second] + nums[third] > new_target) {
                third--;
            }
            else {
                second++;
            }
        }
    }
    return result;
}

class Solution {
public:
    vector<vector<int>> fourSum(vector<int>& nums, int target){
        vector<vector<int>> result;
        int n=nums.size();
        if(n<4)return {};
        sort(nums.begin(),nums.end());

-》这里int求和超出范围
        if(target>0)if( (long long)target>=(long long) 4*nums[0] && (long long) target <= (long long) 4*nums[n-1]) return {};


        for(int first=0;first<n;++first){
            if(first && nums[first]==nums[first-1]) continue;
            for(int second=first+1;second<n;++second){
                if(second!=first+1 && nums[second]==nums[second-1])continue;
                int forth=n-1;
                int new_target=target-nums[first]-nums[second];
                for(int third=second+1;third<n;third++){
                    if(third!=second+1 && nums[third]==nums[third-1]) continue;
                    while(third<forth && nums[forth]+nums[third]>new_target) --forth;
                    if(third==forth) break;
                    if(new_target==nums[third]+nums[forth]){
                        result.push_back({nums[first],nums[second],nums[third],nums[forth]});
                    }
                    
                }
            }
        }
        return result;
    };
};
```



```
344.反转字符串

class Solution {
public:
    void reverseString(vector<char>& s) {
        char mid;
        int n=s.size()-1;
        int i=0;
        while(n>i){
            mid=s[i];
            s[i]=s[n];
            s[n]=mid;
            i++;
            n--;
        }
    }
};
```



```
541.反转字符串2

class Solution {
public:
    //将指定段进行反转
    void reverse_str(string &s,int start,int end){
        char mid;
        while(start<end){
            mid=s[start];
            s[start]=s[end];
            s[end]=mid;
            start++;
            end--;
        }   
    }

    string reverseStr(string s, int k) {
        //计数
        int num=0;
        int n=s.size();
        for(int i=0;i<n;++i){
            if(!((i+1)%(2*k))){
                reverse_str(s,num,i-k);
                num=i+1;
            }
        }

        if(n-num<k) reverse_str(s,num,s.size()-1);
        else{reverse_str(s,num,num+k-1);}
        return s;
    }
};
```



```
151.反转字符串中的单词

1.出现错误:没有出现空格则不返回东西

string reverseWords(string s){

        int sp_pos=s.size()-1;
        string result;
        int i=sp_pos;
        int num=-1;

        while(i>=0){
            
            while(sp_pos>-1 && s[sp_pos]==' ') --sp_pos;
            if(sp_pos==-1) break;

            i=sp_pos;

            while(i>=0 && s[i]!=' ') if(s[i]<'0' || s[i]>'9') --i; else {num=i;--i;}
            if(i==-1) break;

            if(num<i || num>sp_pos){
                for(int j=i+1;j<sp_pos+1;j++) result.insert(result.end(),s[j]);
                result.insert(result.end(),' ');
            }

            sp_pos=i;
        }


        if(i==s.size()-1) return {};
        else if(sp_pos==s.size()-1){
            if(num==-1)return result;
            else return {};
        } 
        else{
            if(s[0]==' '){
                result.erase(result.size()-1);
            }
            else{
                if(num<=i || num>sp_pos)
                    for(int j=0;j<sp_pos+1;j++)
                        result.insert(result.end(),s[j]);
                else
                    result.erase(result.size()-1);
            }
        }
        return result;
    }
 
2.改正：
class Solution {
public:
    string reverseWords(string s){

        int sp_pos=s.size()-1;
        string result;
        int i=sp_pos;
        int num=-1;

        while(i>=0){
            
            while(sp_pos>-1 && s[sp_pos]==' ') --sp_pos;
            if(sp_pos==-1) break;

            i=sp_pos;

            while(i>=0 && s[i]!=' ') --i;
            if(i==-1) break;

            for(int j=i+1;j<sp_pos+1;j++) result.insert(result.end(),s[j]);
            result.insert(result.end(),' ');

            sp_pos=i;
        }

        cout<<i<<" "<<sp_pos<<endl;

        if(i==s.size()-1) 
            return {};
        else if(sp_pos==s.size()-1){
            return s;
        } 
        else{
            if(s[0]==' '){
                result.erase(result.size()-1);
            }
            else{
                for(int j=0;j<sp_pos+1;j++)
                    result.insert(result.end(),s[j]);


            }
        }
        return result;
    }
};
```



```
28.找出字符串中第一个匹配的下标

class Solution {
public:
    int strStr(string haystack, string needle){

        for(int i=0;i<haystack.size();++i){

            if(haystack[i]==needle[0]){
                if(i+needle.size()-1>=haystack.size()) return -1;
                int j=0;
                for(;j<needle.size();j++){
                    if(haystack[i+j]!=needle[j])break;
                }
                if(j==needle.size())return i;
            }
        }
        return -1;
    }
};


2.KMP算法
class Solution {
public:

    //计算得到前缀表
    vector<int> Con_list(string s){

        vector<int> list;

        int j=-1;
        list.push_back(j);

        for(int i=1;i<s.size();++i){
            while(j>=0 && s[i]!=s[j+1])j=list[j];
            if(s[i]==s[j+1])j++;
            list.push_back(j);            
        }
        return list;
    }

    int strStr(string haystack, string needle){
        //使用KMP算法进行计算
        if(needle.size()==0) return 0;
        vector<int> list=Con_list(needle);
        int j=-1;
        for(int i=0;i<haystack.size();++i){
            while(j>=0 && haystack[i]!=needle[j+1]){j=list[j];}
            if(haystack[i]==needle[j+1])j++;
            if(j==(needle.size()-1)) return(i-needle.size()+1);
        }
        return -1;
    }
}；
```



```
459.重复的字符串

class Solution {
public:
    bool repeatedSubstringPattern(string s){
        if(s.size()==0)  return false;
        vector<int> s_mem;
        int j=0;
        s_mem.push_back(j);
        for(int i=1;i<s.size();++i){

            //二者不同，则前对比前一个
            while(j>0 && s[i]!=s[j]) j=s_mem[j-1];
            if(s[i]==s[j]) ++j;
            s_mem.push_back(j);
        }
        for(int i:s_mem) cout<<i<<" ";

        int len=s_mem.size();
        if (s_mem[len - 1] != 0 && len % (len - (s_mem[len - 1])) == 0) {
            return true;
        }
        return false;
    }
};
```



```
232.用栈实现队列

class MyQueue {
public:
    stack<int> first;

    MyQueue() {
        stack<int> first; //用来存储数据
    }
    void push(int x) {
        first.push(x);
    }
    int pop() {
        if(first.size()==0)return {};
    
        stack<int> second;
        while(first.size()>1){
            second.push(first.top());
            
            first.pop();
        }
        
        cout<<first.top()<<endl;

        int num=first.top();
        first.pop();

        while(second.size()>0){
            first.push(second.top());
            second.pop();
        }
        return num;
    }
        
    int peek(){

        if(first.size()==0) return {};

        stack<int> second;
        while(first.size()>1){
            second.push(first.top());
            first.pop();
        }

        int num=first.top();

        while(second.size()>0){
            first.push(second.top());
            second.pop();
        }
        return num;
    }

    bool empty() {
        if(first.size()==0)return true;
        return false;
    }

};
```



```
225.用队列实现栈

class MyStack {
public:

    deque<int> first;

    MyStack() {
        deque<int> first;
    }

    void push(int x) {
        first.push_back(x);
    }
    
    int pop() {
        if(!first.size()) return {};
        deque<int> sceond;
        int num=0;
        int i=0;
        while(first.size()>1){
            num=first[0];
            first.pop_front();
            sceond.push_back(num);
        }
        i=first[0];
        first.pop_front();

        while(sceond.size()>0){            
            num=sceond[0];
            sceond.pop_front();
            first.push_back(num);
        }
        return i;
    }

    int top() {
        if(!first.size()) return{};
        return first[first.size()-1];
    }

    bool empty() {
        if(!first.size()) return true;
        return false;
    }
};
```



```
20.有效符号

class Solution {
public:
    bool isValid(string s) {
        stack<char> mem;

        if(!s.size()) return false;
        
        for(char i:s){
            if(mem.size()>=1){
                if(i==')' && mem.top()=='(')
                mem.pop();
                else if(i=='}' && mem.top()=='{')
                mem.pop();
                else if(i==']' && mem.top()=='[')
                mem.pop();
                else{
                    mem.push(i);
                }
            }
            else
                mem.push(i);
        }
        if(mem.empty()) return true;
        else return false;
    }
};
```



```
1047.删除字符串中的所有相邻的重复项

class Solution {
public:
    string removeDuplicates(string s){

        stack<char> sm;
        if(!s.size()) return{};

        for(char i:s){

            if(!sm.size()){sm.push(i);continue;}

            if(i==sm.top()){sm.pop();continue;}
            
            sm.push(i);
        }
        string new_s=string(sm.size(),' ');

        for(int i=new_s.size()-1;i>=0;--i){
            new_s[i]=sm.top();
            sm.pop();
        }
        return new_s;
    }
};
```



```
150.逆波兰表达式

class Solution {
public:
    int StrtoInt(string s){
        
        int signal=1;
        if(s[0]=='-'){signal=-1;}

        int num= (signal==-1) ? pow(10,s.size()-2):pow(10,s.size()-1);
        int sum=0;
        for(char a:s){
            if(a=='-'){continue;}
            sum+=int(a-'0')*num;
            num=num/10;
        }
        return signal*sum;
    }

    int evalRPN(vector<string>& tokens){
        stack<int> num;
        for(string a:tokens){
            if(a[a.size()-1]>='0' && a[a.size()-1]<='9'){
                num.push(StrtoInt(a));
            }
            else{
                int a1=num.top();
                num.pop();
                int a2=num.top();
                num.pop();              
                if(a[0]=='+')num.push(a1+a2);
                else if(a[0]=='/')num.push(a2/a1);
                else if(a[0]=='*')num.push(a1*a2);
                else num.push(a2-a1);
            }
        }
        return num.top();
    }
};
```



```
239.滑动窗口的最大值

1.时间超出上限
class Solution {
public:
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {
        vector<int> result;

        int max_num=INT_MIN;
        int j=0;
        int pos=-1;

        for(int i=0;i<nums.size();++i){
            if(i-j+1>k){
                result.push_back(max_num);
                if(max_num<=nums[i]) {max_num=nums[i];pos=i;}
                else 
                    if(pos==j)
                        max_num=INT_MIN; 
                        for(int a=j+1;a<=i;++a){
                            if(max_num<nums[a]){max_num=nums[a];pos=a;}}
                j++;
            }
            else{
                if(max_num<nums[i]){max_num=nums[i];pos=i;}
            }
        }

        result.push_back(max_num);
        return result;
    }
};

2.超出时间限制
class Solution {
public:
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {
        vector<int> result;
        deque<int> d;
        int max_count;
        int max_num=INT_MIN;

        int second_count;
        int second_num;

        if(k!=1){
            for(int i=0;i<nums.size();++i){
                d.push_back(nums[i]);
                    if(d.size()<k){
                        if(nums[i]>max_num){max_num=nums[i];max_count=1;}
                        else if(nums[i]==max_num){max_count++;}
                    }
                    else{
                        if(nums[i]>max_num){max_num=nums[i];max_count=1;}
                        else if(nums[i]==max_num){max_count++;}

                        result.push_back(max_num);

                        if(d[0]==max_num){
                            --max_count;
                            if(!max_count){
                                max_num=INT_MIN;
                                for(int j:d){
                                    if(j==d[0])continue;
                                    if(j>max_num){max_count=1;max_num=j;}
                                    else if(j==max_num){max_count++;}
                                }
                            }
                        }
                    d.pop_front();
                    }
                }
            }
        else return nums;
        return result;
    }
};

给的;
prioriy_queue
给每个元素一个优先级，与其值相关

涉及了新的容器

class Solution {
public:
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {
        int n=nums.size();
        priority_queue<pair<int,int>> q;

        for(int i=0;i<k;++i){
            q.emplace(nums[i],i);
        }

        vector<int> ans={q.top().first};

        for(int i=k;i<n;++i){
            q.emplace(nums[i],i);
            while(q.top().second<=i-k){
                q.pop();
            }
            ans.push_back(q.top().first);
        }
        return ans;
    }
};
```

