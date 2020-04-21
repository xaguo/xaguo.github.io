---
title: JavaScript实现二叉树
date: 2017-09-14 22:35:24
tags: [数据结构,JavaScript]
---

排序二叉树的JavaScript编码实现
<!-- more -->

二叉树：所有节点的度都小于2的树。
排序二叉树：左孩子的值小于右孩子的值。

通过前序遍历[O(n)]可以复制一课二叉树，效率比新建[O(n*log(n))]一棵二叉树高。

排序二叉树编码实现

	```js
	 function BinaryTree() {
	        var Node = function (key) {
	            this.key = key;
	            this.left = null;
	            this.right = null;
	        }
	        var root = null;//根节点
	        //插入节点
	        var insertNode = function (node, newNode) {
	            if (newNode.key < node.key) {
	                if (node.left == null) {
	                    node.left = newNode;
	                } else {
	                    insertNode(node.left, newNode);
	                }
	            } else if (newNode.key > node.key) {
	                if (node.right == null) {
	                    node.right = newNode;
	                } else {
	                    insertNode(node.right, newNode);
	                }
	            }
	        }
	        this.insert = function (key) {
	            var newNode = new Node(key);
	            if (root === null) {
	                root = newNode;
	            } else {
	                insertNode(root, newNode);
	            }
	        }
	        //中序遍历
	        var inOrderTraverseNode = function(node,callback){
	            if (node!=null){
	                inOrderTraverseNode(node.left,callback);
	                callback(node.key);
	                inOrderTraverseNode(node.right,callback);
	            }
	            }
	        this.inOrderTraverse = function (callback) {
	            //接口中传入回调函数
	            inOrderTraverseNode(root,callback);
	        }
	        //前序遍历
	        var preOrderTraverseNode = function(node,callback){
	            if (node!=null){
	                callback(node.key);
	                preOrderTraverseNode(node.left,callback);
	                preOrderTraverseNode(node.right,callback);
	            }
	        }
	        this.preOrderTraverse = function (callback) {
	            //接口中传入回调函数
	            preOrderTraverseNode(root,callback);
	        }
	        //后序遍历
	        var posTraverseNode = function(node,callback){
	            if (node!=null){
	                posTraverseNode(node.left,callback);
	                posTraverseNode(node.right,callback);
	                callback(node.key);
	            }
	        }
	        this.posTraverse = function (callback) {
	            //接口中传入回调函数
	            posTraverseNode(root,callback);
	        }
	        //查找最小值
	        var minNode=function (node) {
	            if(node){
	                while(node&&node.left!==null){
	                    node=node.left;
	                }
	                return node.key;
	            }
	            return null;
	        }
	        this.min=function () {
	            return minNode(root);
	        }
	        //查找最大值
	        var maxNode=function (node) {
	            if(node){
	                while(node&&node.right!==null){
	                    node=node.right;
	                }
	                return node.key;
	            }
	            return null;
	        }
	        this.max=function () {
	            return maxNode(root);
	        }
	        //查找给定值
	        var searchNode=function (node,val) {
	            if(node==null){
	                return false;
	            }
	            if(node.key===val){
	                return node.key;
	            }else if(val<node.key){
	               return searchNode(node.left,val);
	            }else if(val>node.key){
	                return searchNode(node.right,val);
	            }
	//            if(node){
	//                while(node&&node.right!==null){
	//                    if(node.key===val){
	//                        return node.key
	//                    }else if(node.key>val){
	//                        node=node.left;
	//                    }else if(node.key<val){
	//                        node=node.right;
	//                    }
	//                }
	//                return node.key;
	//            }
	        }
	        this.search=function (val) {
	            return searchNode(root,val);
	        }
	        //删除节点
	        var findMinNode=function (node) {
	            if(node){
	                while(node&&node.left!==null){
	                    node=node.left;
	                }
	                return node;
	            }
	            return null;
	        }
	        var removeNode=function(node, key){
	            if(node===null){
	                return null;
	            }
	            if(key<node.key){
	                node.left=removeNode(node.left,key);
	                return node;
	            }else if(key>node.key){
	                node.right=removeNode(node.right,key);
	                return node;
	            }else{
	                //叶子节点
	                if(node.left===null&&node.right===null){
	                    node=null;
	                    return node;
	                }
	                if(node.left===null){ //只有右节点
	                    node=node.right;
	                    return node;
	                }else  if(node.right===null){//只有左节点
	                    node=node.left;
	                    return node;
	                }
	                //有两个孩子
	                var aux=findMinNode(node.right);
	                node.key=aux.key;
	                node.right=removeNode(node.right,aux.key);
	                return node;
	            }
	        }
	        this.remove=function (key) {
	            root = removeNode(root, key);
	        }
	    }
	    var nodes = [8, 3, 10, 1, 6, 14, 4, 7, 13];
	    var binaryTree=new BinaryTree();
	    nodes.forEach(function (key) {
	        binaryTree.insert(key);
	    });
	    var callback = function (key) {
	        console.log(key);
	    }
	    //遍历
	    binaryTree.inOrderTraverse(callback);
	    //binaryTree.preOrderTraverse(callback);
	    //binaryTree.posTraverse(callback);
	    //console.log('the min node is:'+binaryTree.min());
	    //console.log('the max node is:'+binaryTree.max());
	    //console.log(binaryTree.search(7)?'key 7 is found':'key 7 is not found');
		// console.log(binaryTree.search(9)?'key 9 is found':'key 9 is not found');
	    binaryTree.remove(3);
	    binaryTree.inOrderTraverse(callback);
	```