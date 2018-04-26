---
layout: post
title:  "XCTest import in own testing framework"
date:   2018-04-20 11:41:01 +0200
categories: jekyll update
---
### Problem
If you would like to create framework, which will be used only in unit tests. At one moment you will need to import `XCTest`.
But Xcode will give you awesome error:
```
"Cannot load underlying module for XCTest"
```
### Reason
As I understand, Xcode doesn't know that your framework will be imported only in test target, so it can't find `XCTest` framework
in "normal" framework location.
### Solution
Add to your config file:
```
OTHER_LDFLAGS = -weak_framework XCTest -weak-lswiftXCTest
FRAMEWORK_SEARCH_PATHS = $(DEVELOPER_FRAMEWORKS_DIR) $(PLATFORM_DIR)/Developer/Library/Frameworks
ENABLE_BITCODE = NO // XCTest doesn't support bitcode, why would it?
```
It will provide location for `XCTest` framework and link it to your project.
