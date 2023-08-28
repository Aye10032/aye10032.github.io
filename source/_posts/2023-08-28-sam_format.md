---
layout: post	
title: "sam/bam文件格式信息"	
date: 2023-08-28 09:21:05	
updated: 2023-08-28 09:21:05	
excerpt: "数据和头部格式信息"	
categories: 
- 学习
- 生物信息学
tag: 
- 学习
- 生物信息学
mermaid: false
typora-root-url: ..
---



# SAM/BAM文件格式[^1]

## 一、head

头部以@开头，字段之间使用制表符分割，每一个字段的键值之间使用冒号连接

### 示例：

```
@HD	VN:1.0	SO:coordinate
@SQ	SN:chr1	LN:248956422
@SQ	SN:chr10	LN:133797422
@SQ	SN:chr11	LN:135086622
@SQ	SN:chr11_KI270721v1_random	LN:100316
……
@PG	ID:samtools	PN:samtools	PP:hisat2	VN:1.18	CL:samtools view -@ 8 -bS -o H1.bam H1.sam
……
```



### 字段含义：

- **@HD**：文件的元信息，若存在，则位于文件开头，有且只有一行
  - **VN\***：格式的版本信息，仅包含数字和小数点
  - **SO**：排序顺序，可能的值包括
    - unknown（默认）
    - unsorted
    - queryname
    - coordinate：主键为`RNAME`字段，按照head中@SQ的顺序排序；次要排序依照`POS`进行，之后顺序任意
  - **GO**：排序分组，可能的值包括
    - none（默认）
    - query：依照`QNAME`进行分组
    - reference：依照`RNAME/POS`进行分组
  - **SS**：排序子序列，格式为sort-order:sorto-rder，简单理解为补充主排序，值可包含
    - unsorted
    - queryname
    - coordinate
- **@SQ**：参考序列，@SQ的顺序决定了排序顺序
  - **SN\***：参考序列名字，SN和AN（若存在）在所有@SQ中必须是唯一的。此字段用于对齐`RNAME`和`RTEXT`字段中的记录
  - **LN\***：参考序列长度，范围 $$ [1,2^{31}-1] $$
  - 




# 参考

[^1]: [https://samtools.github.io/hts-specs/SAMv1.pdf](https://samtools.github.io/hts-specs/SAMv1.pdf)
