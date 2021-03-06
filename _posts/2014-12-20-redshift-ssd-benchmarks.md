---
layout: post
title: "Redshift SSD Benchmarks"
description: "Benchmarking Redshift performance for different nodes"
category: Redshift, Data Science, Data Warehousing
tags: [Coursera, Analytics]
---
{% include JB/setup %}

Our warehouse runs completely on Redshift, and query performance is extremely important to us. Earlier this year, the AWS team announced the release of SSD instances for Amazon Redshift. Is the extra CPU truly worth it? We do a lot of processing with Redshift, so this question is big for us. To answer this, we decided to benchmark SSD performance and compare it to our original HDD performance.

Redshift is easy to use because its PostgreSQL JDBC drivers allow us to use a range of familiar SQL clients. Its speedy performance is achieved through columnar storage and data compression.


## Experiment Setup
The Redshift instance specs are based off on-demand pricing, but the reserved instances can be 75% more affordable. The results from the benchmark are the mean run times after running each query 3 times.

<table class="table table-bordered table-striped table-hover">
  <colgroup>
    <col span="1" style="width: 20%;" />
    <col span="1" style="width: 20%;" />
    <col span="1" style="width: 20%;" />
    <col span="1" style="width: 20%;" />
    <col span="1" style="width: 20%;" />
  </colgroup>
  <thead>
    <tr>
      <td> </td>
      <td><b>HDD Setup 1</b></td>
      <td><b>HDD Setup 2</b></td>
      <td><b>SSD Setup 1</b></td>
      <td><b>SSD Setup 2</b></td>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><b>Nodes</b></td>
      <td>4 dw1.xlarge</td>
      <td>8 dw1.xlarge</td>
      <td>32 dw2.large</td>
      <td>4 dw2.8xlarge</td>
    </tr>
    <tr>
      <td><b>Storage</b></td>
      <td>8 TB</td>
      <td>16 TB</td>
      <td>5.12 TB</td>
      <td>10.24 TB</td>
    </tr>
    <tr>
      <td><b>Memory</b></td>
      <td>60 GB</td>
      <td>120 GB</td>
      <td>480 GB</td>
      <td>976 GB</td>
    </tr>
    <tr>
      <td><b>vCPU</b></td>
      <td>8</td>
      <td>16</td>
      <td>64</td>
      <td>128</td>
    </tr>
    <tr>
      <td><b>Price</b></td>
      <td>$3.4 / hr</td>
      <td>$6.8 / hr</td>
      <td>$8 / hr</td>
      <td>$19.2 / hr</td>
    </tr>
  </tbody>
</table>

### Query 1.
First, we ran a simple join query between a table with 1 billion rows and a table with 50 million rows. The total amount of data processed was around 46GB. The results fell in favour of SSD’s.

<img src="https://dnsta5v53r71w.cloudfront.net/images/redshift-ssd-benchmark/1a.png" alt="Screenshot" style="width: 80%; margin-left:10%; margin-right:10%; margin-top:20px; margin-bottom:20px;"/>


### Query 2.
This complex query features REGEX matching and aggregate functions across 1 million rows from 4 joins. The total amount of data processed was around 100GB. The results fell even more in favour of SSD’s from 5x - 15x the performance improvement.

<img src="https://dnsta5v53r71w.cloudfront.net/images/redshift-ssd-benchmark/2.png" alt="Screenshot" style="width: 80%; margin-left:10%; margin-right:10%; margin-top:20px; margin-bottom:20px;"/>


### Query 3.
A query that runs window functions on a table of 1 billion rows showed surprising results. The total amount of data in this table is about 400GB. Although the SSD’s performed better, the smaller SSD’s out-performed the bigger SSD’s despite having double the memory and CPU power.

<img src="https://dnsta5v53r71w.cloudfront.net/images/redshift-ssd-benchmark/3.png" alt="Screenshot" style="width: 80%; margin-left:10%; margin-right:10%; margin-top:20px; margin-bottom:20px;"/>


### Query 4.
This last query has 4 join statements with a subquery that also includes 2 joins. The amount of data processed is around 107GB. Since this query is very compute-heavy, it is not surprising that SSD’s perform 10x better. What is shocking is that the smaller SSD’s are once again more performant than the bigger SSD’s.

<img src="https://dnsta5v53r71w.cloudfront.net/images/redshift-ssd-benchmark/4a.png" alt="Screenshot" style="width: 80%; margin-left:10%; margin-right:10%; margin-top:20px; margin-bottom:20px;"/>

## Conclusion
We also ran some other queries and the performance improvement from HDD to SSD was consistent at about 5 - 10 times. From these experiments, the DW2 machines are clearly promising in terms of computation time. For the same price, SSD’s provide 3.4 times more CPU power and memory. However, the disk storage is about 25% of that of the HDD’s.

A limitation to the dw2.large SSD instances is that a Redshift cluster can support at most 32 of them. That means dw2.large’s can provide at most 5.12 TB of disk storage. The only other option is to upgrade to dw2.8xlarge’s but this experiment shows little performance benefits from dw2.large’s to dw2.8xlarge’s despite doubling the memory and CPU.

<i><small>PS: This was originally written by Jason Shao on the [Coursera blog](https://tech.coursera.org/blog/2014/12/19/redshift-benchmark/).</small></i>
