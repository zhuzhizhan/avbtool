# Win Avbtool

发布版本：1.0

作者邮箱：jason.zhu@rock-chips.com

日期：2018.12

文件密级：公开资料

------

**前言**

**概述**

介绍Windows环境下avbtool适配使用。

**读者对象**

本文档（本指南）主要适用于以下工程师：

技术支持工程师

软件开发工程师

**产品版本**

**修订记录**

| **日期**   | **版本** | **作者**  | **修改说明** |
| ---------- | -------- | --------- | ------------ |
| 2018-12-27 | V1.0     | Jason Zhu | 初始版本     |

------

[TOC]

------

## openssl 安装

从http://slproweb.com/products/Win32OpenSSL.html下载openssl的安装包Win64OpenSSL_Light-1_1_1a.exe，安装软件。设置环境变量，如下：

![openssl](image\openssl.png)

## avbtool修改

1:

```
@@ -3915,7 +3915,7 @@ class AvbTool(object):
                                        help='Add hashes and footer to image.')
     sub_parser.add_argument('--image',
                             help='Image to add hashes to',
-                            type=argparse.FileType('rab+'))
+                            type=argparse.FileType('ab+'))
     sub_parser.add_argument('--partition_size',
                             help='Partition size',
                             type=parse_number)
@@ -3948,7 +3948,7 @@ class AvbTool(object):
                                        help='Append vbmeta image to image.')
     sub_parser.add_argument('--image',
                             help='Image to append vbmeta blob to',
-                            type=argparse.FileType('rab+'))
+                            type=argparse.FileType('ab+'))
     sub_parser.add_argument('--partition_size',
                             help='Partition size',
                             type=parse_number,
@@ -3962,7 +3962,7 @@ class AvbTool(object):
                                        help='Add hashtree and footer to image.')
     sub_parser.add_argument('--image',
                             help='Image to add hashtree to',
-                            type=argparse.FileType('rab+'))
+                            type=argparse.FileType('ab+'))
     sub_parser.add_argument('--partition_size',
                             help='Partition size',
                             default=0,
```

2:

```
@@ -3080,7 +3080,7 @@ class Avb(object):
           # size as the hash size. Don't populate a random salt if this
           # descriptor is being created to use a persistent digest on device.
           hash_size = digest_size
-          salt = open('/dev/urandom').read(hash_size)
+          salt = open('urandom').read(hash_size)
         else:
           salt = ''

@@ -3310,7 +3310,7 @@ class Avb(object):
           # size as the hash size. Don't populate a random salt if this
           # descriptor is being created to use a persistent digest on device.
           hash_size = digest_size
-          salt = open('/dev/urandom').read(hash_size)
+          salt = open('urandom').read(hash_size)
         else:
           salt = ''

@@ -3926,7 +3926,7 @@ class AvbTool(object):
                             help='Hash algorithm to use (default: sha256)',
                             default='sha256')
     sub_parser.add_argument('--salt',
-                            help='Salt in hex (default: /dev/urandom)')
+                            help='Salt in hex (default: urandom)')
     sub_parser.add_argument('--calc_max_image_size',
                             help=('Don\'t store the footer - '
                                   'instead calculate the maximum image size '
@@ -3974,7 +3974,7 @@ class AvbTool(object):
                             help='Hash algorithm to use (default: sha1)',
                             default='sha1')
     sub_parser.add_argument('--salt',
-                            help='Salt in hex (default: /dev/urandom)')
+                            help='Salt in hex (default: urandom)')
     sub_parser.add_argument('--block_size',
                             help='Block size (default: 4096)',
                             type=parse_number,

```

注意urandom为随机数文件，需要每次随机生成一串随机数。

## Avbtool使用示例

```
python avbtool add_hash_footer --image boot.img --partition_size 33554432 --partition_name boot --key testkey_atx_psk.pem --algorithm SHA256_RSA4096
python avbtool make_vbmeta_image --include_descriptors_from_image boot.img --algorithm SHA256_RSA4096 --key testkey_atx_psk.pem --output vbmeta.img
```

**注意：windows环境下必须以管理员运行程序。**