# Connect to IBM Bluemix Cloud ObjectStorage with rclone
[Rclone][rclone] provides a command line interface (cli) that you can use to quickyl and easily move data between a large number of different storage systems. One of those is [IBM Bluemix Cloud ObjectStorage][cos](COS) using both the S3 API and the Swift API. Below are instructions on how to configure for each system and sync files from Swift to COS.

## Installation
Installation of Rclone is a simple matter of downloading the latest zip and getting in your $PATH. The [Rclone install][install] page has all the details.

## Configure rclone to access IBM COS S3 API
Rclone provides a configuration wizard which can be run using the command:

`rclone config`

Before running the wizard you need your access key id, your secret access key, and the name of the bucket you want to mount. If you don't have this information, check out [these instructions][creds]. You will also need to know which COS [endpoint][endpoints] you will be using. While the wizard can be useful, an easier way is copy paste. The configuration is stored in the $HOME/.rclone.conf file. Use for favorite text editor to add the following entries being sure to substitute your access key id and secret access key:

```
[COS-US-SOUTH]
type = s3
env_auth = false
access_key_id = <your access key>
secret_access_key = <your secret key>
region = other-v4-signature
endpoint = s3.us-south.objectstorage.softlayer.net
location_constraint =
acl = private
server_side_encryption =
storage_class =
```
The first line above [COS_US_SOUTH] is how you will reference this configuration from the rclone command line (without the []).
## Using rclone with COS
The [Rclone documentation][rc-docs] provide all of the details you need. Here are a few simple examples.

* Listing the buckets associated with the account (don't miss the colon at the end)
```
mpcarl@dal12-swift-cos-migrator:~# rclone lsd COS-US-SOUTH:
          -1 2017-06-16 18:27:45        -1 mpc-bucket-a
          -1 2017-06-08 16:46:33        -1 mpc-rclone-target-a
          -1 2017-06-08 17:11:19        -1 mpc-rclone-target-b
          -1 2017-06-08 17:21:15        -1 mpc-rclone-target-c
          -1 2017-06-08 18:02:56        -1 mpc-rclone-target-reg-a
          -1 2017-06-08 19:10:44        -1 mpc-rclone-target-reg-b
          -1 2017-06-08 19:51:40        -1 mpc-rclone-target-reg-c
2017/06/16 19:31:48
Transferred:      0 Bytes (0 Bytes/s)
Errors:                 0
Checks:                 0
Transferred:            0
Elapsed time:       100ms
```
*** Note: The buckets listed are all of the account buckets. Not all buckets may be located at the endpoint you configured.

* Listing the contents of a bucket
```
@dal12-swift-cos-migrator:~# rclone ls COS-US-SOUTH:mpc-rclone-target-reg-c
        0 prime/p1003
        0 prime/p1004
        0 prime/p1005
        0 prime/p1006
        0 prime/p100
        0 prime/p1008
.....
```
If you attempt to list a bucket that is not at the endpoint configured, you will get a directory not found error message.
```
mpcarl@dal12-swift-cos-migrator:~# rclone ls COS-US-SOUTH:mpc-rclone-target-a
2017/06/16 19:39:05 directory not found
```
* Copying a local file (goodstufftxt) to COS
```
mpcarl@dal12-swift-cos-migrator:~# rclone copy goodstuff.txt COS-US-SOUTH:mpc-rclone-target-reg-c
2017/06/16 19:43:33 S3 bucket mpc-rclone-target-reg-c: Waiting for checks to finish
2017/06/16 19:43:33 S3 bucket mpc-rclone-target-reg-c: Waiting for transfers to finish
2017/06/16 19:43:33
Transferred:     19 Bytes (183 Bytes/s)
Errors:                 0
Checks:                 1
Transferred:            1
Elapsed time:       100ms
```

* make sure it is there
```
mpcarl@dal12-swift-cos-migrator:~# rclone ls COS-US-SOUTH:mpc-rclone-target-reg-c | grep good
       19 goodstuff.txt
2017/06/16 19:44:45
Transferred:      0 Bytes (0 Bytes/s)
Errors:                 0
Checks:                 0
Transferred:            0
Elapsed time:          2s
```

* delete it from the bucket and check it is gone
```
mpcarl@dal12-swift-cos-migrator:~# rclone delete COS-US-SOUTH:mpc-rclone-target-reg-c/goodstuff.txt
2017/06/16 19:46:48 Waiting for deletions to finish
2017/06/16 19:46:48
Transferred:      0 Bytes (0 Bytes/s)
Errors:                 0
Checks:                 1
Transferred:            0
Elapsed time:       100ms
mpcarl@dal12-swift-cos-migrator:~# rclone ls COS-US-SOUTH:mpc-rclone-target-reg-c | grep good
2017/06/16 19:46:55
Transferred:      0 Bytes (0 Bytes/s)
Errors:                 0
Checks:                 0
Transferred:            0
Elapsed time:        2.2s
```

## Configure rclone to access IBM COS S3 API
In order to configure rclone to access your Swift buckets, you will need your Username and API Key. If you don't have this information, check out [these instructions][creds].

## Using rclone with Swift



[rclone]: https://rclone.org/
[cos]: https://www.ibm.com/cloud-computing/products/storage/object-storage/
[dl]: https://rclone.org/downloads/
[install]: https://rclone.org/install/
[endpoints]: https://ibm-public-cos.github.io/crs-docs/endpoints
[rc-docs]: https://rclone.org/docs/
