---
title: "Why MySQL Uses B+Tree As The Index?"
datePublished: Sat Mar 04 2023 17:13:11 GMT+0000 (Coordinated Universal Time)
cuid: cleu83ns3000a09l2bvi22pv0
slug: why-mysql-uses-btree-as-the-index
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/Y9kOsyoWyaU/upload/8304db2e1e6ec986f1046036075a23b9.jpeg
tags: mysql, databases, data-structures

---

A tree data structure is prevalent in database index design. It can always keep the data sorted, so users can search for exact matches and ranges more efficiently. But why choose B+tree over other tree structures?

## How about Binary Search Tree?

Let's try to use a binary search tree as the database index. Suppose we have a table and the binary search tree index as below.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1676200506092/c5e1a65c-66e7-4642-8774-0b495d155df6.png align="center")

Let's try to look up 88 from the table.

```sql
select col2 from table where col2 = 88;
```

Without the index, MySQL must begin with the first row and read the whole table until row 6 to find the result(picture on the left). However, if MySQL uses the binary search tree as the index, after checking the root node which is 31, 88 is greater than 31, so MySQL chooses to visit the right child of the root, then the result is found.

In this situation, the binary search tree index seems reasonable enough, but how about other cases? Let's take a look at another example.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1676202127780/2909f750-005e-4487-8b40-09aaba712f6e.png align="center")

The binary search tree will become a list if the data is sorted. If MySQL tries to find the row where col 2 is 50, even if with the index, it still needs to go through the whole 'tree' from the root to the end. In this situation, the binary search tree index is pointless. We need a self-balancing binary search tree to keep the index balanced.

## How about **Red Black Tree?**

![What is Red-Black Tree? - KK JavaTutorials](https://kkjavatutorials.com/wp-content/uploads/2020/07/Red-Black-Tree.jpg align="left")

On the other hand, the Red-black tree is optimized for in-memory operations and is not as efficient for disk-based operations. The red-black tree uses more memory than B+tree because it stores data in its internal nodes, which can be a disadvantage when memory is limited. Even though the red-black tree can be stored on the disk, with the same amount of data, it will cost much more space than B+Tree.

Another reason why B+tree is preferred over the red-black tree in database systems is that B+tree is designed to support efficient range queries, which are common in database queries. B+tree's design enables it to retrieve all the data within a specific range efficiently, while the red-black tree requires traversing the entire tree to retrieve all data within a range, which can be slow and inefficient for large datasets.

When it comes to disk-based storage and retrieval of large datasets. The red-black tree does not have a fixed structure and its depth can vary depending on the dataset, which can lead to increased traversal and disk read operations.

Suppose we have a database table with 1 million records, and we want to retrieve all records within a specific range of values. With a Red-black tree index, the database system would need to traverse the entire tree to retrieve all data within the range, which could take a long time and result in many disks reads.

## How about B Tree?

![Interview Questions: Database Indexes](https://th.bing.com/th/id/R.2dedb39142502142fec40b8ec592105d?rik=xgiwcVnnqc83Ww&riu=http%3a%2f%2fassets.20bits.com%2f20080513%2fb-tree.png&ehk=UGzdUEPjd29ZYqJAXX5p4X9NxNyx3iCQR5OXO520uAo%3d&risl=&pid=ImgRaw&r=0 align="left")

One of the main advantages of B+tree over B-tree is its support for efficient range queries. B+tree is designed to store data only in its leaf nodes, while the non-leaf nodes only store keys to the data in the leaf nodes. This design allows B+tree to support range queries efficiently by performing a range scan over the leaf nodes.

In contrast, B-tree stores both keys and data in its nodes, which can result in a larger number of disk accesses required to perform range queries. While B-tree can still support range queries, it may require traversing the entire tree, which can be inefficient for large datasets.

Another advantage of B+tree over B-tree is its performance in disk-based operations. B+tree is optimized for disk read and write operations, which are crucial in database systems. B+tree's design allows it to reduce the number of disk accesses required to access the data, making it more efficient than B-tree for large datasets.

## B+Tree

![[MySQL FAQ]系列 — 为什么InnoDB表要建议用自增列做主键 | IT瘾](https://th.bing.com/th/id/R.d6e3984272fa2c94318052a2cd95ad5c?rik=Mnn%2fmvWcOis%2fiw&riu=http%3a%2f%2fimysql.com%2fwp-content%2fuploads%2f2014%2f09%2fB-tree.png&ehk=13HozsBNVqoGKP%2fqcbzaac0z4Qp1lTebwDbsKpqdfF8%3d&risl=&pid=ImgRaw&r=0 align="left")

One of the main advantages of B+tree over other tree data structures is its ability to efficiently store and retrieve large amounts of data. B+tree is optimized for disk read and write operations, which are crucial in database systems. B+tree's design allows it to reduce the number of disk accesses required to access the data, making it more efficient than other tree structures for large datasets.

Another advantage of B+tree over other tree structures like binary search trees and red-black trees is its support for efficient range queries. B+tree is designed to store data only in its leaf nodes, while the non-leaf nodes only store keys to the data in the leaf nodes. This design allows B+tree to support range queries efficiently by performing a range scan over the leaf nodes.

In contrast, other tree structures like binary search trees and red-black trees store both keys and data in their nodes, which can result in a larger number of disk accesses required to perform range queries. While these structures can still support range queries, they may require traversing the entire tree, which can be inefficient for large datasets.

Overall, B+tree is the preferred choice for database indexing due to its support for efficient range queries, optimized design for disk-based operations, and ability to efficiently store and retrieve large amounts of data.