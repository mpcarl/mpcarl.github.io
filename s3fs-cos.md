# Connect to IBM Bluemix Cloud ObjectStorage (S3 API) with s3fs-fuse
[s3fs-fuse] can be used to mount an IBM Bluemix Cloud ObjectStorage (COS) S3 API bucket as a file system on your local Linux or Mac system. Once mounted as a local file system, you can use other system tools to interact with your bucket. If you don't yet have a COS account, see [Ordering Storage][order]

## Installation
The [s3fs-fuse] GitHub page has all the details on building and installing the package. The build and install process I ran on a standard Ubuntu 14.04 Bluemix VM are as follows:

```
sudo apt-get update
sudo apt-get install automake autotools-dev g++ git libcurl4-openssl-dev libfuse-dev libssl-dev libxml2-dev make pkg-config
git clone https://github.com/s3fs-fuse/s3fs-fuse.git
cd s3fs-fuse
./autogen.sh
./configure
make
sudo make install
```
Note: The above command is slightly different from the instructions provided by [s3fs-fuse][s3fs-fuse]. Testing has shown that replacing `libcurl4-gnutls-dev` with `libcurl4-openssl-dev` shows improved performance.

## Configuration
In order to configure s3fs-fuse, you need your access key id, your secret access key, and the name of the bucket you want to mount. If you don't have this information, check out [these instructions][creds]. You will also need an existing bucket that you can mount. If you don't yet have a bucket, you can create one from the control portal. [These steps][buckets] will get you there.

1. Now that you have your credentials, you are ready to configure s3fs-fuse and mount your bucket.
From the command line of your Linux system, add your credentials to a configuration file and fix the permissions to limit access.

```
echo "Key ID:Secret Access Key" > $HOME/.cos_creds
chmod 600 $HOME/.cos_creds
```

2. Create a directory where you can mount your bucket. Typically, this is done in the /mnt directory on Linux.
```
sudo mkdir /mnt/mybucket
```

3. Mount the bucking using the command below. Be sure to use the [endpoint][endpoints] that corresponds to the location of your bucket. In this example, I will be using the US Cross Region service, private endpoint, and the bucket "mpc-bucket-a"

```
sudo s3fs mpc-bucket-a /mnt/mybucket  -o passwd_file=$HOME/.cos_creds -o sigv2 -o use_path_request_style -o url=https://s3-api.us-geo.objectstorage.service.networklayer.com
```

The bucket should now be mounted and available for use on the Linux system. If you have existing objects in your bucket, listing them on the Linux system will confirm you are connected.
```
mpcarl@dal12-swift-cos-migrator:~# cd /mnt/mybucket/
mpcarl@dal12-swift-cos-migrator:/mnt/mybucket# ls
consoleFull
```


Creating, editing, deleting files you see in this directory will be reflected in your COS bucket. Because the files are really objects in COS, some operations will take additional time for network transfers to complete.

If you have problems connecting or want the bucket to auto mount on reboots, the [s3fs-fuse] instructions have more details for you. The [s3fs-fuse Wiki][s3fs-wiki] page is another good source of information.


[s3fs-fuse]:  https://github.com/s3fs-fuse/s3fs-fuse
[sl]: https://control.softlayer.com
[slos]: https://control.softlayer.com/storage/objectstorage
[creds]: find_os_credentials
[order]: https://ibm-public-cos.github.io/crs-docs/ordering-storage
[buckets]: https://ibm-public-cos.github.io/crs-docs/storing-and-retrieving-objects
[s3fs-wiki]: https://github.com/s3fs-fuse/s3fs-fuse/wiki
[endpoints]: https://ibm-public-cos.github.io/crs-docs/endpoints
