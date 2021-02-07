---
toc: true
title: " Sed Replace Text Sample"
description: "In this page, I will show you some examples about Linux sed command which replace matching text with your desire text."
categories: [Linux]
tags: [Sed]
redirect_from:
  - /2018/09/09/
---

> In this page, I will show you some examples about Linux sed command which replace matching text with your desire text.

### Assumption & Background

1. Your OS is CentOS 6.5

### Basic Sed Test

```md

# If you use the following command it will display all of the file contents including text replacement.
sed "s/my target text/my replaced text/" your_file.txt 

```

### Print Only Matching Case

```md

# In case when you want to see only the changes of your sed command, then use the following options.
sed -n "s/my target text/my replaced text/p" your_file.txt 
# -n option will disable automatic print. But it does not mean that it will print mathcing sed command lines.
# Notice that I typed p at the end of the command. This will show the matching sed line if there is any.
# Most of the time, I use these optinos to check the result before running. 
# Especially when I use regex.

```

### Use Regular Expression in Sed. (Sed with Regex)

```md

# When you want to use regular expression in sed command, then you need -r option.
# In this example, the case is where you need grouping and multiple matching (A Or B. Pipeline in other word)

# Sed with Regex Pipeline Example
sed -n -r "s/SEGMENT CREATE IMMEDIATE|SEGMENT CREATE DEFERRED/I DO NOT WANT SEGMENT SYNTAX/p" your_file.txt 

# Sed with Regex Grouping Example
sed -n -r "s/(SEGMENT) CREATE IMMEDIATE/\1 DROP IMMEDIATE/p" your_file.txt

```

### Replace contents of the file using Sed (Apply Changes to the files)

```md

# Finally, when you are sure about the apply the modification, use the following

sed -i'' -r -e "s/SEGMENT CREATE IMMEDIATE|SEGMENT CREATE DEFERRED/I DO NOT WANT SEGMENT SYNTAX/" your_file.txt 
# Make sure you remove 'p' character. 
# -i option is the one that replace the text.
# The reason I put '' right after i option is because, I found that sed makes another copy of the file with different file type.
# Ex) after sed -ir -e "s/a/b/" a.txt, it will produces extra file like a.txtr 
# It's because -i option expects a parameter that will be used for creating a back-up file name. 
# since I do not want to have extra file, I put '' right after that.
# And that's why I do not combine multiple options in a single '-' 

```

### Find file and Replace contents of each file using Find and Sed Command (Apply Changes to the files)

```md

# When you want to find more than one file and replace some common text, then use the following.
find . -type f -name my_*.log -exec sed -i'' -e "s/foo/bar/" {} \;
# This command will find any file that start with my_ and finish with .log and for each find found, it will apply sed command.

```

### Some Other Simple Options in sed (Ignore Case, Global)

```md

# Ignore Case Option can be used in sed. see below 
sed -i'' -e "s/foo/bar/i" your_file.txt

# Global Option. Which means multiple occurrence allowed in each line. 
# In otehr words, only first matching word will be replaced and ignore others for each line when 'g' is not appeard.
sed -i'' -e "s/foo/bar/g" your_file.txt

```

### For those who are interested in append text before/after. Click this [link](https://marindie.github.io/blog/2018/09/09/Sed-Append-Before-After-Sample-EN)

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io