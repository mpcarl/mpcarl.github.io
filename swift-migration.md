---
title: Migration from IBM Cloud ObjectStorage Swift API to S3 API
keywords:
last_updated: May 5, 2017
tags:
summary: Current customers of Softlayer Object Storage based on OpenStack Swift can see benefits moving to IBM S3
# TODO - What benefits?
sidebar: crs_sidebar
permalink: swift-migration
folder: swift
toc: True
---


## Swift DC deployments / Cross region / availability zone
The standard IBM Bluemix Swift deployment stores all (or most) systems that back the API in the same data center (DC). From a performance point of view, this is extremely fast because all components are, network wise, very close to each other. From a problem point of view, the API can become unavailable if the particular data center is unavailable.

The new S3 based API is being deployed in two different flavors. The cross-region and regional.

* The cross-region configuration uses DCs in three cities across a geography (e.g. Washington, Dallas, and San Jose). The API and all buckets/objects are available as long as two of the three cities are available. Thus, a single DC or regional outage does not affect the API or availability of data. The API can be reached using a generic URL or a region specific URL. In either case, the same buckets and objects are available. The improved availability of the cross-region offering does come with a price in terms of cost and performance.

* The regional configuration uses systems in the same city (e.g. Dallas). However, even in this case, unlike Swift, the systems are spread across multiple DCs in the same city. Thus a failure at one DC in a region still allows for the data and API to remain available.

The currently available Regional and Cross-Region deployments can be found int the [endpoints][endpoints] documentation.

## Swift vs S3 pricing
The Swift API provides a single pricing model for customers. You pay for your storage capacity and your outbound data usage.
The S3 API provides more options that allow you to choose the pricing model that is optimal for your workload.

The [IBM cloud object storage pricing][pricing] page has full details.

## Features

### S3 compatible API
IBM Cloud ObjectStorage S3 provides an Amazon S3 [compatible API][1].

Openstack Swift provides a [Swift/APIFeatureComparison][2] document to help understand the high-level similarities and differences between the APIs.

## Swift migration considerations
Migration from Swift to S3 environments.

## Amazon S3 migration considerations

### Use cases
#### Archive workloads

#### Static web hosting / CDN (future)


### API differences
Enabling public access to objects and containers/buckets

| Function | Swift    | S3      |
| -------- | -------- | -------- |
| Publicly readable object | Oranges | Pears |


### Data migration
Tools of interest

#### Swift virtual file system (svfs)
The [Swift virtual file system][3] can be used to mount a swift container as a file system on Linux and MacOS systems. Once mounted, the objects in the container can be interacted with like standard files on the system.

#### s3fs-fuse
Similarly, the [s3fs-fuse][s3fs-fuse] utility can be use to mount an S3 bucket as a files system on Linux or MacOS systems. The [Connect to IBM Bluemix Cloud ObjectStorage (S3 API) with s3fs][s3fs-cos] page gets you up and running pretty quickly.

#### Swift command line Tool




[1]: https://ibm-public-cos.github.io/crs-docs/api-reference
[2]: https://wiki.openstack.org/wiki/Swift/APIFeatureComparison
[3]: https://github.com/ovh/svfs
[s3fs-fuse]: https://github.com/s3fs-fuse/s3fs-fuse
[endpoints]: https://ibm-public-cos.github.io/crs-docs/endpoints
[pricing]: https://www.ibm.com/cloud-computing/bluemix/pricing-object-storage
[s3fs-cos]: s3fs-cos
