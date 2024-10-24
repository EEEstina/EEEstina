# 笔试

## 字节笔试

### 1

小红有一个长度为n的数组a.她想选定一个整数x，然后每次选择以下操作之一

1. 选定一个区间[l,r]使得将所有i∈[l,r],ai=ai+x
2. 选定一个区间[l,r]使得将所有i∈[l,r],ai=ai-x

小红想知道在任意次操作的前提下，把整个数组的所有元素变为一样的最大x是多少。

输入描述

每个测试文件均包含多组测试数。第一行输入一个整数T(1≤T≤100),代表数据组数,每组测试数据描述如下:
对于每一组测试数据:
第一行一个整数n(2≤n≤10^5),表示结点总数.
第二行n个整数,第i个为ai(1≤a≤10^9)表示数组元素,保证任意两个数互不相同。

输出描述
共T行,每行一个整数,表示x的最大值.

示例1
输入
2
2
5 10
2
8 10
输出
5
2

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

int main() {
    int T;
    cin >> T; // 读入测试组数

    while (T--) {
        int n;
        cin >> n; // 读入数组长度
        vector<int> a(n);

        for (int i = 0; i < n; i++) {
            cin >> a[i]; // 读入数组元素
        }

        // 找到最小值和最大值
        int minA = *min_element(a.begin(), a.end());
        int maxA = *max_element(a.begin(), a.end());

        // 计算最大x
        int maxX = (maxA - minA) / 2;
        cout << maxX << endl; // 输出结果
    }

    return 0;
}

```

```cpp
#include <iostream>
#include <vector>
#include <numeric>
#include <algorithm>

using namespace std;

// 求两个数的最大公约数
int gcd(int a, int b) {
    while (b != 0) {
        int temp = b;
        b = a % b;
        a = temp;
    }
    return a;
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    
    int T;
    cin >> T; // 输入测试组数
    
    while (T--) {
        int n;
        cin >> n; // 输入数组长度
        vector<int> a(n);
        
        for (int i = 0; i < n; ++i) {
            cin >> a[i]; // 输入数组元素
        }
        
        // 计算相邻元素的差值
        vector<int> diffs(n - 1);
        for (int i = 1; i < n; ++i) {
            diffs[i - 1] = abs(a[i] - a[i - 1]);
        }
        
        // 计算所有差值的GCD
        int max_x = diffs[0];
        for (int i = 1; i < diffs.size(); ++i) {
            max_x = gcd(max_x, diffs[i]);
        }
        
        cout << max_x << "\n"; // 输出结果
    }
    
    return 0;
}
```

### 2

nxm的迷官中存在一个终点(ex, ey)，你将被无数次的传送到迷宫中，每一次都会被等概率的传送任意一个能够到达终点的位置(包括终点本身)。

随后，你可以任意的向上下左右四个方向移动，前提是目标位置没有障碍物且依旧位于迷宫中，每一轮你都将移动最短的步数到达终点。你想知道到达终点的期望步数是多少。

输入描述
    第一行输入四个整数
n,m,ex,ey(1≤n,m≤10^3;1≤ex≤n;1≤ey≤m)代表迷官的大小和终点的位置。
    此后n行,每行输入一个长度为m且仅包含'0'和'1'的字符串s，代表迷官中这一行的情况，其中'0'代表该位置可通行,'1'代表该位置存在障碍物。保证终点为可通行位置

输出描述
    在一行上输出两个整数代表到达终点的期望步数。
可以证明答案可以表示为一个不可约分数 p/q，为了避免精度问题，请直接输出p和q.

示例
输入
2 2 1 1
00
00
输出
1 1

```cpp
#include <iostream>
#include <vector>
#include <queue>
#include <numeric> // For std::gcd

using namespace std;

const int dx[4] = {1, -1, 0, 0};
const int dy[4] = {0, 0, 1, -1};

int main() {
    int n, m, ex, ey;
    cin >> n >> m >> ex >> ey;
    ex--; ey--; // 转为 0-indexed

    vector<string> maze(n);
    for (int i = 0; i < n; i++) {
        cin >> maze[i];
    }

    vector<vector<int>> dist(n, vector<int>(m, -1));
    queue<pair<int, int>> q;

    // BFS 初始化
    dist[ex][ey] = 0;
    q.push({ex, ey});

    // BFS 寻找所有点到终点的最短距离
    while (!q.empty()) {
        auto [x, y] = q.front();
        q.pop();
        
        for (int i = 0; i < 4; i++) {
            int nx = x + dx[i];
            int ny = y + dy[i];
            if (nx >= 0 && nx < n && ny >= 0 && ny < m && 
                maze[nx][ny] == '0' && dist[nx][ny] == -1) {
                dist[nx][ny] = dist[x][y] + 1;
                q.push({nx, ny});
            }
        }
    }

    long long total_steps = 0;
    long long reachable_count = 0;

    // 计算总步数和可达位置数量
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < m; j++) {
            if (dist[i][j] != -1) {
                total_steps += dist[i][j];
                reachable_count++;
            }
        }
    }

    if (reachable_count == 0) {
        cout << "0 1" << endl; // No reachable points (though problem guarantees at least one)
        return 0;
    }

    // 计算期望步数
    long long p = total_steps;
    long long q = reachable_count;

    // 简化分数
    long long g = gcd(p, q);
    p /= g;
    q /= g;

    cout << p << " " << q << endl;
    return 0;
}
```

### 3

小苯定义一个长度为n的排列[p1,p2,..,pn]的曼哈顿距离为:n∑(i=1)|Pi-i|， 即所有位置上的排列值与坐标的绝对差之和。
现在小苯希望你求出，所有长度为n的排列的曼哈顿距离之和

输入描述
每个测试文件均包含多组测试数据。第一行输入一个整数T(1≤T≤10^4)代表数据组数,每组测试数据描述如下:
    第一行输入一个正整数n(1≤n≤2x10^5),表示小苯询问的长度。

输出描述
对于每一组测试数据，在一行上输出一个整数，代表表示所有长度为n的排列的曼哈顿距离之和，由于答案可能很大，输出对10^9+7取模的结果。

示例
输入
3
1
2
3
输出
0
2
16

```cpp
#include <iostream>
#include <vector>

using namespace std;

const int MOD = 1e9 + 7;

// 计算阶乘并返回它的模
long long factorial(int n) {
    long long result = 1;
    for (int i = 2; i <= n; i++) {
        result = (result * i) % MOD;
    }
    return result;
}

int main() {
    int T;
    cin >> T; // 读入测试组数

    vector<long long> results;
    while (T--) {
        int n;
        cin >> n; // 读入n

        long long total_contribution = 0;

        // 计算每个位置的贡献
        for (int i = 1; i <= n; i++) {
            long long contribution = 0;

            // 1到i-1的部分
            contribution += (i - 1) * (i) - (i - 1) * (i - 1) / 2; // sum of (i - k) for k = 1 to i-1
            // i到n的部分
            contribution += (i * (n - i)) - (n * (n + 1) / 2 - (i - 1) * i / 2); // sum of (k - i) for k = i to n

            total_contribution += contribution;
        }

        long long factorial_n_minus_1 = factorial(n - 1);
        total_contribution = (total_contribution * factorial_n_minus_1) % MOD;

        results.push_back(total_contribution);
    }

    // 输出所有结果
    for (auto res : results) {
        cout << res << endl;
    }

    return 0;
}

```

### 4

小红在玩一款机器人移动游戏。

游戏地图的大小为nxm(行列).小红操控了k个机器人,每个机器人的初始坐标为(xi, yi)

现在小红会做出q次操作,每次操作为
v(v ∈[1,k]), dir ∈{U,D,L,R}.表示编号为v的机器人往某一个方向移动,直到下一个位置也是机器人就停下,或者移动到地面边界

对于每次操作,小红需要你帮助她输出当前编号为v的机器人的终点

`'U':上 'D':下 'L':左 'R:右`

输入描述
第一行四个整数n,m,k,q(1 ≤n,m,k,q ≤ 10^5, k≤nxm).依次表示地图大小,机器人数量和小红的操作次数。

接下来k行,每行两个整数xi,yi(1≤xi≤n,1≤yi≤m),表示编号为i的机器人的初始坐标。

接下来q行,每行包含一个整数v(1≤v≤k)和一个字符 dir(dir ∈ {U,D,L,R}),表示操作的机器人编号和方向。

数据保证初始时任意两个机器人的坐标都不相同。

输出描述
每q行，每行两个整数x,y,表示当前操作下机器人的终点坐标。

示例
输入
5 5 3 3
3 4
4 2
1 1
2 L
3 D
1 L
输出
4 1
3 1
3 2

```cpp
#include <iostream>
#include <vector>
#include <unordered_map>

using namespace std;

int main() {
    int n, m, k, q;
    cin >> n >> m >> k >> q;

    // 存储机器人的位置
    vector<pair<int, int>> positions(k + 1); // 1-based index

    // 读入机器人的初始坐标
    for (int i = 1; i <= k; i++) {
        int x, y;
        cin >> x >> y;
        positions[i] = {x, y};
    }

    // 使用一个集合来记录所有机器人的位置
    unordered_map<int, unordered_map<int, bool>> occupied;
    for (int i = 1; i <= k; i++) {
        occupied[positions[i].first][positions[i].second] = true;
    }

    // 处理操作
    for (int i = 0; i < q; i++) {
        int v;
        char dir;
        cin >> v >> dir;

        int x = positions[v].first;
        int y = positions[v].second;

        // 移动机器人直到碰到边界或其他机器人
        while (true) {
            if (dir == 'U') {
                if (x == 1 || occupied[x - 1][y]) break; // 到达边界或碰到其他机器人
                x--;
            } else if (dir == 'D') {
                if (x == n || occupied[x + 1][y]) break; // 到达边界或碰到其他机器人
                x++;
            } else if (dir == 'L') {
                if (y == 1 || occupied[x][y - 1]) break; // 到达边界或碰到其他机器人
                y--;
            } else if (dir == 'R') {
                if (y == m || occupied[x][y + 1]) break; // 到达边界或碰到其他机器人
                y++;
            }
        }

        // 更新机器人的位置
        occupied[positions[v].first][positions[v].second] = false; // 移除旧位置
        positions[v] = {x, y}; // 更新位置
        occupied[x][y] = true; // 添加新位置

        // 输出当前机器人的终点坐标
        cout << x << " " << y << endl;
    }

    return 0;
}

```

## 腾讯笔试

### 11

小红拿到了一个数组，她想在数组中选择两个不同的元素，满足这两个元素的和等于它们的异或。小红想知道，自己有多少种选择的方案？

输入描述
第一行为t，表示有t组数据。
接下来有2xt行，其中的第一行为一个n，表示数组长度。
第二行有n个整数,表示数据元素ai
1≤t≤100, 1≤n≤10^3, 1≤ai≤10^9, ∑n≤10^3.

输出描述
输出为t行，每行为一组答案。

示例1
2
5
1 2 3 4 5
3
4 4 8

输出
5
2

```go
package main

import (
    "fmt"
)

func main() {
    var t int
    fmt.Scan(&t)

    for i := 0; i < t; i++ {
        var n int
        fmt.Scan(&n)

        arr := make([]int, n)
        for j := 0; j < n; j++ {
            fmt.Scan(&arr[j])
        }

        count := 0
        for x := 0; x < n; x++ {
            for y := x + 1; y < n; y++ {
                if arr[x]+arr[y] == arr[x]^arr[y] {
                    count++
                }
            }
        }
        fmt.Println(count)
    }
}

```

```cpp
#include <iostream>
#include <vector>

using namespace std;

int main() {
    int t;
    cin >> t;

    while (t--) {
        int n;
        cin >> n;
        vector<int> arr(n);

        for (int j = 0; j < n; j++) {
            cin >> arr[j];
        }

        int count = 0;
        for (int x = 0; x < n; x++) {
            for (int y = x + 1; y < n; y++) {
                if (arr[x] + arr[y] == (arr[x] ^ arr[y])) {
                    count++;
                }
            }
        }
        cout << count << endl;
    }

    return 0;
}
```

### 22

有m个苹果，n个小孩。每个小孩都有一个编号1-n，小明的编号是k。要尽量公平的分苹果，相邻编号的小孩分到的苹果数目差距不能大于1。请问如何在满足相邻编号的小孩分到的苹果数目差距不能大于1的情况下，小明分配到的苹果数目最多，并且输出这个最大值，每个小朋友至少需分配到一个苹果。

输入描述
第一行三个整数n, m, k (1≤n≤m≤10^9, 1≤k≤n)

输出描述
输出一行，表示小明分配到苹果个数的最大值。

示例1
输入
4 6 2
输出
2
说明
可以这样分配1 2 2 1。可以小明分配到了2个苹果。

```go
package main

import (
    "fmt"
    "os"
)

// 全局数组来存储每个小孩的苹果数，初始化为0
var apples []int

func main() {
    var n, m, k int
    _, err := fmt.Scanln(&n, &m, &k)
    if err != nil {
        fmt.Println("Error reading input:", err)
        os.Exit(1)
    }

    // 初始化每个小孩的苹果数为0
    apples = make([]int, n)
    for i := range apples {
        apples[i] = 0
    }

    result := maxApplesFork(m, n, k-1) // k-1 因为索引从0开始
    fmt.Println(result)
}

func maxApplesFork(m, n, k int) int {
    if k < 0 || k >= n {
        return -1 // 索引越界
    }

    // 先给每个小孩分配一个苹果
    for i := range apples {
        apples[i] = 1
    }
    apples[k]++
    // 剩余苹果数
    applesLeft := m - n
    applesLeft--

    // 尝试分配剩余的苹果给小明及其相邻的小孩
    for applesLeft > 0 {
        if k > 0 && apples[k-1] <= apples[k]-1 {
            // 如果小明左边第一个小孩苹果数少于或等于小明苹果数减1，代表不具备小明苹果树+1的条件（条件是两者应该相等）则给小明左边的小孩一个苹果
            apples[k-1] = apples[k-1] + 1
            applesLeft--
            //每次剩余苹果数减一都需要判断是否为0
            if applesLeft == 0 {
                return apples[k]
            }
            i := k - 1
            //因为小明旁边第一个小孩苹果数+1啦，所以要判断他左边的小孩是否满足相差不超过1的原则，
            for i > 0 && apples[i-1] < apples[i]-1 {
                apples[i-1] = apples[i] + 1
                applesLeft--
                if applesLeft == 0 {
                    return apples[k]
                }
                if apples[i-1] == apples[i]-1 {
                    i--
                }
            }
        } else if k < n-1 && apples[k+1] <= apples[k]-1 {
            // 如果小明右边的小孩苹果数少于小明苹果数减1，则给小明右边的小孩一个苹果
            apples[k+1] = apples[k+1] + 1
            applesLeft--
            if applesLeft == 0 {
                return apples[k]
            }
            i := k + 1
            for i < n-1 && apples[i+1] < apples[i]-1 {
                apples[i+1] = apples[i+1] + 1 //asdf
                applesLeft--
                if applesLeft == 0 {
                    return apples[k]
                }
                if apples[i+1] == apples[i]-1 {
                    i++ //asdf
                }
            }
        } else {
            // 否则，给小明一个苹果
            apples[k] = apples[k] + 1
            applesLeft--
        }
    }

    // 返回小明分到的苹果数
    return apples[k]
}

```

```cpp
#include <iostream>
#include <vector>

using namespace std;

// 全局变量来存储每个小孩的苹果数
vector<int> apples;

int maxApplesFork(int m, int n, int k) {
    if (k < 0 || k >= n) {
        return -1; // 索引越界
    }

    // 先给每个小孩分配一个苹果
    apples.assign(n, 1);
    apples[k]++;
    // 剩余苹果数
    int applesLeft = m - n;
    applesLeft--;

    // 尝试分配剩余的苹果给小明及其相邻的小孩
    while (applesLeft > 0) {
        if (k > 0 && apples[k - 1] <= apples[k] - 1) {
            // 给小明左边的小孩一个苹果
            apples[k - 1]++;
            applesLeft--;
            if (applesLeft == 0) {
                return apples[k];
            }
            int i = k - 1;
            while (i > 0 && apples[i - 1] < apples[i] - 1) {
                apples[i - 1] = apples[i] + 1;
                applesLeft--;
                if (applesLeft == 0) {
                    return apples[k];
                }
                i--;
            }
        } else if (k < n - 1 && apples[k + 1] <= apples[k] - 1) {
            // 给小明右边的小孩一个苹果
            apples[k + 1]++;
            applesLeft--;
            if (applesLeft == 0) {
                return apples[k];
            }
            int i = k + 1;
            while (i < n - 1 && apples[i + 1] < apples[i] - 1) {
                apples[i + 1]++;
                applesLeft--;
                if (applesLeft == 0) {
                    return apples[k];
                }
                i++;
            }
        } else {
            // 否则，给小明一个苹果
            apples[k]++;
            applesLeft--;
        }
    }

    // 返回小明分到的苹果数
    return apples[k];
}

int main() {
    int n, m, k;
    cin >> n >> m >> k; // 输入小孩数、苹果总数和小明的索引

    // 初始化每个小孩的苹果数为0
    apples.resize(n, 0);

    int result = maxApplesFork(m, n, k - 1); // k-1 因为索引从0开始
    cout << result << endl; // 输出结果

    return 0;
}


```

### 33

给定一个字符串，请你求出有多少个连续子串包含'r'和'e'字符，但不包
含d字符？

输入描述
一个仅包含小写字母的字符串，长度不超过300000。

输出描述
满足条件的连续子串数量。

示例1
输入
raefadr
输出
3
说明
共包含3个连续子串："rae"、"raef"、"raefa".

```cpp
#include <iostream>
#include <string>
using namespace std;

int countSubstrings(const string& s) {
    int count = 0;
    int n = s.size();

    for (int start = 0; start < n; ++start) {
        if (s[start] == 'd') continue;

        int hasR = 0, hasE = 0;

        for (int end = start; end < n; ++end) {
            if (s[end] == 'd') break;
            if (s[end] == 'r') hasR = 1;
            if (s[end] == 'e') hasE = 1;

            if (hasR && hasE) {
                count++;
            }
        }
    }

    return count;
}

int main() {
    string s;
    cin >> s;
    cout << countSubstrings(s) << endl;
    return 0;
}
```

```go
package main

import (
    "fmt"
)

func countSubstrings(s string) int {
    count := 0
    n := len(s)

    for start := 0; start < n; start++ {
        if s[start] == 'd' {
            continue
        }

        hasR := false
        hasE := false

        for end := start; end < n; end++ {
            if s[end] == 'd' {
                break
            }
            if s[end] == 'r' {
                hasR = true
            }
            if s[end] == 'e' {
                hasE = true
            }

            if hasR && hasE {
                count++
            }
        }
    }

    return count
}

func main() {
    var s string
    fmt.Scan(&s)
    fmt.Println(countSubstrings(s))
}

```
