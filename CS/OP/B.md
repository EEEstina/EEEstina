# 柠檬笔试

## 1

给定一个字符串S,判断S是否为有效字符串。有效的标准如下:
1.字符串 abc 有效。
2.对于一个有效字符串S，S=X+Y(+号为连接符，X,Y可有一个为空)，那么T=X+abc+Y也为有效字符串。如果字符串有效，返回true，否则返回false。

补充说明
字符串长度|S|<=10^5,且字符集为{a,b,c}。
30%的数据|S|<=30
70%的数据|S|<=1000
100%的数据|S|<=10^5

示例1
输入
"aabcbc"

输入
"true"

```cpp
#include <iostream>
#include <string>
using namespace std;

bool isValid(string s) {
    int n = s.size();
    if (n == 0) return false;

    int i = 0;
    while (i < n) {
        if (s.substr(i, 3) == "abc") {
            i += 3; // Move the index by 3 if "abc" is found
        } else {
            // If the substring doesn't match "abc", return false
            return false;
        }
    }
    return true;
}

int main() {
    string s;
    cin >> s;  // Read the input string
    cout << (isValid(s) ? "true" : "false") << endl;
    return 0;
}

```

## 2

IPv4 地址字符串为点(.)分隔的四段数字，每段数字取值范围0~255，例如202.106.0.20，8.8.4.4。请编写函数将其转换为32比特无符号整数。其中字符串中最左边的一段在最高位侧，最右边的一段在最低位侧。例如上述两个IP 地址转换完后分别为0xCA6A0014 和0x08080404。

输入描述
输入数据每行为一个IPv4地址，例如:
202.106.0.20
8.8.4.4
输出描述
输出为解析后得到的整数的8位16进制表述，例如:
CA6A0014
08080404
注意不输出0x前缀，不够8位数用0补齐。对非法的输入，输出一个X

示例1
输入
202.106.0.20
6.7.8.999
8.8.4.4
输出
CA6A0014
X
08080404

```cpp
#include <iostream>
#include <sstream>
#include <vector>
#include <iomanip>
using namespace std;

// 判断每段数字是否合法并返回对应的整数值
bool isValidSegment(const string& segment) {
    // 检查是否全是数字
    if (segment.empty() || segment.size() > 3) return false;
    for (char c : segment) {
        if (!isdigit(c)) return false;
    }
    // 转换为整数并检查范围
    int num = stoi(segment);
    return num >= 0 && num <= 255;
}

string ipv4ToInteger(const string& ip) {
    stringstream ss(ip);
    string segment;
    vector<int> segments;

    // 按"."分割字符串
    while (getline(ss, segment, '.')) {
        segments.push_back(stoi(segment));
    }

    // 如果不是四段数字，则认为非法
    if (segments.size() != 4) return "X";

    // 拼接 32 位整数
    unsigned int result = 0;
    for (int i = 0; i < 4; ++i) {
        if (!isValidSegment(to_string(segments[i]))) {
            return "X"; // 如果某一段无效，返回X
        }
        result = (result << 8) | segments[i]; // 左移并加上当前段
    }

    // 返回 32 位整数的 8 位十六进制格式，补零
    stringstream hexStream;
    hexStream << setw(8) << setfill('0') << uppercase << hex << result;
    return hexStream.str();
}

int main() {
    string ip;
    while (getline(cin, ip)) {
        cout << ipv4ToInteger(ip) << endl;
    }
    return 0;
}
```

## 3

我们有一个刻有字母的圆盘，用字符串plate表示(从圆盘正上方开始顺时针阅读)，我们的任务是通过旋转该圆环得到目标词target,并且使得旋转次数最少。
我们每次可以将圆盘顺时针或逆时针旋转一格，圆盘中心有一个按钮，按下即可输出圆盘正上方的字母，当我们将目标词输出出来时任务完成，尝试给出完成任务所需的最小旋转次数总和。
圆盘示例:

   a
b     b
a     c
   d

示例1
输入
"abcdab","ad"
输出
3
说明
对于target的第一个字符'a'，已经处于圆盘正上方，按下按钮即可输出。
对于target的第二个字符'd'，需要顺时针旋转3个位置，
把圆盘从"abcdab"变为"dababc"，按下按钮输出。
因此最终的结果为3。

示例2
输入
"abcde","abc"
输出
2

```cpp
#include <iostream>
#include <string>
#include <cmath>
using namespace std;

int minRotations(const string& plate, const string& target) {
    int n = plate.length(); // 圆盘的大小
    int total_rotations = 0; // 总旋转次数
    int current_pos = 0; // 当前的正上方位置
    
    // 对于目标字符串中的每个字符
    for (char ch : target) {
        // 找到目标字符在圆盘中的位置
        int target_pos = plate.find(ch);
        
        // 计算顺时针和逆时针的旋转次数
        int clockwise = (target_pos - current_pos + n) % n;  // 顺时针旋转
        int counterclockwise = (current_pos - target_pos + n) % n;  // 逆时针旋转
        
        // 选择较小的旋转次数
        total_rotations += min(clockwise, counterclockwise);
        
        // 更新当前的位置
        current_pos = target_pos;
    }
    
    return total_rotations;
}

int main() {
    string plate, target;
    cin >> plate >> target;
    
    int result = minRotations(plate, target);
    cout << result << endl;
    
    return 0;
}

```

## 4

实现简单的正则表达式匹配，本题中模式字符串所包含的字符的范围为字母、“.”、“*”、“?”
“.”匹配任何单个字符
“*”与模式字符串前一个字符组成一组，匹配零个或多个前面的字符
“?”与模式字符串前一个字符组成一组，匹配一个或多个前面的字符
匹配应该覆盖到整个输入的字符串(而不是局部的)，测试用例中不会出现超出匹配字符范围之外的字符，也不会出现非法的模式字符串。
使用语言提供的正则表达式库将算作无效答案。

输入描述
输入的第一行为需要检测匹配的用例数。
接下来的每一行包括两个字符串，前一个字符串为待匹配的字符串，后一个字符串为模式字符串。待匹配字符串的长度不超过10。

输出描述
对于每一个测试用例，如果匹配则输出一行true，如果不匹配则输出一行false。

示例1
输入
3
aa a
aa aa
aaa aa
输出
false
true
false

示例2
输入
5
a a.
a a.*
ab .*
ab .?
b a?

输出
false
true
true
true
false

```cpp
#include <iostream>
#include <vector>
#include <string>
using namespace std;

bool isMatch(const string& s, const string& p) {
    int m = s.length();
    int n = p.length();
    
    // DP 表，dp[i][j] 表示 s[0..i-1] 与 p[0..j-1] 是否匹配
    vector<vector<bool>> dp(m + 1, vector<bool>(n + 1, false));
    
    // 空字符串与空模式匹配
    dp[0][0] = true;
    
    // 处理模式中以 '*' 或 '?' 开头的情况
    for (int j = 1; j <= n; ++j) {
        if (p[j - 1] == '*' || p[j - 1] == '?') {
            dp[0][j] = dp[0][j - 2];  // '*' 或 '?' 可以跳过
        }
    }
    
    // 动态规划填表
    for (int i = 1; i <= m; ++i) {
        for (int j = 1; j <= n; ++j) {
            if (p[j - 1] == s[i - 1] || p[j - 1] == '.') {
                dp[i][j] = dp[i - 1][j - 1];  // 字符匹配或 '.' 匹配任意字符
            } else if (p[j - 1] == '*') {
                dp[i][j] = dp[i][j - 2] ||  // 不匹配当前字符，跳过 '*'
                           (s[i - 1] == p[j - 2] || p[j - 2] == '.') && dp[i - 1][j];  // 匹配当前字符并使用 '*' 重复
            } else if (p[j - 1] == '?') {
                dp[i][j] = dp[i - 1][j - 1] || dp[i][j - 2];  // '?' 匹配一次或跳过
            }
        }
    }
    
    return dp[m][n];
}

int main() {
    int t;
    cin >> t;
    while (t--) {
        string s, p;
        cin >> s >> p;
        if (isMatch(s, p)) {
            cout << "true" << endl;
        } else {
            cout << "false" << endl;
        }
    }
    return 0;
}
```
