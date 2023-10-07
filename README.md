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


