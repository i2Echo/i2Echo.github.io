---
title: 有序数组转化为二叉搜索树
tags: [算法， 二叉树， 递归]
date: 2017-07-11 23:40:46
categories: [算法]
---
## 前言
最近偶然在网上看到一道要求将一个有序数组转化为二叉树的算法题，就试着用js(ES6)实现一下。
<!--more-->
## 算法实现（采用ES6）
``` javascript
/**
 * 节点类
 */
class Node {
  constructor(data, left, right) {
    this.data = data;
    this.left = left;
    this.right = right;
  }
}

/**
 * //递归
 * @param {*Array} arr
 * @param {*Number} start
 * @param {*Number} end
 */
let creatBST = function ( arr, start, end ) {
  if ( start > end ) return
  let mid = Math.floor( (start+end)/2 )
  let root = new Node( arr[mid], null, null )

  root.left = creatBST( arr, start, mid-1 )
  root.right = creatBST( arr, mid+1, end )

  return root
}

let sortedArr2BST = arr => {
  if ( arr === null ) return null
  let len = arr.length
  return creatBST( arr, 0, len-1)
}
/**
 *
 * @param {*Obeject(Node)} root
 */
let printTree = function ( root ) {
  if ( root ) {
    printTree( root.left )
    console.log( root.data )
    printTree( root.right )
  }
}

// test
// const arr = []
// const arr0 = [1, 2, 3, 4, 5]
// const arr1 = [1, 2, 3, 4, 5, 6]
// let tree0 = sortedArr2BST( arr )
// let tree1 = sortedArr2BST( arr1 )
// printTree( tree0 )
// printTree( tree1 )

```