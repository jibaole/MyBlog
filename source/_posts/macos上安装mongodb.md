---
title: macos上安装mongodb
tags:
  - mongodb
categories:
  - Database
toc: true
date: 2015-11-21 11:36:20
description: 转自：https://docs.mongodb.com/master/tutorial/install-mongodb-on-os-x/?_ga=1.128744908.1590020553.1472522903
feature:
---

#### Overview

Use this tutorial to install MongoDB Community Edition on OS X systems.

>PLATFORM SUPPORT
Starting in version 3.0, MongoDB only supports OS X versions 10.7 (Lion) and later on Intel x86-64.

MongoDB Community Edition is available through the popular OS X package manager Homebrew or through the MongoDB Download site.

#### Install MongoDB Community Edition

>NOTE
To install a version of MongoDB prior to 3.2, please refer to that version’s documentation. For example, see version 3.0.

You can install MongoDB Community Edition with Homebrew or manually. This section describes both methods.
<!-- more -->

#### Install MongoDB Community Edition with Homebrew

Homebrew installs binary packages based on published “formulae.” This section describes how to update brew to the latest packages and install MongoDB Community Edition. Homebrew requires some initial setup and configuration, which is beyond the scope of this document.

1. Update Homebrew’s package database.
In a system shell, issue the following command:
``` bash
brew update
```
2. Install MongoDB.
You can install MongoDB via brew with several different options. Use one of the following operations:

* Install the MongoDB Binaries
To install the MongoDB binaries, issue the following command in a system shell:
``` bash
brew install mongodb
```
* Install the MongoDB Binaries with TLS/SSL Support
To install the MongoDB binaries that have TLS/SSL support, issue the following from a system shell:
``` bash
brew install mongodb --with-openssl
```
* Install the Latest Development Release of MongoDB
To install the latest development release for use in testing and development, issue the following command in a system shell:
``` bash
brew install mongodb --devel
```
#### Install MongoDB Community Edition Manually

Only install MongoDB Community Edition using this procedure if you cannot use homebrew.

1. Download the binary files for the desired release of MongoDB.
Download the binaries from https://www.mongodb.org/downloads.
For example, to download the latest release through the shell, issue the following:
``` bash
curl -O https://fastdl.mongodb.org/osx/mongodb-osx-x86_64-3.4.0-rc3.tgz
```
2. Extract the files from the downloaded archive.
For example, from a system shell, you can extract through the tar command:
``` bash
tar -zxvf mongodb-osx-x86_64-3.4.0-rc3.tgz
```
3. Copy the extracted archive to the target directory.
Copy the extracted folder to the location from which MongoDB will run.
``` bash
mkdir -p mongodb
cp -R -n mongodb-osx-x86_64-3.4.0-rc3/ mongodb
```
4. Ensure the location of the binaries is in the PATH variable.
The MongoDB binaries are in the bin/ directory of the archive. To ensure that the binaries are in your PATH, you can modify your PATH.

For example, you can add the following line to your shell’s rc file (e.g. ~/.bashrc):
``` bash
export PATH=<mongodb-install-directory>/bin:$PATH
```
Replace <mongodb-install-directory> with the path to the extracted MongoDB archive.

#### Run MongoDB

1. Create the data directory.
Before you start MongoDB for the first time, create the directory to which the mongod process will write data. By default, the mongod process uses the /data/db directory. If you create a directory other than this one, you must specify that directory in the dbpath option when starting the mongod process later in this procedure.
The following example command creates the default /data/db directory:
``` bash
mkdir -p /data/db
```
2. Set permissions for the data directory.
Before running mongod for the first time, ensure that the user account running mongod has read and write permissions for the directory.

3. Run MongoDB.
To run MongoDB, run the mongod process at the system prompt. If necessary, specify the path of the mongod or the data directory. See the following examples.
  - Run without specifying paths
  If your system PATH variable includes the location of the mongod binary and if you use the default data directory (i.e., /data/db), simply enter mongod at the system prompt:
  ``` bash
  mongod
  ```
  - Specify the path of the mongod
  If your PATH does not include the location of the mongod binary, enter the full path to the mongod binary at the system prompt:
  ```
  <path to binary>/mongod
  ```
  - Specify the path of the data directory
  If you do not use the default data directory (i.e., /data/db), specify the path to the data directory using the --dbpath option:
  ``` bash
  mongod --dbpath <path to data directory>
  ```

4. Begin using MongoDB.
To help you start using MongoDB, MongoDB provides Getting Started Guides in various driver editions. See Getting Started for the available editions.
Before deploying MongoDB in a production environment, consider the Production Notes document.
Later, to stop MongoDB, press Control+C in the terminal where the mongod instance is running.

