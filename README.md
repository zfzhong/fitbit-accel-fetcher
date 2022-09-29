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

The above example is from one of the files we got using the code of this repository (slightly modified from [fitbit-accel-fetcher](https://github.com/gondwanasoft/fitbit-accel-fetcher) to check timestamp gaps and duplicates). 

The above example is from file "[17.csv](/blob/main/17.csv)", row 391 - 400. 

- Timestamp Gap
  
   From the data we identified a gap of 101ms (from 897761 to 897862). Where are the data for timestamp 897791, 897821, 897851?

- Timestamp Decreasing/Duplicates 

  Starting from timestamp 930679, 0x8000 gets added to the timestamp. If we do 930679 - 0x8000 = 897911, we found the timestamp of this entry is actually smaller than its previous timestamp 897952. 

  Sometimes, the decreased timestamps are just repetition of previous timestamps.

  We actually expect the timestamps increases. We are just confused by the timestamp decreasing or duplicating issues.

I will appreciate it very much if anyone has such experience and can share the fixes to these issues.

# fitbit-accel-fetcher
This is a Fitbit OS demo for transferring watch accelerometer data via companion to an external web server.

Features
-
Data is stored on the watch and transmitted to the companion in binary. This improves storage capacity and data transfer rate by about a factor of four.

Data is saved and transferred using multiple small files, to provide greater feedback during transfers and to allow faster error recovery.

Failed transfers are automatically retried.

The companion converts the binary data into plain text in CSV format, so it can be read in a text editor or imported into a spreadsheet.

The settings screen displays the companion's status.

By default, this app uses [android-fitbit-fetcher](https://github.com/gondwanasoft/android-fitbit-fetcher) as the server that receives the data. However, you could adapt it to use any other suitable server available to you.

The approach demonstrated in these repositories could be adapted to transfer other sensor data (such as heart rate).

More information is available in the [server's readme](https://github.com/gondwanasoft/android-fitbit-fetcher/blob/master/README.md).

Usage (assuming use of [android-fitbit-fetcher](https://github.com/gondwanasoft/android-fitbit-fetcher))
-
Build and install this repo to your watch and companion device (*eg*, phone).

Build and install the server on your companion device (*eg*, phone).

Start the server app.

Start the watch app ('Accel Fetcher').

Record some accelerometer data on your watch.

Transfer accelerometer data from watch to phone.

Await transfer to finish (see watch app).

Select `GET DATA` on server app, and select a file into which the data will be copied.

Use a file manager on your phone to verify that the data has been received.

Caveats
-

This app is not intended to be used as is. Its purpose is to demonstrate some techniques that could be applied in other applications.

This has not been tested using any server other than [android-fitbit-fetcher](https://github.com/gondwanasoft/android-fitbit-fetcher).

No support is provided.