# 小黑盒笔试

## 1

你在玩一个策略卡牌游戏《杀戮尖塔》
现在轮到你出牌，手里有N张攻击卡，每张牌都有需要花费的金钱点数cost[i]和敌人获得的伤害damage[i]。每张牌只能出一次。
你最多能花费3点金钱。
请问你最多能给敌人多少伤害?

补充说明
0<N<=50
1<=cost[i]<=3
0<damage[i]<=100

示例1
输入
3,[1,1,1],[2,4,6]
输出
12

示例2
输入
4,[1,1,1,1],[2,4,8,6]
输出
18

示例3
输入
2,[2,2],[5,6]
输出
16

示例4
输入
3,[1,2,3],[1,4,9]
输出
9

```go

package main

import "fmt"

// maxDamage 函数，计算在最多花费3点金钱时能造成的最大伤害
func maxDamage(N int, cost []int, damage []int) int {
    dp := make([]int, 4) // dp数组表示花费0, 1, 2, 3的最大伤害

    // 遍历每张卡牌
    for i := 0; i < N; i++ {
        c := cost[i]
        d := damage[i]

        // 从大到小更新dp，避免重复使用同一张卡牌
        for j := 3; j >= c; j-- {
            dp[j] = max(dp[j], dp[j-c]+d)
        }
    }

    return dp[3] // 返回花费最多3点金钱时的最大伤害
}

// max 辅助函数，返回两个数中的较大值
func max(a, b int) int {
    if a > b {
        return a
    }
    return b
}

func main() {
    var N int
    fmt.Println("请输入卡牌的数量 N：")
    fmt.Scan(&N)

    cost := make([]int, N)
    damage := make([]int, N)

    fmt.Println("请输入每张卡牌的花费 cost：")
    for i := 0; i < N; i++ {
        fmt.Scan(&cost[i])
    }

    fmt.Println("请输入每张卡牌的伤害 damage：")
    for i := 0; i < N; i++ {
        fmt.Scan(&damage[i])
    }

    // 计算并输出最大伤害
    result := maxDamage(N, cost, damage)
    fmt.Printf("在最多花费3点金钱时，最大伤害是: %d\n", result)
}

```

## 2

你的国家里有N个城市，每个城市都有一个名字。你要给这N个名字提供缩写。

缩写的规则如下:
-缩写的字符串长度为3个字符
-缩写的名字是通过原来的名字里删除若干个字符来组成(不能调换字符顺序)
-如果一个缩写能通过其他城市的名字删除而来，则不能使用这个缩写(冲突)

请返回能否给所有这N个名字提供缩写，而不冲突。如果是，返回1，否则，返回0

补充说明
城市个数大小N：1<N<50
城市原名字长度魏2 ~ 50
城市名字全部由英文大写字母组成'A'~'Z'

示例1
输入
3,["FRANKFURT","ZURICH","LONDONHEATHROW"]
输出
1
说明
可能的缩写组合为["FRK","ZRH","LHR"]

示例2
输入
4,["NEWYORK","NEWJERSEY","NEWPORT","NEUSTADT"]
输出
1
说明
可能的缩写组合为["NEY","NEJ","NEP","NEU"]

示例3
输入
3,["NEWYORK","NEWYORK","NEWARK"]
输出
0
说明
因为第0个和第1个名字完全相同所以无法得出不冲突的缩写

```go
package main

import "fmt"

// 判断是否能生成不冲突的缩写
func canGenerateUniqueAbbreviation(cities []string) int {
    abbreviationSet := make(map[string]int) // 用来记录每个缩写出现的次数

    // 对每个城市名进行处理
    for _, city := range cities {
        // abbreviationFound := false
        // 为每个城市生成所有可能的3字符缩写
        for i := 0; i < len(city)-2; i++ {
            for j := i + 1; j < len(city)-1; j++ {
                for k := j + 1; k < len(city); k++ {
                    abbreviation := string([]rune{rune(city[i]), rune(city[j]), rune(city[k])})
                    abbreviationSet[abbreviation]++

                    // 如果这个缩写已经出现过，则说明有冲突
                    if abbreviationSet[abbreviation] > 1 {
                        return 0
                    }
                }
            }
        }
    }

    return 1
}

func main() {
    // 输入城市数量
    var N int
    fmt.Println("请输入城市数量：")
    fmt.Scan(&N)

    // 输入城市名称
    cities := make([]string, N)
    fmt.Println("请输入城市名称：")
    for i := 0; i < N; i++ {
        fmt.Scan(&cities[i])
    }

    // 判断是否能生成不冲突的缩写
    result := canGenerateUniqueAbbreviation(cities)
    if result == 1 {
        fmt.Println("可以生成不冲突的缩写，返回1")
    } else {
        fmt.Println("无法生成不冲突的缩写，返回0")
    }

    fmt.Println(canGenerateUniqueAbbreviation([]string{"FRANKFURT", "ZURICH", "LONDONHEATHROW"})) // 1
    fmt.Println(canGenerateUniqueAbbreviation([]string{"NEWYORK", "NEWJERSEY", "NEWPORT", "NEUSTADT"})) // 1
    fmt.Println(canGenerateUniqueAbbreviation([]string{"NEWYORK", "NEWYORK", "NEWARK"})) // 0

}

```

## 3

你在玩一款中等的横版游戏
这个游戏由一个字符串level表示，其中字符'-'表示陆地，'*'表陷阱；如果你走到陷阱则会死亡。
你在最左端；通过走路或者短跳；或者长跳的方式往右移动当你本死亡的情况下移动到最右端表示成功通关。最左端一定保证是陆地。
其中移动的方式如下(只能向右移动)
走路(按键盘S):往右走1格
短跳(按键盘H):往右走2格(略过中间1格)
长跳(按键盘J):往右走3格(略过中间2格)
请返回有多少种不同的方式可以通关?
如果结果为ans 对ans取模1000000007并返回。

示例1
输入
"----"
输出
4
说明
4种方法："SSSS","SH","HS","J"

示例2
输入
"-**-"
输出
1
说明
只能J

示例3
输入
"-*-****-*-"
输出
0
说明
无法通关

```go
package main

import "fmt"

const MOD = 1000000007

func countWays(level string) int {
    n := len(level)
    if level[0] == '*' {
        return 0 // 起点是陷阱，无法开始
    }

    // dp[i] 表示到达 i 位置的不同方法数
    dp := make([]int, n)
    dp[0] = 1 // 起点有 1 种方法

    for i := 0; i < n; i++ {
        if level[i] == '*' {
            continue // 如果当前位置是陷阱，跳过
        }

        // 走路：从 i 到 i+1
        if i+1 < n && level[i+1] != '*' {
            dp[i+1] = (dp[i+1] + dp[i]) % MOD
        }

        // 短跳：从 i 到 i+2
        if i+2 < n && level[i+2] != '*' {
            dp[i+2] = (dp[i+2] + dp[i]) % MOD
        }

        // 长跳：从 i 到 i+3
        if i+3 < n && level[i+3] != '*' {
            dp[i+3] = (dp[i+3] + dp[i]) % MOD
        }
    }

    return dp[n-1]
}

func main() {
    var level string
    fmt.Println("请输入关卡字符串 (由 '-' 和 '*' 组成)：")
    fmt.Scan(&level)
    // 测试示例1
    result := countWays(level)
    fmt.Println("不同的通关方式数为:", result)

    fmt.Println(countWays("----")) // 输出 4
    // 测试示例2
    fmt.Println(countWays("-**-")) // 输出 1
    // 测试示例3
    fmt.Println(countWays("-*-****-*-")) // 输出 0
}

```
