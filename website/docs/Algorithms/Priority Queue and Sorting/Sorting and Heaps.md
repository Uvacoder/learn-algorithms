---
---

# Heaps
> Often useful for scheduling/assigning the next available smallest/largest element to a subset. 
## Max heaps
To make a max heap, push -x for x in array onto a min heap.

[LC-23. Merge k Sorted Lists](</docs/Some Leetcode Questions/LC-23. Merge k Sorted Lists.md>)- Put the head of each linked list on a heap
- Extend the resulting linked list
- If the extended tail has a next node, put it back on the heap

[LC-1851. Minimum Interval to Include Each Query](</docs/Some Leetcode Questions/LC-1851. Minimum Interval to Include Each Query.md>)- Find the smallest interval that can include each query (array of nums)
- Sort queries and intervals
- Put all intervals ``[l,r]`` that include q onto the heap with key ``[l-r+1, r]``
- Pop all intervals where `r` < q
- Store results in dictionary because we sorted our input 

- [ ] https://leetcode.com/problems/the-skyline-problem/description/

- [ ] https://leetcode.com/problems/task-scheduler/description/

- [ ] https://leetcode.com/problems/trapping-rain-water-ii/description/
- [ ] https://leetcode.com/problems/number-of-visible-people-in-a-queue/description/

### Max Profit from Sequential Projects with Minimum Capital Requirement
https://leetcode.com/problems/ipo/description
- Greedy: 
```python
def findMaximizedCapital(self, k: int, w: int, profits: List[int], capital: List[int]) -> int:
        # we want to start with projects with the lowest capital but highest profits
        # so we push all projects with requirement <= current money onto a maxheap
        projects = list(zip(capital, profits))
        projects.sort()
        n = len(profits)
        pq = []
        i = 0
        for j in range(k):
            while i < n and projects[i][0] <= w:
                heappush(pq, -projects[i][1])
                i += 1
            if not pq:
                break
            w += -heappop(pq)
        return w

```

### Maximum Total Area of All Possible Rectangles
Amazon games have introduced a new mathematical game for kids. You will be given n sticks and the player is required to form rectangles from those sticks.  
Formally, given an array of n integers representing the lengths of the sticks, you are required to create rectangles using those sticks. Note that a particular stick can be used in at most one rectangle and in order to create a rectangle we must have exactly two pairs of sticks with the same lengths. For example, you can create a rectangle using sticks of lengths [2, 2, 5, 5] and [4, 4, 4, 4] but not with [3, 3, 5, 8]. The goal of the game is to maximize the total sum of areas of all the rectangles formed.  
In order to make the game more interesting, we are allowed to reduce any integer by at most 1. Given the array sideLengths, representing the length of the sticks, find the maximum sum of areas of rectangles that can be formed such that each element of the array can be used as length or breadth of at most one rectangle and you are allowed to decrease any integer by at most 1. Since this number can be quite large, return the answer modulo 109+7.  
Note: It is not a requirement that all side lengths be used. Also, a modulo b here represents the remainder obtained when an integer a is divided by an integer b.  
Example  
The side lengths are given as sideLengths = [2, 6, 6, 2, 3, 5].  
The lengths 2, 2, 6, and 6 can be used to form a rectangle of area $2*6=12$. No other rectangles can be formed with the remaining lengths. The answer is 12 modulo (109+7)= 12.  
Function Description  
Complete the function getMaxTotalArea in the editor below.  
getMaxTotalArea has the following parameter(s):  
int sideLengths[n]: the side lengths that can be used to form rectangles  
Returns  
int: the maximum total area of the rectangles that can be formed, modulo (109+7).

- Get the 4 largest sticks repeatedly
```python
def total_area(nums):
	nums = [-x for x in nums]
	heapify(nums)
	area = 0
	while len(nums) >= 4:
		n1,n2 = heappop(nums),heappop(nums)
		n3,n4 = heappop(nums),heappop(nums)
		if n1-n2 <= 1 and n3-n4 <= 1:
			area += min(n1, n2) * min(n3,n4)
		elif n1-n2 <= 1:
			heappush(nums, n1)
			heappush(nums, n2)
		elif n3-n4 <= 1:
			heappush(nums, n3)
			heappush(nums, n4)
			if nums2-nums3 <= 1:
				heappush(nums, n2)
	return area
```

### Find K Pairs with Smallest Sums Given Two Non Decreasing Arrays
https://leetcode.com/problems/find-k-pairs-with-smallest-sums/description
- Push smallest pairs on to the heap, we do this a max of 2k times
- Keep track of pairs that have been added to avoid duplicates. 

```python
def kSmallestPairs(self, nums1: List[int], nums2: List[int], k: int) -> List[List[int]]:
        pq = [(nums1[0]+ nums2[0], 0, 0)]
        res = []
        seen = set([(0,0)])
        while k and pq:
            s, i, j = heappop(pq)
            res.append([nums1[i], nums2[j]])
            if i < len(nums1) - 1 and (i+1, j) not in seen:
                heappush(pq, (nums1[i+1] + nums2[j], i+1, j))
                seen.add((i+1, j))
            if j < len(nums2) - 1 and (i, j+1) not in seen:
                heappush(pq, (nums1[i] + nums2[j+1], i, j+1))
                seen.add((i, j+1))
            k -= 1
        return res
```

## Building Valid Subsequence
> Heaps are useful for providing the next available smallest/largest element. 

### [Divide Array Into Increasing Sequences](https://leetcode.com/problems/divide-array-into-increasing-sequences/)
>Given an integer array `nums` sorted in non-decreasing order and an integer `k`, return `true` if this array can be divided into one or more disjoint increasing subsequences of length at least `k`
- DFS is not useful here since sequences can start to overlap
```python
def canDivideIntoSubsequences(self, nums: List[int], k: int) -> bool:
        freq = Counter(nums)
        mx = max(freq.values())
        seq = [0]*mx
        for i in range(len(nums)):
            l = heappop(seq)
            heappush(seq, l+1)
        return seq[0] >= k
```
- There is an optimal O($n$) solution but the goal is to recognize when a heap could be useful
# Advanced Heaps

[LC-2386. Find the K-Sum of an Array](</docs/Some Leetcode Questions/LC-2386. Find the K-Sum of an Array.md>)- Find the k-th largest array where nums contains positive and negative numbers
- Maxheap (minheap with -nums)
- Keep a sorted list of abs(nums) in increasing order
- The next biggest sum sum can either be:
	- Adding the current number and subtracting the previous number
	- Not reverting the subtraction of the previous number and continuing to substract the current number
- Push the next biggest sum onto the heap
- O(NlogN + klogk) where NlogN is to sort all N numbers, and klogk is to maintain the heap since we push at most 2k sums to the heap.

```python
def kSum(self, nums: List[int], k: int) -> int:
        maxSum = sum([max(0, num) for num in nums])
        absNums = sorted([abs(num) for num in nums])
        maxHeap = [(-maxSum + absNums[0], 0)]
        nextSum = -maxSum
        for _ in range(k-1):
            nextSum, i = heappop(maxHeap)
            if i + 1 < len(absNums):
                heappush(maxHeap, (nextSum - absNums[i] + absNums[i + 1], i + 1))
                heappush(maxHeap, (nextSum + absNums[i + 1], i + 1))
        return -nextSum
```