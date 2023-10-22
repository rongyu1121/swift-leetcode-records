# swift-leetcode-records
力扣算法解题记录，Swift语言实现，记录不会做的题&会做但有更好解法的题

#### 1、单调栈

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

##### [739. 每日温度](https://leetcode.cn/problems/daily-temperatures/description/)

> 栈中记录还没算出下一个更大元素的数的下标

``` swift
class Solution {
    func dailyTemperatures(_ temperatures: [Int]) -> [Int] {
        // [73,74,75,71,69,72,76,73]
        var ans = Array(repeating: 0, count: temperatures.count)
        var arr: [Int] = []
        for i in 0..<temperatures.count {
            let item = temperatures[i]
            while !arr.isEmpty, item > temperatures[arr.last!] {
                let j = arr.popLast()!
                ans[j] = i - j
            }
            arr.append(i)
        }
        return ans
    }
}
```

#### 2、设计数据结构

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

##### [2530. 执行 K 次操作后的最大分数](https://leetcode.cn/problems/maximal-score-after-applying-k-operations/description/)

> Swift 没有堆的结构，需要自己实现。堆的概念，参考 [这个链接](https://zhuanlan.zhihu.com/p/399460271)。例如最大堆，数组不一定是有序的，但能保证左右子节点都比父节点小就可以了。

``` swift

class Solution {
    func maxKelements(_ nums: [Int], _ k: Int) -> Int {
        var mNums = nums
        heapify(&mNums)
        var ans = 0
        for _ in 0..<k {
            ans += mNums[0]
            mNums[0] = (mNums[0] + 2) / 3
            skin(&mNums, 0)
        }
        return ans
    }

    func heapify(_ nums: inout [Int]) {
        // 1个数不用执行下沉
        guard nums.count > 1 else {
            return
        }
        // 非叶子节点可以下沉，例如4层结构满叶子，叶子是8非叶子是7，非叶子下标最大是6
        // 反向遍历，肯定能将最大值带到下标0的位置
        for i in (0...nums.count/2 - 1).reversed() {
            skin(&nums, i)
        }
    }
    func skin(_ nums: inout [Int], _ l: Int) {
        let n = nums.count
        var i = l
        while 2 * i + 1 < n {
            var j = 2 * i + 1
            if j + 1 < n, nums[j+1] > nums[j] {
                j += 1
            }
            // 上面2步是找出左右儿子哪个大，最大的儿子都比nums[i]小，就结束下沉了
            if nums[j] <= nums[i] {
                break
            }
            // 有儿子比父亲nums[i]大，执行下沉，也就是父子交换
            nums.swapAt(i, j)
            i = j
        }
    }
}
```

#### 3、双指针

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

##### [2095. 删除链表的中间节点](https://leetcode.cn/problems/delete-the-middle-node-of-a-linked-list/description/)

> 快慢指针，从哨兵节点开始遍历

``` swift
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     public var val: Int
 *     public var next: ListNode?
 *     public init() { self.val = 0; self.next = nil; }
 *     public init(_ val: Int) { self.val = val; self.next = nil; }
 *     public init(_ val: Int, _ next: ListNode?) { self.val = val; self.next = next; }
 * }
 */
class Solution {
    func deleteMiddle(_ head: ListNode?) -> ListNode? {
        let dummy: ListNode? = ListNode(-1)
        var fast = dummy
        var slow = dummy
        dummy!.next = head
        while fast?.next != nil, fast?.next?.next != nil {
            fast = fast?.next?.next
            slow = slow?.next
        }
        slow?.next = slow?.next?.next
        return dummy?.next
    }
}
```

#### 4、脑筋急转弯

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

#### 5、贪心

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

##### [1402. 做菜顺序](https://leetcode.cn/problems/reducing-dishes/description/)

> 排序后贪心

``` swift
class Solution {
    func maxSatisfaction(_ satisfaction: [Int]) -> Int {
        let s = satisfaction.sorted(by: >)
        // 优先做满意度高的菜
        var ans = 0, sum = 0
        for item in s {
            sum += item
            if sum < 0 {
                break
            }
            // 前缀和小于0，ans值达到峰值后开始下降
            ans += sum
        }
        return ans
    }
}
```

#### 6、前后缀（最大、最小值）

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

##### [100101. 找出满足差值条件的下标 II](https://leetcode.cn/problems/find-indices-with-index-and-value-difference-ii/description/)

> 记录一段距离之前的前缀最大、最小值的下标

``` swift
class Solution {
    func findIndices(_ nums: [Int], _ indexDifference: Int, _ valueDifference: Int) -> [Int] {
        if indexDifference > nums.count {
            return [-1,-1]
        }

        let n = nums.count
        var minIndex = 0
        var maxIndex = 0

        for i in indexDifference..<n {
            let j = i - indexDifference
            if nums[j] < nums[minIndex] {
                minIndex = j
            }
            if nums[j] > nums[maxIndex] {
                maxIndex = j
            }
            if abs(nums[i] - nums[minIndex]) >= valueDifference{
                return [minIndex, i]
            }
            if abs(nums[i] - nums[maxIndex]) >= valueDifference{
                return [maxIndex, i]
            }
        }
        return [-1,-1]
    }
}
```

#### 7、位运算

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

#### 8、滑动窗口

##### [1456. 定长子串中元音的最大数目](https://leetcode.cn/problems/maximum-number-of-vowels-in-a-substring-of-given-length/description/)

> 一边滑一边记录，左出右进之后，元音字符有没有增加

``` swift
class Solution {
    func maxVowels(_ s: String, _ k: Int) -> Int {
        let arr = Array(s)
        let aset: Set<Character> = ["a","e","i","o","u"]
        var ans = Array(arr[0..<k]).filter {aset.contains($0)}.count
    
        var left = 1
        var count = ans
        while left <= arr.count-k {
            let l = aset.contains(arr[left-1]) ? 1 : 0
            let r = aset.contains(arr[left+k-1]) ? 1 : 0
            count += r-l
            ans = max(ans, count)
            left += 1
        }
        
        return ans
    }
}
```

##### [1004. 最大连续1的个数 III](https://leetcode.cn/problems/max-consecutive-ones-iii/description/)

> 记录0的个数 <=k 时的最大窗口距离，当个数大于k，就从左边移出一个

``` swift
class Solution {
    func longestOnes(_ nums: [Int], _ k: Int) -> Int {
        var ans = 0
        var cnt0 = 0
        var left = 0
        for i in 0..<nums.count {
            cnt0 += 1 - nums[i]
            while cnt0 > k {
                cnt0 -= 1 - nums[left]
                left += 1
            }
            ans = max(ans, i - left + 1)
        }
        return ans
    }
}
```

##### [209. 长度最小的子数组](https://leetcode.cn/problems/minimum-size-subarray-sum/description/)

> 当>=target 时，记录最小区间，同时左指针右移

``` swift
class Solution {
    func minSubArrayLen(_ target: Int, _ nums: [Int]) -> Int {
        var ans = nums.count + 1
        var s = 0
        var left = 0
        for i in 0..<nums.count {
            s += nums[i]
            while s >= target {
                ans = min(ans, i - left + 1)
                s -= nums[left]
                left += 1
            }
        }
        return ans <= nums.count ? ans : 0
    }
}
```

#### 9、哈希表

##### [1726. 同积元组](https://leetcode.cn/problems/tuple-with-same-product/description/)

> 把所有可能乘积记录下来，累加每种乘积的组合数，再对每种答案*8

``` swift
class Solution {
    func tupleSameProduct(_ nums: [Int]) -> Int {
        var dic: [Int: Int] = [:]
        for i in 0..<nums.count {
            for j in i+1..<nums.count {
                if dic[nums[i]*nums[j]] == nil {
                    dic[nums[i]*nums[j]] = 1
                } else {
                    dic[nums[i]*nums[j]]! += 1
                }
            }
        }
        var ans = 0
        for (key, val) in dic {
            ans += val * (val - 1) / 2
        }
        return ans * 8
    }
}
```

#### 10、图

##### [2316. 统计无向图中无法互相到达点对数](https://leetcode.cn/problems/count-unreachable-pairs-of-nodes-in-an-undirected-graph/description/)

> 无向图、建图、连通块

``` swift
class Solution {
    func countPairs(_ n: Int, _ edges: [[Int]]) -> Int {
       var g = Array(repeating: [Int](), count: n)
       for item in edges {
           g[item[0]].append(item[1])
           g[item[1]].append(item[0]) 
       }//建图

       var vis = Array(repeating: false, count: n)

       func dfs(_ x: Int) -> Int {
           vis[x] = true
           var size = 1
           for item in g[x] {
               if !vis[item] {
                   size += dfs(item)
               }
           }
           return size
       }//得到连通块的大小

       var ans = 0, total = 0
       for i in 0..<n {
           if !vis[i] {
               var size = dfs(i)
               ans += size * total
               total += size
           }
       }//每个新的连通块的大小*前面得到块的大小和
       return ans
    }
}
```

