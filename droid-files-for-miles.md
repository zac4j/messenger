---
title: Files: Where to Store Them All?
tags: 
- droid
- file
categories: 
- [file]
- [droid]
---

#### Interal storage
Safe and secure
+ Context.getFilesDir()
+ Context.getCacheDir()
	- Not counted against your app, managed automatically by OS
+ Context.getNoBackupFilesDir()
	- Left behind during migration between devices
+ Context.getCodeCacheDir()
	- Optimized code, cleared by OS during upgrades.


#### External storage
Shared and upprotected
+ Context.getExternalFilesDirs()
+ Context.getExternalCacheDirs()
	- Not counted against your app, managed automatically by OS
+ Context.getExternalMediaDirs()
	- Scanned and included in MediaStore
+ Context.getObbDirs()
	- Counted towards your app's code size

#### Asking the user
Flexible and powerful
Intent.ACTION_OPEN_DOCUMENT and ACTION_CREATE_DOCUMENT give users control over where their content is stored

#### Cached data
+ Data you can re-generate or re-download
	- Two-way street:cached files aren't counted against your app,but the OS can delete them when other apps need space.
+ OS balances cached data between all apps
	- Each app has a "cache quote" which is adjusted over time
	- Apps most over their quote are the first in line to have cached files removed when space is needed
