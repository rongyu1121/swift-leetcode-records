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



