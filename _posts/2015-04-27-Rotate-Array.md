Rotate an array of n elements to the right by k steps.

For example, with n = 7 and k = 3, the array [1,2,3,4,5,6,7] is rotated to [5,6,7,1,2,3,4].

<!--more-->

**Note:**

Try to come up as many solutions as you can, there are at least 3 different ways to solve this problem. 

###分析

这道题如果是用空间复杂度为O(n)或O(k)来做都非常简单，但是如果O(1)就比较难。

**解法1**

最笨的方法，每次用一个变量暂存最后一个元素，再将1～n-1的元素往后移，最后将暂存的值放到数组最前面。这样虽然实现了O(1)，但是时间复杂度太大了，当数组较大时会超时，不可取。

**解法2**

假如数组为[1,2,3,4,5,6,7]，n = 7，k = 2，那么此时我们提取出暂存3，将1置到3的位置，然后暂存5，将原来的3置到5的位置，依次类推，但是什么时候结束呢？

	[1,2,3,4,5,6,7]	
	[1,2,1,4,5,6,7]	//取出array[0]的1，计算它的新位置newIndex=(0+2)%7=2，并将array[2]=3暂存
	[1,2,1,4,3,6,7]	//计算3的新位置newIndex=(2+2)%7=4，暂存array[4]=5，将3置到newIndex=4
	[1,2,1,4,3,6,5]	//依次类推
	[1,7,1,4,3,6,5]
	[1,7,1,2,3,6,5]
	[1,7,1,2,3,4,5]
	[6,7,1,2,3,4,5]	//最终的结束条件是6置到`0`，0是最初index，这个可以作为我们循环结束的条件

但是，注意到n = 6，k = 2的情况：

	[1,2,3,4,5,6]
	[1,2,1,4,5,6]
	[1,2,1,4,3,6]
	[5,2,1,4,3,6]	//注意到此时5已经到了index=0的位置，循环已经终止，但是2,4,6的位置并没有改变

此时我们可以认为[1,2,3,4,5,6]被分配成了两组数据[1,3,5]和[2,4,6]，我们只需分别对它们进行处理即可。深入分析后，其实n个数据被分成了m组，其中m为n和k的最大公因子。

代码如下：

	class Solution {
	public:
		void rotate(int nums[], int n, int k) {
		    if(n < 2 || k % n == 0) return;
		    k = k % n;
		    subRotate(nums, n, k, 0);	//第一组肯定是要处理的
		    int start = 1;
		    while(start < maxCommonFactor(n, k)) {	//如果最大公因子大于1，则对剩下的组进行处理
		        subRotate(nums, n, k, start);
		        start++;
		    }
		}
		void subRotate(int nums[], int n, int k, int start) {
		    int newIndex = (start + k) % n;
		    int savedNum = nums[newIndex];
		    int changeNum = 0;
		    nums[newIndex] = nums[start];
		    while(newIndex != start) {
		        newIndex = (newIndex + k) % n;
		        changeNum = savedNum;
		        savedNum = nums[newIndex];  //保存下来
		        nums[newIndex] = changeNum;
		    }
		}
		int maxCommonFactor(int n, int k) {	//求n和k的最大公因子
		    int factor = 1;
		    for(int i = 1; i <= k; i++) {
		        if(n % i == 0 && k % i == 0) {
		            factor = i;
		        }
		    }
		    return factor;
		}
	};

**解法3**

这是九章算法里给出的解法，非常巧妙，通过下面的过程来进行分析：

	n = 6，k = 2
	[1,2,3,4,5,6]
	[1,2,3,4 | 5,6]，分成两组，长度分别为 n - k 和 k
	[4,3,2,1 | 6,5]，分别对这两个组进行reverse操作
	[5,6,1,2,3,4]，对整个数组进行reverse操作

代码如下：

	class Solution {
	public:
		void rotate(int nums[], int n, int k) {
		    if(n == 0)
		        return;
		    k = k % n;
		    if(k == 0)
		        return;
		    reverse(nums, 0, n - k - 1);
		    reverse(nums, n - k, n - 1);
		    reverse(nums, 0, n - 1);
		}
	private:
		void reverse(int nums[], int start, int end) {
		    while(start < end) {
		        int tmp = nums[start];
		        nums[start] = nums[end];
		        nums[end] = tmp;
		        start++;
		        end--;
		    }
		}
	};
	
