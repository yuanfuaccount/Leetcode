题目描述：
Given a binary tree root, the task is to return the maximum sum of all keys of any sub-tree which is also a Binary Search Tree (BST).
Assume a BST is defined as follows:
* The left subtree of a node contains only nodes with keys less than the node’s key.
* The right subtree of a node contains only nodes with keys greater than the node’s key.
* Both the left and right subtrees must also be binary search trees.

Example 1:
![image](https://github.com/yuanfuaccount/Leetcode/blob/master/images/1373sample1.png)

Input: root = [1,4,3,2,4,2,5,null,null,null,null,null,null,4,6]
Output: 20
Explanation: Maximum sum in a valid Binary search tree is obtained in root node with key equal to 3.

Example 2:
![image](https://github.com/yuanfuaccount/Leetcode/blob/master/images/1373sample2.png)

Input: root = [4,3,null,1,2]
Output: 2
Explanation: Maximum sum in a valid Binary search tree is obtained in a single root node with key equal to 2.

Example 3:

Input: root = [-4,-2,-5]
Output: 0
Explanation: All values are negatives. Return an empty BST.

Example 4:

Input: root = [2,1,3]
Output: 6

Tips:
* Create a datastructure with 4 parameters: (sum, isBST, maxLeft, minLeft).
* In each node compute theses parameters, following the conditions of a Binary Search Tree.

题解：
这道题分为两个步骤：
1.判断一棵树是不是BST
2.求最大值
一开始的想法是先写一个判断BST的函数，然后遍历原始二叉树去判断以某节点为根节点的二叉树是不是BST，最后针对以该节点为根节点的所有元素求和。这样一看
就非常复杂，因为光是遍历二叉树操作就进行了2+n遍。

然后原题有两条提示。一看就是需要针对以某节点为根节点的二叉树返回该元组，采用从底向上的递归操作，对于每个节点，都返回该元组，同时传递一个参数记录最大值。该方法的优点是只进行了1遍遍历，时间复杂度为O(n)。所以先构造一个返回该元组的递归函数，如下：

C++代码：
//(sum, isBST, maxLeft, minLeft)分别代表以root作为根节点的二叉树的和，是否是BST，整棵树的最大值和最小值
vector<int> maxBST(TreeNode* root,int& maxVal)   //返回的vector定义为(sum, isBST, maxLeft, minLeft)
    {
        if(root==NULL) return {0,1,INT_MIN,INT_MAX};  //base_case
        vector<int> left=maxBST(root->left,maxVal); //递归左子树
        vector<int> right=maxBST(root->right,maxVal);  //递归右子树
        int sum=root->val; //求和
        int isBST=1,minLeft=0,maxLeft=0;  //左右子树为空时不需要进行任何操作，sum,isBST,maxLeft,minLeft每个节点都更新
        if(left[3]!=INT_MAX)  //左子树不为空
        {
            if(left[1]==0 || root->val<=left[2]) //该树不是BST
                isBST=0;
            sum+=left[0];
        }
        if(right[2]!=INT_MIN) //右子树不为空
        {
            if(right[1]==0 || root->val>=right[3])  //不是二叉树
                isBST=0;
            sum+=right[0];
        }
        minLeft=min(left[3],root->val);  //更新最大，最小值
        maxLeft=max(root->val,right[2]);
        if(isBST==1 && sum>maxVal) maxVal=sum; //如果是BST，更新求和结果
        return {sum,isBST,maxLeft,minLeft};

    }
    
主函数调用该函数即可：
int maxSumBST(TreeNode* root) {
        if(root==NULL) return 0;
        int maxVal=0;
        vector<int> res=maxBST(root,maxVal);
        return maxVal;
    }


