### Linux
##### tar progress  

```
tar cf - $1 | pv -s $(du -sb $1 | awk '{print $1}') | gzip > 1.tar.gz
```

##### max gzip level and remove

```
GZIP=-9 tar czf sd211.tar.gz src_folder/ --remove-files
GZIP=-9 tar czf zzz.tar.gz  --remove-files
```

##### referencing all variables in bash script 

```
$*
```

##### sha1sum

```
sha1sum yourfile
openssl sha1 youfile        # mac
```

##### md5sum

```
md5sum yourfile
md5 yourfile                # mac
```

##### kernel version

```
uname -r
cat /proc/version
```


### wget

```
wget --content-disposition "url"
```

##### wget from file list

```
xargs -i wget '{}' < your_list
```


### grep
##### without a string, -L

```
grep -iL '"format_id": "136"' *
```

##### grep by ex

```
find . -name "*.ini" -print0 | xargs -0 grep 'anything'
find . -name "*.jsp" -print0 | xargs -0 grep '<body>' | awk -F : '{print $1}' | sort | uniq | xargs sed -i 's/<body>/<body><script src=\"\/\/static.glodon.com\/yun\/latest\/common\/js\/ga.js\"><\/script>/g'
```

##### extension

```
grep -ri --include \*.cs "bitconverter"
```

##### exclude

```
find . -print | grep -v '.bak$' | grep -v '.old$' | grep -v '[%~]$'
```

##### Searching for a hyphen

```
man grep | grep .-i
man grep | grep '[-]i'
man grep | grep "[-]i"
man grep | grep -e -i
```

##### man grep

```
-a, --text
-L, --files-without-match
-l, --files-with-matches
-s, --no-messages
-w, --word-regexp
-e PATTERN
-f FILE, --file=FILE
--exclude-from=FILE
--exclude-dir=DIR
```

##### egrep is 100% equivalent to grep -E

```
-E, --extended-regexp
```

##### fgrep is 100% equivalent to grep -F

```
-F, --fixed-strings
```


### find

```
-i 			# ignore case
-name
-type f
-type d
-print
-delete
```

##### -exec
##### ; a single grep command for each file

```
find . -exec grep "chrome" {} \;                # \ is escape, \; is just ;
```

##### + as many files as possible are given as parameters to grep at once

```
find . -exec grep "chrome" {} +
```

##### line calculator

```
find . -name "*.cs" | xargs cat | wc -l
```

##### multi-threading grep

```
find . -type f -print0 | xargs -0 -n1 -P8 grep -H "string"
```

##### delete recursively

```
find . -name "*.meta" -type f -delete
```

##### list all file & sort by size
##### asc

```
ls -lhR | grep '^-' | sort -k 5 -h
find . -type f -print0 | xargs -0 du -sh |sort -h
```

##### desc  

```
ls -lhR | grep '^-' | sort -k 5 -rh
find . -type f -print0 | xargs -0 du -sh |sort -rh
```

##### desc and exclude path and bigger than 50M, k, M, G, c for byte, + for bigger than, - for smalller than

```
find . -path ./Game -prune -o -type f -size +50M -print0 | xargs -0 du -sh |sort -rh
```

##### exclude multiple path

```
find . -type d \( -path dir1 -o -path dir2 -o -path dir3 \) -type f -size +50M -print0 | xargs -0 du -sh |sort -rh
find . -path ./Game -prune -o -path ./Windows/Installer -prune -o -path ./Program\ Files\ \(x86\)/Unity463 -prune -o -path ./Program\ Files/Unity511 -prune -o -path ./Program\ Files\ \(x86\)/Scaleform -prune -o -type f -size +50M -print0 | xargs -0 du -sh |sort -rh > 2.txt
```


### xargs
##### xargs explained  
Convert input to a single line.  
Usually many UNIX operating system doesn't accept such a long list of argument, xargs command divide that list into sub-list with acceptable length and made it work.  
"|" the unix pipe receives data on STDIN, however typically small utility programs that you write yourself don't accept their commands on standard in, they expect it to be spelled out in the arguments to the command, in this situation you have to use xargs:

```
devuser@system:/etc find . -name "*bash*"
./bash.bashrc
./bash.bash_logout
./defaults/etc/bash.bashrc
./defaults/etc/bash.bash_logout
./defaults/etc/skel/.bashrc
./defaults/etc/skel/.bash_profile
./postinstall/bash.sh.done
./setup/bash.lst.gz
./skel/.bashrc
./skel/.bash_profile
devuser@system:/etc find . -name "*bash*" | xargs
./bash.bashrc ./bash.bash_logout ./defaults/etc/bash.bashrc ./defaults/etc/bash.bash_logout ./defaults/etc/skel/.bashrc ./defaults/etc/skel/.bash_profile ./postinstall/bash.sh.done ./setup/bash.lst.gz ./skel/.bashrc ./skel/.bash_profile
```

so all .java files in multiple lines will be converted to a single line and added behind grep "Stock" as arguments:

```
find . -name "*.java" | xargs grep "Stock"
```

#####  -I{} and {} explained
-I replace-str so {} is the replace-str

```
ls | xargs rm           # will invoke rm a.txt b.txt
ls | xargs -I{} rm {}   # will invoke rm a.txt, rm b.txt, ... one by one
```

so this is useful for program that don't accept varargs
#####  convert space to 0 ascii

```
find /tmp -name "*.tmp" -print0 | xargs -0 rm
```


### sed
#####  sed is turing complete
http://www.computerhope.com/unix/used.htm  

Code | Meaning
---|---
-n, --quiet, --silent | Suppress automatic printing of pattern space
-e script, --expression=script | Add the script script to the commands to be executed.
-f script-file, --file=script-file | Add the contents of script-file to the commands to be executed.
--follow-symlinks | Follow symlinks when processing in place.
-i[SUFFIX], --in-place[=SUFFIX] | Edit files in place (this makes a backup with file extension SUFFIX, if SUFFIX is supplied). Used for saving memory
-l N, --line-length=N | Specify the desired line-wrap length, N, for the "l" command.
--POSIX | Disable all GNU extensions.
-r, --regexp-extended | Use extended regular expressions in the script.
-s, --separate | Consider files as separate rather than as a single continuous long stream.
-u, --unbuffered | Load minimal amounts of data from the input files and flush the output buffers more often.
#####  basic form

```
sed 's/regexp/replacement/g' inputFileName > outputFileName
```

#####  & for "entire match", \1 to \9 for match 1-9

```
sed -r 's/(cat|dog)s?/\1s/g'
sed 's/\(cat\|dog\)s\?/\1s/g'           # without -r option, equivalent to above cmd, it's very verbose, so -r option is useful
```

##### -e multi-pass

```
sed -e 's/^#\([^#]*\)$/##### \1/g' -e 's/^\[\(.*\)\]$/### \1/g' linux-cmd.md > 1.md
sed -r -e 's/^#([^#]*)$/##### \1/g' -e 's/^\[(.*)\]$/### \1/g' 
```


### chown & chmod
##### change user & group of a folder

```
owner+group+all
chown -R owner:group folder
```


### ln
##### hard link

```
ln sourcefile link
```

##### symbolic link

```
ln -s sourcefile link
```

##### same name

```
ln sourcefile .
ln -s sourcefile .
```


### screen

Code | Meaning
---|---
"Ctrl-a" "c" | create
"Ctrl-a" "n" | next
"Ctrl-a" "p" | previous
"Ctrl-a" "d" | detach
screen -r | reattach

### less

Code | Meaning
---|---
-N | line number
/ | search
n | continue searching
N | continue searching backwards
G | end of file
gg | beginning of file
24g | line 24
enter | scroll one line

### netstat

```
netstat -antp | grep nginx
-a --all
-n --numeric
-t --tcp
-p --program
```


```
netstat -antp | grep nginx | awk '{print $5}' | cut -d: -f1 | sed -e '/^$/d' |sort | uniq -c | sort -n
```


### zip
unzip

### Split & Merge
##### KB	1000    K	1024    MB	1000 x 1000     M	1024 x 1024

```
split --byte=2G/2M sourcefile destfileprefix
```

##### merge

```
cat fileprefix* > mergefile
```


### ps
##### show start time

```
ps -eo pid,comm,lstart,etime,time,args
ps -eo pid,comm,etime | grep honey
```


### scp

```
sshpass -p "Agbms198964" scp -r root@104.238.128.90:/srv/r
rsync -r --ignore-existing KChase root@104.238.128.67:/srv/r/KChase
nohup sshpass -p "Agbms198964" scp -r root@104.238.129.11:/srv/r /srv/r &
nohup sshpass -p "Agbms198964" rsync -r --ignore-existing root@104.238.129.11:/srv/r /srv/r/r &
```


### sort
http://www.computerhope.com/unix/usort.htm  

Code | Meaning
---|---
default | alphabetical order
-n | --numeric-sort
-r | --reverse
-h | --human-numeric-sort
-u | uniq
-k 2 | sort by 2nd column
-k 2n | sort by 2nd column numerically
-k2,2 -k1,1 | -k2,2 specifies sorting on the key starting and ending with column 2, -k1,1 dictates breaking ties using the value in column 1
-b | --ignore-leading-blanks
-d | --dictionary-order
-f | --ignore-case
-g | --general-numeric-sort
-i | --ignore-nonprinting
-M | --month-sort
-R | --random-sort

#####  example

```
sort -t'|' -k2 zipcode			# delimited by '|'
Adam|12345
Wendy|23456
Bob|34567
Sam|45678
Joe|56789
```


```
sort -k2,2 -t $'\t' phonebook 	# delimited by '\t'
Doe, John	555-1234
Fogarty, Suzie	555-2314
Doe, Jane	555-3214
Avery, Cory	555-4132
Smith, Brett	555-4321
```


### uniq
http://www.computerhope.com/unix/uuniq.htm  
Outputs the file with ADJACENT!!! identical lines collapsed to one, so to really get rid of all duplicate, one must sort the file first then uniq  

Code | Meaning
---|---
-u | Print only those lines which are not repeated (unique) in the input.
-d | Print only those lines which are repeated in the input.
-c | Generate an output report in default style except that each line is preceded by a count of the number of times it occurred. 
-i | Ignore case differences when comparing lines
-f | Ignore a number of fields in a line
-s | Skips a number of characters in a line
-w | Specifies the number of characters to compare in lines, after any characters and fields have been skipped
#####  see the list of lines in a file, sorted by the number of times each occurs:
sort file | uniq -c | sort -n

### head
http://www.computerhope.com/unix/uhead.htm  

Code | Meaning
---|---
-n2 | [:2]
-n+2 | [:2]
-n-2 | [:-2]
-q, --quiet, --silent | bever output headers giving file names

### tail
http://www.computerhope.com/unix/utail.htm
-f, --follow			monitor file
-n2						[-2:]
-n+2					[2:]
-n-2					[-2:]
-q, --quiet, --silent	bever output headers giving file names

### nl
http://www.computerhope.com/unix/nl.htm  
nl stands for number lines


```
cat list.txt
apples
oranges
potatoes
lemons
garlic
```


```
nl list.txt
 1	apples
 2	oranges
 3	potatoes
 4	lemons
 5	garlic
```

 
### cut
http://www.computerhope.com/unix/ucut.htm  
#####  4-10 characters of each line

```
cut -c 4-10 file
```

#####  5 field to the end of the line of each line using the ":" character as the field delimiter

```
cut -d ":" -f 5- file
```


### tr
http://www.computerhope.com/unix/utr.htm  
tr stands for translate  

```
cat 0.json | tr 'abcd' 'jkmn'
```


### strings
strings is a program in Unix-like operating systems that finds and prints text strings embedded in binary files such as executables

### fold
https://en.wikipedia.org/wiki/Fold_(Unix)  
#####  output 10 characters per line

```
fold -w 10 0.json
```


### comm
http://www.computerhope.com/unix/ucomm.htm  
comm stands for common  

Code | Meaning
---|---
-1 | suppress column 1 (lines unique to FILE1)
-2 | suppress column 2 (lines unique to FILE2)
-3 | suppress column 3 (lines that appear in both files)
#####  print only the lines present in both myfile1.txt and myfile2.txt

```
comm -12 myfile1.txt myfile2.txt
```

#####  print only the lines that are present in myfile1.txt and not myfile2.txt, and vice versa

```
comm -3 myfile1.txt myfile2.txt
```


### tee
http://www.computerhope.com/unix/utee.htm  
tee stands for T-splitter used in plumbing  
reads standard input and writes it to both standard output and one or more files, like write to log file and standard output at the same time

```
wc -l 0.json | tee 1.txt
```


### wc

Code | Meaning
---|---
-l | line count (note that if the last line does not have \n, it will not be counted)
-c | byte count
-m | character count
-L | length of longest line
-w | word count

### tac
line by line backwards, so forth the name 'tac' is the reverse of 'cat' 

### rename
##### space to underscore
##### g means global, replace all occurrences

```
find . -type f | rename 's/ /_/g'
find . -type f | rename 's/#//g'
find . -type f | rename 's/\+//g'
```


### Zisc
##### sort by size
##### asc

```
du -sh * | sort -h
```
 
##### desc        

```
du -sh * | sort -hr
```

##### disable alias

```
command ls
```

##### display remind space

```
command df | awk '{if (NR == 2) print ($2-$3)/1048576, "GB"}'
command df | awk '{if (NR == 2) printf("%.2f GB\n",($2-$3)/1048576)}'
```

##### cp folder as hardlinks

```
cp -r -l folder
```
 
##### clean apt

```
apt-get autoremove
apt-get clean
```


### 7zip

```
apt-get install p7zip-full
```

#####  create

```
7z a basic.7z basic
```

#####  extract

```
7z e basic.7z
```


### ffmpeg

```
ffmpeg -f concat -i mylist.txt -c copy 1.mp4
file 'VTS_02_1.mp4'
file 'VTS_02_2.mp4'
file 'VTS_02_3.mp4'
file 'VTS_02_4.mp4
```
'


```
ffmpeg -i 2.m2ts -c copy -ss 01:30:59 -t 02:29:15 -sn 3.m2ts
```


### OpenJDK

```
make ARCH_DATA_MODEL=64 HOTSPOT_BUILD_JOBS=4 PARALLEL_COMPILE_JOBS=4 2>&1|tee c:/Java/output_amd64.log
make ARCH_DATA_MODEL=64 HOTSPOT_BUILD_JOBS=4 PARALLEL_COMPILE_JOBS=4 GENERATE_DOCS=false BUILD_HOTSPOT=true BUILD_JDK=false BUILD_LANGTOOLS=false BUILD_CORBA=false BUILD_JAXP=false BUILD_JAXWS=false BUILD_INSTALL=false BUILD_SPONSORS=false 2>&1|tee c:/Java/output_amd64.log
```
