# hello

  little attempt to study Git  
check .md formatting: try newline after 2 spaces  
(hope to see text in new lines without empty line)

and new paragraph
... (in one line)


And I want to have true modtime? when switch beetwen branches.

Here it is (pre-commit hook)

<pre>
#!/bin/sh
#
# add mtimes of commited files to .mtimes
# .git/hooks/pre-commit
#

esc_q ()
{
    printf '%s' "$*" | sed "s/'/'\\\\''/g" | sed 's/\\/\\\\/g'
}

BRANCH=$(git status -s -b --p | head -1)
MTIMES=.cur-mtimes-`hostname`-$USER
TNEW=/tmp/$$.new-mtimes
> $TNEW

for i in $(git ls-tree -r --name-only HEAD | grep -v $MTIMES)
do
#    echo process $i
    mt=$(date +%Y%m%d%H%M.%S -r "$i")
    fn=`esc_q "$i"`
    echo touch -m -t $mt \'"$fn"\' >> $TNEW
done

if [ -f $MTIMES ] ; then
    if tail -n +2 $MTIMES | cmp $TNEW >/dev/null 2>&1 ; then
	echo No mtime changes
	rm -f $TNEW
	exit
    fi
fi

echo \#$(date) WD:$(pwd)  \($BRANCH $(git rev-parse HEAD)\) > $MTIMES
cat $TNEW >> $MTIMES
chmod +x $MTIMES
rm -f $TNEW 
git add $MTIMES

</pre>