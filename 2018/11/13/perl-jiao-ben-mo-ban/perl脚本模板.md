---
title: perl 脚本模板
categories: 
  - perl
tags:
  - 编程语言
  - perl
date: 2018-11-13
---

```perl
#!/usr/bin/perl -w
use strict;
use Getopt::Long;
use File::Basename;
use FindBin qw($Bin $Script);


my $USAGE = qq{

Fuction：
        test
Useage:
        perl $0 [Options]
Options:
        -h|help         Print this help
        -i| input       <str>  input input file
        -o|output       <str>  output output file
Other:
        
};


my ($help,$input,$output);

GetOptions(	# 冒号为设置参数类型
	"help:s"=>\$help,
	"input:s"=>\$input,
	"output:s"=>\$output
);
die "$USAGE\n" unless ($input && $output);

```