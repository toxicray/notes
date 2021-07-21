# 							   LEETCODE

## 一、二进制数据



#### [231. 2 的幂](https://leetcode-cn.com/problems/power-of-two/)



#### [342. 4的幂](https://leetcode-cn.com/problems/power-of-four/)







## 二、二叉树



### 1,叉树的中序遍历

#### [94. 二叉树的中序遍历](https://leetcode-cn.com/problems/binary-tree-inorder-traversal/)

##### 关键点:  左边入栈,借助栈来实现

```
		Stack<TreeNode> stack = new Stack<TreeNode>();
        List<Integer> list = new ArrayList<>();
        TreeNode cur = root;
        stack.add(root);
        while(cur != null || !stack.isEmpty()) {
            if (cur != null) {
                stack.push(cur);
                cur = cur.left;
            } else {
                cur = stack.pop();
                list.add(cur.val);
                cur = cur.right;
            }
        }
        return list;
```

#### [257. 二叉树的所有路径](https://leetcode-cn.com/problems/binary-tree-paths/)

##### 关键点:理解dfs  边界处理,非递归实现?



#### 二叉树的前序遍历

```
二叉树的前序遍历，非递归实现，本质是需要一个栈来进行辅助操作


public List<Integer> preorderTraversal(TreeNode root) {
       List<Integer> result = new ArrayList<>();
        if (root == null) {
            return result;
        }
        Stack<TreeNode> stack = new Stack<>();
        stack.push(root);
        while (!stack.isEmpty()) {
            TreeNode pop = stack.pop();
            if (pop != null) {
                result.add(pop.val);
                stack.push(pop.right);
                stack.push(pop.left);
            }
        }
        return result;
    }
```



## 三、排序

##### 快速排序

```
private static void qSort(int[] arr, int left, int right) {
        if (left < right) {
            int pivot = sort(arr, left, right);
            qSort(arr, left,pivot);
            qSort(arr, pivot+1,right);
        }
    }

    private static int sort(int[] arr, int left, int right) {
        int pivotIndex = left;
        int pivot = arr[left];
        while(left != right) {
            while(arr[right] >= pivot && left != right) {
                right--;
            }
            while(arr[left] <= pivot && left != right) {
                left++;
            }
            int temp = arr[left];
            arr[left] = arr[right];
            arr[right] = temp;
        }
        arr[pivotIndex] = arr[left];
        arr[left] = pivot;
        return left;
    }
```





## 四、贪心算法

#### [55. 跳跃游戏](https://leetcode-cn.com/problems/jump-game/)

```
public boolean canJump(int[] nums) {
        int maxL = nums[0];
        if(nums.length <= 1) {
            return true;
        }
        for (int i = 1; i < nums.length; i++) {
            if (i <= maxL) {
                maxL = Math.max(maxL,i + nums[i]);
            } 
        }
        return maxL >= nums.length - 1;
    }
```

## 五、回溯

#### [39. 组合总和](https://leetcode-cn.com/problems/combination-sum/)

```
public List<List<Integer>> combinationSum(int[] candidates, int target) {
        List<List<Integer>> result = new ArrayList<>();
        Arrays.sort(candidates);
        backTrack(result, candidates, target, new ArrayList(), 0); 
        return result;
    }

    private void backTrack(List<List<Integer>> result, int[] candidates, int target, ArrayList arrayList, int i) {
        if (target == 0) {
            result.add(new ArrayList<>(arrayList));
            return;
        }
        if (target < 0 ) {
            return;
        }
        for (int start = i; start < candidates.length; start++) {
            arrayList.add(candidates[start]);
            backTrack(result, candidates, target - candidates[start], arrayList, start);
            arrayList.remove(arrayList.size() - 1);
        }
    }
```

