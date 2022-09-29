# Code Modification: Checking Timestamp Gaps and Duplicates
The code gets slightly modified to check the timestamp gaps and duplicates from Fitbit Accelerometer batch reading.

# The Problem
Given frequency 30 and batch 30, we will get 30 entries of data from the batch reading. Ideally, the timestamps of
the 30 entries of data should be evenly separated within a second, e.g., every entry should be around 33ms behind
its previous entry. 

Specifically, we expect the data to be like:

| Timestamp | x | y | z |
| :-------: | :-: | :-: | :-: |
| 1 | -0.37 | -0.22 | 9.72 |
| 34 | -0.38 | -0.42 | 9.52 |
| 67 | -0.25 | -0.31 | 9.61 |
| 100 | -0.39 | -0.21 | 9.71 |

In the above table, the timestamps are evenly separated. The time interval between two consecutive entries of data is 33ms.

However, in practice we found timestamp gaps from the batch reading data. The data we actually got, sometimes, was like the following:
| Timestamp | x | y | z |
| :-------: | :-: | :-: | :-: |
| 897701 | 0.01 | -0.136 | 9.734 |
| 897731 | 0.006 | -0.134 | 9.744 |
| 897761 | -0.008 | -0.108 | 9.73 |
| 897862 | 0.026 | -0.13 | 9.738 |
| 897892 | 0 | -0.122 | 9.718 |
| 897922 | 0.032 | -0.122 | 9.744 |
| 897952 | 0.02 | -0.136 | 9.732 |
| 930679 | 0.018 | -0.15 | 9.748 |
| 930709 | 0.026 | -0.132 | 9.728 |
| 930739 | 0.014 | -0.118 | 9.67 |

The above example is from one of the files we got by running the code of this repository (slightly modified from [fitbit-accel-fetcher](https://github.com/gondwanasoft/fitbit-accel-fetcher)) on Fitbit Sense to check timestamp gaps and duplicates. 

The above example is from file "[17.csv](/17.csv)", row 391 - 400. 

- Timestamp Gap
  
   From the data we identified a gap of 101ms (from 897761 to 897862). Where are the data for timestamp 897791, 897821, 897851?

- Timestamp Decreasing/Duplicates 

  Starting from timestamp 930679, 0x8000 gets added to the timestamp. If we do 930679 - 0x8000 = 897911, we found the timestamp of this entry is actually smaller than its previous timestamp 897952. 

  Sometimes, the decreased timestamps are just repetition of previous timestamps.

  We actually expect the timestamps increases. We are just confused by the timestamp decreasing or duplicating issues.

  I have many other files which have gaps and duplicates, I am willing to provide them if anyone is interested. If you run the code from this repository with your Fitbit device, you will get data with gaps and duplicates as well.

# How Did I Discover the Problem
I actually built my stand-alone Fitbit App to record the accelerometer data with frequency 50 and batch 50. I found the gaps and duplicates in my App. I am not quite confident in my code because I am quite new to Fitbit App developement.

I found [fitbit-accel-fetcher](https://github.com/gondwanasoft/fitbit-accel-fetcher) from the [Fitbit github site: github.com/Fitbit/ossapps](https://github.com/Fitbit/ossapps). Unfortunately, after a bit checking, it seems [fitbit-accel-fetcher](https://github.com/gondwanasoft/fitbit-accel-fetcher) has the timestamp issues as well.

# Looking for Fixes
I will appreciate it very much if anyone has such experience is willing to share his/her fixes to these issues.
