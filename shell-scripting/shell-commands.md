🔹 Basics

Topic	Key Syntax	Example
Shebang	#!/usr/bin/env bash	Start script
Make Executable	chmod +x file.sh	chmod +x script.sh
Run Script	./file.sh	./script.sh
Run (Alt)	bash file.sh	bash script.sh
Comment	# comment	echo "Hi" # inline comment
🔹 Variables & Input

Topic	Key Syntax	Example
Variable	VAR="value"	NAME="DevOps"
Use Variable	"$VAR"	echo "$NAME"
Read Input	read VAR	read USER
🔹 Arguments & Exit Status

Topic	Key Syntax	Example
Positional Args	$1 $2	./script.sh arg1 arg2
Arg Count	$#	echo $#
All Args	$@	echo "$@"
All Args (Alt)	$*	echo "$*"
Script Name	$0	echo "$0"
Exit Status	$?	echo $?
🔹 Conditionals

Topic	Key Syntax	Example
String Equal	[ "$a" = "$b" ]	[ "$name" = "Linux" ]
String Not Equal	[ "$a" != "$b" ]	[ "$name" != "Ubuntu" ]
Empty Check	[ -z "$var" ]	[ -z "$name" ]
Not Empty	[ -n "$var" ]	[ -n "$name" ]
Integer Compare	[ "$a" -gt 10 ]	[ "$num" -eq 5 ]
File Exists	[ -e file ]	[ -e file.txt ]
File Check	[ -f file ]	Regular file
Directory Check	[ -d dir ]	[ -d /home ]
If Condition	if [ cond ]; then ... fi	if [ -f file ]; then echo OK; fi
Case Statement	case var in ... esac	(see below)
AND	cmd1 && cmd2	mkdir test && cd test
OR	`cmd1	
NOT	! condition	! [ -f file ]
Note: OR --> cmd1 || cmd2 --> cd dir || pwd

🔹 Loops

Topic	Syntax	Example
For Loop	for i in list; do ... done	for i in 1 2 3; do echo "$i"; done
For Files	for f in *	for f in *; do echo "$f"; done
C-Style	for ((i=1;i<=3;i++))	for ((i=1;i<=3;i++)); do touch f$i; done
While Loop	while [ cond ]; do ... done	while [ "$a" -lt 5 ]; do echo "$a"; done
Until Loop	until [ cond ]; do ... done	wait until success
Break	break	exit loop
Continue	continue	skip iteration
🔹 Functions

Topic	Key Syntax	Example
Function	name() { ... }	greet(){ echo "Hi"; }
Function Args	$1 $2	add(){ echo $(($1+$2)); }
Return (Function)	return <0–255>	return 0
Exit (Script)	exit <0–255>	exit 1
Capture Output	result="$(func)"	val="$(date)"
Local Variable	local var=value	local count=10
🔹 Text Processing

Tool	Key Syntax	Example
grep	grep pattern file	grep -i "error" log.txt
awk	awk '{print $1}' file	awk -F: '{print $1}' /etc/passwd
sed	sed 's/old/new/g' file	sed -i 's/foo/bar/g' file.txt
cut	cut -d: -f1 file	cut -d: -f1 /etc/passwd
sort	sort file	sort -n numbers.txt
uniq	sort file | uniq	sort file | uniq -c
tr	tr 'a-z' 'A-Z'	echo hi | tr 'a-z' 'A-Z'
wc	wc -l file	wc -w file.txt
head	head -n 5 file	head -n 10 log.txt
tail	tail -f file	tail -f app.log
🔹 Extras

Topic	Syntax	Example
Command Substitution	$(command)	now=$(date)
Old Substitution	`command`	`date`
Safer Test	[[ condition ]]	[[ "$a" == "$b" ]]
Arithmetic	$((expression))	echo $((5+3))
Redirect Output	>	echo hi > file.txt
Append Output	>>	echo hi >> file.txt
Error Redirect	2>	cmd 2> error.log
All Output	&>	cmd &> all.log
Pipe	|	ls | grep txt
Quote Variables	"$var"	echo "$name"
