# funny-bash
Practical Bash-examples you are searching

### basic path
``` bash
BASE_DIR=$(cd `dirname $0`; pwd)
```

### random number between
``` bash
echo $RANDOM
```

### builtin variables
``` bash
$0  # script name
$n  # nth param passed to script
$*  # param string, "$1 $2 $3 ..."
$#  # params length
$$  # script pid
$!  # the short name of last command
$?  # the return code of last command
$@  # params, "$1" "$2" "$3" ...
${FUNCNAME[0]} #current function name
```

### batch tasks with limited concurrency
``` bash
fifoFile="${0}.fifo"
threads=3

mkfifo ${fifoFile}
exec 6<>${fifoFile}
rm ${fifoFile}

for ((i=0;i<$threads;i++));do echo done >&6

for ((i=0;i<10;i++));do
    read -u6
    {
        # real tasks
        echo $i && sleep $i
        echo >&6
    } &
done

wait
exec 6>&-
```

### list copy
``` bash
b=("${a[@]}")
```

### split string into array
``` bash
read -d '\n' -r -a array <<< "${data}"
```

### traverse array
``` bash
for index in "${!array[@]}"
do
    echo "$index ${array[index]}"
done
```

### unicode encode
``` bash
var="$(echo -n 测试 | od -An -tx1)"; printf '%%%s' ${var^^};echo
```

### search/check ipv4
``` bash
grep -oE "\b([0-9]{1,3}\.){3}[0-9]{1,3}\b"
```

### pass system signal to subprocesses
``` bash
function trap_with_arg() {
    func="$1" ; shift
    for sig ; do
        trap "$func $sig" "$sig"
    done
}

function catch_signal() {
    c1=$(pgrep -P $$)
    c2=$(pgrep -P $$)
    for i in `comm <(echo "$c1") <(echo "$c2") -1 -2`;do
        echo kill -$1 ${i}
        kill -$1 ${i} > /dev/null 2>&1
    done
}

trap_with_arg catch_signal INT TERM HUP USR1 USR2 WINCH

echo $$
while ! wait;do
    echo wait children end
    pgrep -P $$
done
```
