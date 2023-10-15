# swift-leetcode-records
力扣算法解题记录，Swift语言实现

#### 单调栈

##### [901. 股票价格跨度](https://leetcode.cn/problems/online-stock-span/description/)

> 及时去掉无用数据，保持栈中元素有序

``` swift
class StockSpanner {
    var arr: [[Int]]
    init() {
        arr = []
    }
    
    func next(_ price: Int) -> Int {
        var ans = 1
        while !arr.isEmpty, arr.last![0] <= price {
            ans += arr.last![1]
            arr.popLast()
        }
        arr.append([price, ans])
        return ans
    }
}

/**
 * Your StockSpanner object will be instantiated and called as such:
 * let obj = StockSpanner()
 * let ret_1: Int = obj.next(price)
 */
```
#### 设计数据结构

##### [2034. 股票价格波动](https://leetcode.cn/problems/stock-price-fluctuation/description/?envType=daily-question&envId=2023-10-08)

> 关键是取 current 要是O(1) 才行

``` swift
class StockPrice {
    var curt = 0
    var dic: [Int: Int]
    init() {
        dic = [:]
    }
    
    func update(_ timestamp: Int, _ price: Int) {
        if dic[timestamp] == nil {
            dic[timestamp] = price
        } else {
            dic[timestamp]! = price
        }
        curt = max(curt, timestamp)
    }
    
    func current() -> Int {
        return dic[curt]!
    }
    
    func maximum() -> Int {
        return dic.values.max()!
    }
    
    func minimum() -> Int {
        return dic.values.min()!
    }
}

/**
 * Your StockPrice object will be instantiated and called as such:
 * let obj = StockPrice()
 * obj.update(timestamp, price)
 * let ret_2: Int = obj.current()
 * let ret_3: Int = obj.maximum()
 * let ret_4: Int = obj.minimum()
 */
```

#### 双指针

##### [345. 反转字符串中的元音字母](https://leetcode.cn/problems/reverse-vowels-of-a-string/description/)

> 双指针+交换方法

``` swift
class Solution {
    func reverseVowels(_ s: String) -> String {
        if s.count == 0 {
            return s
        }
        let aset: Set<Character> = ["a", "e", "i", "o", "u", "A", "E", "I", "O", "U"]
        var arr = Array(s)
        var left = 0, right = s.count - 1
        while left < right {
            if !aset.contains(arr[left]) {
                left += 1
                continue
            }
            if !aset.contains(arr[right]) {
                right -= 1
                continue
            }
            arr.swapAt(left, right)
            left += 1
            right -= 1
        }
        return String(arr)
    }
}
```

##### [611. 有效三角形的个数](https://leetcode.cn/problems/valid-triangle-number/description/)

> 双指针

``` swift
class Solution {
    func triangleNumber(_ nums: [Int]) -> Int {
        if nums.count < 3 {
            return 0
        }
        let n = nums.sorted()
        var count = 0
        for i in 2..<n.count {

            var left = 0, right = i-1
            while left < right {
                if n[right] + n[left] > n[i] {
                    count += (right - left)
                    right -= 1
                } else {
                    left += 1
                }
            }
        }
        return count
    }
}
```

#### 脑筋急转弯

##### [2731. 移动机器人](https://leetcode.cn/problems/movement-of-robots/description/)

> 机器人都相同，对撞考虑成穿过，忽略对撞，只计算d秒后的每个坐标位置。再排序后计算两两坐标距离之和

``` swift
class Solution {
    func sumDistance(_ nums: [Int], _ s: String, _ d: Int) -> Int {
        var n = nums
        let arrS = Array(s)
        for i in 0..<nums.count {
            n[i] += arrS[i] == "R" ? d : -d
        }
        n.sort()

        var ans = 0, sum = 0
        for i in 0..<nums.count {
            // 1e9 值是Double类型，要转换成Int
            ans = (ans + i*n[i] - sum) % (Int(1e9)+7)
            sum += n[i]
        }
        return ans
    }
}
```

#### 贪心

##### [1488. 避免洪水泛滥](https://leetcode.cn/problems/avoid-flood-in-the-city/description/)

> arr存储晴天的天数，抽水优先在上一个该湖泊下雨日期之后的晴天抽，晴天的ans[i] = 1，是随便抽的一个，也可以写成其他值

``` swift
class Solution {
    func avoidFlood(_ rains: [Int]) -> [Int] {
        var dic: [Int: Int] = [:]
        var arr: [Int] = []
        var ans = Array(repeating: -1, count: rains.count)

        for i in 0..<rains.count {
            if rains[i] == 0 {
                arr.append(i)
                ans[i] = 1
                continue
            }

            if dic[rains[i]] != nil {
                let index = dic[rains[i]]!
                var firstIndex = -1
                for item in arr {
                    if item > index {
                        firstIndex = item
                        break
                    }
                }
                if firstIndex == -1 {
                    return []
                }
                ans[firstIndex] = rains[i]
                arr.remove(at: arr.firstIndex(of:firstIndex)!)
            }

            dic[rains[i]] = i
        }
        return ans
    }
}
```

#### 前后缀（最大、最小值）

##### [42. 接雨水](https://leetcode.cn/problems/trapping-rain-water/description/)

> 算出前后缀最大值，当前柱子能接到的水就是前后缀最大值中的最小值减去柱子高度

``` swift
class Solution {
    func trap(_ height: [Int]) -> Int {
        let len = height.count
        var arr1 = Array(repeating: 0, count: len)
        var arr2 = Array(repeating: 0, count: len)

        arr1[0] = height[0]
        for i in 1..<len {
            arr1[i] = max(height[i], arr1[i-1])
        }

        arr2[len-1] = height[len-1]
        for i in (0..<len-1).reversed() {
            arr2[i] = max(height[i], arr2[i+1])
        }
        var ans = 0
        for i in 0..<len {
            ans += min(arr1[i], arr2[i]) - height[i]
        }
        return ans
    }
}
```

#### 位运算

##### [137. 只出现一次的数字 II](https://leetcode.cn/problems/single-number-ii/description/)

> 统计出每个数的相同二进制位中1的个数，对3求余

``` swift
class Solution {
    func singleNumber(_ nums: [Int]) -> Int {
        var ans = 0
        for i in 0..<64 {
            var cnt = 0
            for item in nums {
                cnt += ((item >> i) & 1)
            }
            ans |= ((cnt % 3) << i)
        }
        return ans
    }
}
```

