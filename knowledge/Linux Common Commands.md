---
tags:
  - os
  - linux
  - cli
summary: file ops, view/edit, search (grep/find), text processing (awk/sed), process, network, pipe
use_cases:
  - cli
  - log
  - scripting
---

# Linux Common Commands

## File and Directory  

```bash
ls # list files
ls -l # detailed list
ls -a # include hidden files

cd dir # change directory
cd .. # parent directory
cd ~ # home directory

pwd # print working directory

mkdir dir # create directory
rm -r dir # remove directory
rm file # remove file

cp a b # copy
mv a b # move / rename

touch file # create empty file
```

## File Viewing  and Editing

```bash
cat file # show full content
less file # paged view
head -n 10 file # first 10 lines
tail -n 10 file # last 10 lines
tail -f log # real-time log

vim file # edit file (advanced editor)
nano file # edit file (simple editor)
```

## Search and Filter

```bash
grep "text" file # search text
grep -r "text" . # recursive search
grep -v "text" file # inverse match
grep -i "text" file # ignore case
grep -n "text" file # show line number
grep -c "text" file # count matches
grep -E "a|b" file # multiple patterns

find . -name "*.txt" # find file

which python # command path
whereis python # locate binary/source/man
```

## Text Processing

```bash
awk '{print $1}' file # print first column
awk '{print $1, $3}' file # print multiple columns
awk '$3 > 100' file # filter by column value
awk '{sum += $1} END {print sum}' file # sum column
awk '{sum += $1} END {print sum/NR}' file # average
awk 'NR % 2 == 0' file # select even lines
awk -F ',' '{print $1}' file.csv # set delimiter
awk 'max < $1 {max = $1} END {print max}' file # max value
awk '{cnt[$1]++} END {for (k in cnt) print k, cnt[k]}' file # frequency count

sed 's/a/b/g' file # replace text
sed 's/a/b/' file # replace first match per line
sed -i 's/a/b/g' file # replace in place
sed '2d' file # delete line 2
sed '1,3d' file # delete lines 1-3
sed '/^$/d' file # delete empty lines
sed -n '1,10p' file # print lines 1-10
sed '3s/a/b/' file # replace only line 3
sed 's/^ *//' file # remove leading spaces
sed 's/ *$//' file # remove trailing spaces
```

## Permissions

```bash
chmod 755 file # change permission
chown user:group file # change owner

sudo command # run as admin
```

## Process Management

```bash
ps aux # list processes
top # real-time monitor
kill pid # kill process
kill -9 pid # force kill
```

## Network

```bash
ping google.com # test connectivity
curl url # http request
wget url # download file

netstat -tuln # list listening ports (legacy)
ss -lnt # list listening ports (modern)
```

## Compression

```bash
tar -cvf a.tar dir # archive
tar -xvf a.tar # extract
tar -czvf a.tar.gz dir # compress
tar -xzvf a.tar.gz # decompress
```

## Pipe and Redirection

```bash
command1 | command2 # pipe
> file # overwrite output
>> file # append output
```

example:
```bash
ps aux | grep python
```