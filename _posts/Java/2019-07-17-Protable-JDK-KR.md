---
toc: true
title: " Portable JDK 만들기"
description: "JAVA Portable JDK 만들기"
categories: [Java]
tags: [Java]
redirect_from:
  - /2019/07/17/
---

> Portable JDK 만들기

### Recipe for JDK 8 (update 93 and newer) {#toc1}

```md
Prerequisite: Install 7-Zip file archiver (or its portable alternative).
Download the Java 8 SDK for Windows from Oracle. You will get a file named like jdk-8u101-windows-x64.exe.
Open the downloaded file with 7-Zip, which will show a a list of files and folders.
Open folder ‘.rsrc’ (select and double click this folder within 7-Zip).
Open folder ‘1033’ (or the folder of greatest size).
Open folder ‘JAVA_CAB10’ (or the folder of greatest size).
Open file ‘111’ (or the folder of greatest size).
Open file ‘tools.zip’.

Extract its contents to a directory where own write permissions, for example: C:\Users\Daniel\Documents\jdk-8u102-x64. To prevent issues, I recommend a directory without spaces.

From within this directory, search for all .pack files and extract them using unpack2000.exe command line tool found in the bin subdirectory. 

Following windows prompt command does the trick when executed from within the extracted directory: 
for /r %i in (*.pack) do .\bin\unpack200.exe %i %~pi%~ni.jar
The downloaded .exe file also contains the JDK sources. Navigating through the folders ‘.rsrc’, ‘1033’, ‘JAVA_CAB9’, ‘110’.
```

### Recipe for JDK 8 (update 92 and older) {#toc2}

```md
Prerequisite: Install 7-Zip file archiver (or its portable alternative).
Download the Java 8 SDK for Windows from Oracle. You will get a file named like jdk-8u74-windows-x64.exe.
Open the downloaded file with 7-Zip, which will show a single archived file named tools.zip.
Open the tools.zip from within 7-Zip.

Extract its contents to a directory where own write permissions, for example: C:\Users\Daniel\Documents\java-8-sdk
From within this directory, search for all .pack files and extract them using unpack2000.exe command line tool found in the bin subdirectory. Following windows 

prompt command does the trick when executed from within the extracted directory: 
for /r %i in (*.pack) do .\bin\unpack200.exe %i %~pi%~ni.jar
```

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io
