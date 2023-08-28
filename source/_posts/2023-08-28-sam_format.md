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
  - **LN\***：参考序列长度，范围 $ [1,2^{31}-1] $
  - **AH**：表示该序列是一个备用位点，值为该序列在主序列中所替代的位点，格式如下
    - chr:start-end
    - chr：如果已知
    - *：如果未知
  - **AN**：备用参考序列名称，不得出现在`RNAME`与`RTEXT`字段中
  - **AS**：基因组组装标识符
  - **DS**：描述，必须为UTF8编码
  - **M5**：序列的MD5码
  - **SP**：物种
  - **TP**：分子拓扑结构，可能的值包括
    - linear（默认）
    - circular
  - **UR**：序列的统一资源标识符，为http或ftp链接
- **@RG**：read组，允许无序的多行@RG
  - **ID\***：read的ID，是唯一的，且可在SAM文件合并时进行修改
  - **BC**：样品或库的认证码
  - **CN**：测序中心的名字
  - **DS**：描述，UTF8编码
  - **DT**：检测日期
  - **FO**：流程顺序
  - **KS**：与每个读数的关键序列相对应的核苷酸碱基阵列
  - **LB**：库
  - **PG**：用于处理read group的软件
  - **PI**：预测的插入尺寸中值
  - **PL**：产生此read的技术，可能的值包括
    - CAPILLARY
    - DNBSEQ
    - ELEMENT
    - HELICOS
    - ILLUMINA
    - IONTORRENT
    - LS454
    - ONT
    - PACBIO
    - SOLID
    - ULTIMA
    - unknown（若不在上述技术中）

  - **PM**：Platform model
  - **PU**：Platform unit
  - **SM**：Sample

- **@PG**：程序相关
  - **ID\***：程序的代号，每个程序唯一，用于对其@PG中的`PG`和`PP`字段
  - **PN**：程序名称
  - **CL**：具体的指令，UTF8编码
  - **PP**：上一个@PG条目的`ID`字段
  - **DS**：描述，UTF8编码
  - **VN**：所使用软件的版本

- **@CO**：其它注释信息，允许有多行



## 二、正文






# 参考

[^1]: [https://samtools.github.io/hts-specs/SAMv1.pdf](https://samtools.github.io/hts-specs/SAMv1.pdf)
