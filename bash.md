Frequent cmds:

```
grep -Ei <expr> <file>
grep -Eir <expr> <dir>
# (the \ before the * is necessary only if you have *.cpp files in cwd,
# prefix cmd line with 'echo' to see shell char substitutions)
grep -r foo --include \*.cpp src/
# or the same with find instead of --include (note trailing \;):
find src/ -name \*.cpp -exec grep -n "foo" {} \; -print
# add grep -R for follow symbolic links

# find
find . -iname "*.cpp" # or -name or -path
find ../AMCC -path */xerces*.vcproj
find ../AMCC -path */xerces*.vcproj -exec head -n 10 {} \;
# or with xargs which imo is less cryptic:
find ../AMCC -path */xerces*.vcproj | head -n 5 | xargs head -n 10
find ../AMCC -path */xerces*.vcproj | head -n 5 | xargs --verbose head -n 10
find ../AMCC -path */xerces*.vcproj | head -n 5 | xargs -I % head -n 10 % # or use 'filename' instead of %
find ../AMCC -path */xerces*.vcproj | head -n 5 | xargs --verbose -I % head -n 10 %
find ../AMCC -size +100000 | xargs ls -lh
# to create hg repo from existing dir tree
hg init
find . -type f -not -path ./.hg\* | xargs hg add

# xargs exec'ing multiple cmds which each found file (from http://stackoverflow.com/questions/6958689/xargs-with-multiple-commands-as-argument)
find ../AMCC -path */xerces*.vcproj | head -n 5 | xargs --verbose -I % sh -c "head -n 10 % && ls -l %"

# xargs with other sources than find:
ls -1
ls -1 | xargs  # just concats lines and feeds as single param list to single echo
ls -1 | xargs --verbose
ls -1 | xargs --verbose -I % echo % # one echo process per line
echo -e "a\nb\nc" | xargs --verbose -I % echo %

# tar
tar xvf arch.tar.gz -C tmp/a/b/c
tar tf arch.tar.gz # or simply less arch.tar.gz
tar czvf arch.tar.gz foo bar
# when tar x messes up file permissions (e.g. when the tar was created
# on Windows):
tar tzf win-changes.tgz | xargs chmod --reference=foo.txt
find somedir -type f | xargs chmod --reference=foo.txt

# zip
zip -r -X -xthirdparty\* -x\*.sdf -x\*Debug\* bla.zip bla/
# curl
curl -O http://whatever.org/foo  # like wget (-o to set filename)
curl -v
curl -u user:pass
curl --help | grep header
curl -X POST -H "Content-Type: application/json" -d '{"username":"xyz","password":"xyz"}' http://localhost:3000/api/login # or use --data instead -d

# openssl / telnet to http(s) server
telnet www.google.com 80
openssl s_client -connect www.google.com:443
# paste HTTP headers:
GET / HTTP/1.0
Host: www.google.com
Accept: */*\r\n
Connection: close
# follow with enter+enter

# unnecessary cat, replace with <
cat foo | grep bla
< foo grep bla # slightly faster, whatever
grep bla < foo # just different syntax

# create new files without vi/vim/...
cat > my_temp_file.txt # end with ctrl-D
echo -e "foo\nbar\nbaz" > my_temp_file.txt
echo -e "\nlast line" >> my_temp_file.txt # append

tail -f <file>
ln [-s] <src> <dst>
$PWD
echo `pwd`
whoami
hostname
ifconfig
echo $0 # prints -bash
pushd .; popd; dirs
nohup longrunningprog &
ps -A | grep java | head -n 1 | cut -d' ' -f3 | xargs kill -9
kill -9 1234
killall vim
source ~/.bashrc

# variants for searching jars
# a) search single dir:
for i in *.jar; do jar -tvf "$i" | grep -Hsi SomeClassName; done
# b) search dir tree
find dir/ -name \*.jar -exec jar -tvf {} \; | grep -Hsi SomeClassName

# less frequent cmds
chmod --reference=file1 file2
scp foo.txt kschubert@192.168.190.128:
scp foo.txt kschubert@192.168.190.128:foo.txt
yum / apt-get / apt-search
somecmd | tee savedIntermediateOutput | nextcmd # T pipe
ip addr # or ifconfig
sudo lsof -i # show ports listened on
sudo lsof -p <procid> # shows all FDs for procid: cwd, ports, files, ...
sudo lsof | grep <somelockedfile>
sudo iptables -L # list
sudo iptables --line -vnL # list more verbosely
sudo iptables -I INPUT 5 -i eth0 -p tcp --dport 6080 -m state --state NEW,ESTABLISHED -j ACCEPT
ls -l /proc/*/cwd | grep <someworkdir>
# procmon (from http://www.dedoimedo.com/computers/linux-cool-hacks.html)
sudo apt-get install acct
dump-acct /var/log/account/pacct
/etc/init.d/acct start/stop
iostat
netstat
netcat
lsmod
df .
cat /proc/cpuinfo
cat /proc/meminfo
cat /proc/version
uname -r
somecmd | vim - # the '-' makes it read stdin (cmds read from stderr)
hexdump -C <file> # canonical hexdump format
```

Chaining cmds:

See http://www.ibm.com/developerworks/aix/library/au-badunixhabits.html#three,
so prefer && chaining over ; (unless the chained cmds are indep and could run in
parallel). Use case for || chaining?

Links:

* http://cli.learncodethehardway.org/bash_cheat_sheet.pdf
* http://www.johnstowers.co.nz/blog/pages/bash-cheat-sheet.html
* http://www.ibm.com/developerworks/aix/library/au-badunixhabits.html
* http://www.catb.org/jargon/html/U/UUOC.html

Order of Interpretation: The bash shell carries out its various types of interpretation for each line in the following order (from http://www.johnstowers.co.nz/blog/pages/bash-cheat-sheet.html):

* brace expansion         (see a reference book)
* ~ expansion             (for login ids)
* parameters              (such as $1)
* variables               (such as $var, see
  [here](http://www.ibm.com/developerworks/aix/library/au-badunixhabits.html#four)
  for details)
* command substitution    (Example:  match=\`grep DNS *` )
* arithmetic              (from left to right)
* word splitting
* pathname expansion      (using *, ?, and [abc] )

ssh cmds
```
ssh-keygen -t dsa -P '' -f ~/.ssh/id_dsa # dsa is gov standard (limited bit len)
ssh-keygen -t rsa -b 2048
cat ~/.ssh/id_dsa.pub >> ~/.ssh/authorized_keys # for ssh to localhost
ssh-copy-id user@machine # writes ~/.ssh/authorized_keys
mosh -6 $DEVSERVER
# if mosh lists detached sessions just kill them (or use mosh -d?)
tmux attach
mosh -d -6 $DEVSERVER -- tmux attach # same in 1 cmd
``

Edit shortcuts
---
See [here](https://www.gnu.org/software/bash/manual/html_node/Commands-For-Moving.html):
ctrl-a/e goto begin/end of line
ctrl-u/k deletes/kills until begin/end of line
esc+f/b moves forward/backward 1 word (on mac, otherwise alt)
