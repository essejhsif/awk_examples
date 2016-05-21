# awk_examples
A collection of awk / sed examples


## Contents

- [Basic awk & sed](#basic-awk--sed)
- [sort, uniq, cut, etc.](#sort-uniq-cut-etc)
- [Other generally useful aliases for your .bashrc](#other-generally-useful-aliases-for-your-bashrc)
- [Basic awk & sed Overview](http://www-users.york.ac.uk/~mijp1/teaching/2nd_year_Comp_Lab/guides/grep_awk_sed.pdf)

## Basic awk & sed
[[back to top](#contents)]


Extract fields 2, 4, and 5 from file.txt:

    awk '{print $2,$4,$5}' input.txt

Count number of columns in file.txt:

    awk '{print NF; exit}' file.txt

Print each line where the 5th field is equal to ‘abc123’:

    awk '$5 == "abc123"' file.txt


Print each line where the 5th field is *not* equal to ‘abc123’:

    awk '$5 != "abc123"' file.txt


Print each line whose 7th field matches the regular expression:

    awk '$7  ~ /^[a-f]/' file.txt


Print each line whose 7th field *does not* match the regular expression:

    awk '$7 !~ /^[a-f]/' file.txt


Get unique entries in file.txt based on column 2 (takes only the first instance):

    awk '!arr[$2]++' file.txt


Print rows where column 3 is larger than column 5 in file.txt:

    awk '$3>$5' file.txt

'Unpivot' file.csv data. Second column delimited by |, create one row per value; 
insert new values from column 2 to the end (column 5).

    awk '{n=split($2,s,"|");for (i=1;i<=n;i++) {$5=s[i];print}}' file.csv

Sum column 1 of file.txt:

    awk '{sum+=$1} END {print sum}' file.txt


Compute the mean of column 2:

    awk '{x+=$2}END{print x/NR}' file.txt


Given a file with data in the format of column 1 data|column 2 data, 
print out data so it is in the form of ("column 1","column 2")

    awk 'BEGIN {FS="|"}; { printf(" (\"%s\",\"%s\"),", $1, $2 ); }' < file.txt

Number each line in file.txt:

    sed = file.txt | sed 'N;s/\n/ /'

Or:

    cat -n file.txt


Replace all occurances of `foo` with `bar` in file.txt:

    sed 's/foo/bar/g' file.txt


Trim leading whitespaces and tabulations in file.txt:

    sed 's/^[ \t]*//' file.txt


Trim trailing whitespaces and tabulations in file.txt:

    sed 's/[ \t]*$//' file.txt


Trim leading and trailing whitespaces and tabulations in file.txt:

    sed 's/^[ \t]*//;s/[ \t]*$//' file.txt


Delete blank lines in file.txt:

    sed '/^$/d' file.txt


## sort, uniq, cut, etc.
[[back to top](#contents)]


Count the number of unique lines in file.txt

    cat file.txt | sort -u | wc -l


Find number of lines shared by 2 files (assumes lines within file1 and file2 are unique):

    sort file1 file2 | uniq -d


Sort numerically (with logs) (g) by column (k) 9:

    sort -gk9 file.txt


Find the most common strings in column 2:

    cut -f2 file.txt | sort | uniq -c | sort -k1nr | head


Pick 10 random lines from a file:

    shuf file.txt | head -n 10


Print all possible 3mer DNA sequence combinations:

    echo {A,C,T,G}{A,C,T,G}{A,C,T,G}


Untangle an interleaved paired-end FASTQ file. If a FASTQ file has paired-end reads intermingled, and you want to separate them into separate /1 and /2 files, and assuming the /1 reads precede the /2 reads:

    cat interleaved.fq |paste - - - - - - - - | tee >(cut -f 1-4 | tr "\t" "\n" > deinterleaved_1.fq) | cut -f 5-8 | tr "\t" "\n" > deinterleaved_2.fq


## Other generally useful aliases for your .bashrc
[[back to top](#contents)]


Get a prompt that looks like `user@hostname:/full/path/cwd/:$ `

    export PS1="\u@\h:\w\\$ "


Never type `cd ../../..` again (or use [autojump](https://github.com/joelthelion/autojump), which enables you to navigate the filesystem faster):

    alias ..='cd ..'
    alias ...='cd ../../'
    alias ....='cd ../../../'
    alias .....='cd ../../../../'
    alias ......='cd ../../../../../'


Ask before removing or overwriting files:

    alias mv="mv -i"
    alias cp="cp -i"  
    alias rm="rm -i"


My favorite `ls` aliases:

    alias ls="ls -1p --color=auto"
    alias l="ls -lhGgo"
    alias ll="ls -lh"
    alias la="ls -lhGgoA"
    alias lt="ls -lhGgotr"
    alias lS="ls -lhGgoSr"
    alias l.="ls -lhGgod .*"
    alias lhead="ls -lhGgo | head"
    alias ltail="ls -lhGgo | tail"
    alias lmore='ls -lhGgo | more'


Use `cut` on space- or comma- delimited files:

    alias cuts="cut -d \" \""
    alias cutc="cut -d \",\""


Pack and unpack tar.gz files:

    alias tarup="tar -zcf"
    alias tardown="tar -zxf"


Or use a generalized `extract` function:

    # as suggested by Mendel Cooper in "Advanced Bash Scripting Guide"
    extract () {
       if [ -f $1 ] ; then
           case $1 in
            *.tar.bz2)      tar xvjf $1 ;;
            *.tar.gz)       tar xvzf $1 ;;
            *.tar.xz)       tar Jxvf $1 ;;
            *.bz2)          bunzip2 $1 ;;
            *.rar)          unrar x $1 ;;
            *.gz)           gunzip $1 ;;
            *.tar)          tar xvf $1 ;;
            *.tbz2)         tar xvjf $1 ;;
            *.tgz)          tar xvzf $1 ;;
            *.zip)          unzip $1 ;;
            *.Z)            uncompress $1 ;;
            *.7z)           7z x $1 ;;
            *)              echo "don't know how to extract '$1'..." ;;
           esac
       else
           echo "'$1' is not a valid file!"
       fi
    }



Use `mcd` to create a directory and `cd` to it simultaneously:

    function mcd { mkdir -p "$1" && cd "$1";}


Go up to the parent directory and list it's contents:

    alias u="cd ..;ls"


Make grep pretty:

    alias grep="grep --color=auto"


Refresh your `.bashrc`:

    alias refresh="source ~/.bashrc"

Common typos:

    alias mf="mv -i"
    alias mroe="more"
    alias c='clear'
