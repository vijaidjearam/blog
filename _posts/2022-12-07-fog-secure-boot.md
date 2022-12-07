---
layout: post
date: 2022-12-07 16:46:41
title: Fog Secure Boot issue
category: Fog
tags: fog secureboot
---
# Fog Secure Boot issue


https://www.rodsbooks.com/efi-bootloaders/controlling-sb.html#creatingkeys

sudo apt-get update && apt-get upgrade

sudo apt install libfile-slurper-perl 
sudo apt-get install libssl-dev build-essential gnu-efi git make


Reference : https://www.geeksforgeeks.org/perl-slurp-module/

install as root

perl -MCPAN -e shell

install File::Slurp

Ref: https://stackoverflow.com/questions/1039107/how-can-i-check-if-a-perl-module-is-installed-on-my-system-from-the-command-line
Check if module is installed

alias modver="perl -e\"eval qq{use \\\$ARGV[0];\\\\\\\$v=\\\\\\\$\\\${ARGV[0]}::VERSION;};\ print\\\$@?qq{No module found\\n}:\\\$v?qq{Version \\\$v\\n}:qq{Found.\\n};\"\$1"mode

=> modver XML::Simple
No module found


List all the installed Perl modules

perl -MFile::Find=find -MFile::Spec::Functions -Tlw -e 'find { wanted => sub { print canonpath $_ if /\.pm\z/ }, no_chdir => 1 }, @INC'

git clone git://git.kernel.org/pub/scm/linux/kernel/git/jejb/efitools.git


https://community.spiceworks.com/topic/2343741-fog-self-signing-kernel-so-secure-boot-works?source=recommended
