---
title: "config doxygen"
description: "config doxygen"
date: 2026-03-11T20:38:18+08:00
---
template -> basic -> project -> 
generate documentation
```
doxygen mydoxy
```

## config - template
```
doxygen -g mydoxy
```
default name: Doxyfile
### update
If you are using a newer version of Doxygen, you need to execute:
```
doxygen -u mydoxy
```

## config - basic
- AUTO BRIEF
```
JAVADOC_AUTOBRIEF      = YES
```
- BANNER
```
JAVADOC_BANNER         = YES
```

#### PYTHON - Off
```
PYTHON_DOCSTRING       = NO
```
#### Inherit - Off
```
INHERIT_DOCS           = NO
```

## config - project
- output
```
OUTPUT_DIRECTORY       = C:/Users/hp262/Documents
```
### project src
```
INPUT                  = C:/Users/hp262/CLionProjects/untitled
```
#### Searched subdirectories
```
RECURSIVE              = YES
```
