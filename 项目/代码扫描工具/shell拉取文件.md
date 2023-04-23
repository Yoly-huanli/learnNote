####

[toc]

## source filename.sh

被执行文filename.sh中的变量，传递到了当前的shell中。

## 变量

```
$1  表示传递过来的字符串
```

## 数组

变量=（xx,xx,xx）表示数组

```shell
array_name=(value1 value2  valuen)
```

读取数组元素

```
${array_name[index]}  读取某一项
${array_name[@]}使用@ 或 * 可以获取数组中的所有元素
```



## if语句

```
if [ command ];then
xxx
elif [ command ];then
xxx
else
xxx
fi
```

## for语句

```js
for i（变量的名字） in {1..10}（变量的范围）
do
	echo $i（输出变量i的所有值）
done
```

## 正则匹配

使用双中括号[[]] 和 =~ 来判断字符串是否匹配给定的正则表达式

```SHELL
filelist="lvlvcheck dablelvlv checklvlv"
for file in $filelist
do 
	if [[ $file =~ lvlv$ ]]
	then
		echo $file
	fi
done
```

${BASH_REMATCH[0]}：代表了if中整个匹配到的字符串 后边BASH_REMATCH 中下标为n的元素是字符串中与第n 个括号里面的子模式匹配的部分

## 文件位置

```
表示当前位置： $(pwd)
变量： $project_dir
```

## 文件相关

https://blog.csdn.net/icanflyingg/article/details/121125994

```
[ -d FILE ] 如果 FILE 存在且是一个目录则为真。	
```

## 函数

直接调用

```shell
function countLine(){
   XXXX
}

echo "Call function countLine"
countLine           # 函数调用
```

## 示例：

### 更新文件夹

```SHELL
function update_project () {
  echo " ---- cd $test_dir ---- "
  cd $test_dir
  regex="\/(.+).git" 
  for loop in ${test_list[@]}
  do
    if [[ $loop =~ $regex ]]
      then
          name="${BASH_REMATCH[1]}"
          dir="$(pwd)/$name"
          if [ -d $name ]; then
            update $name
          else 
            mkdir $name
            git clone $loop
            update $name
          fi
      else
        echo " ---- $loop doesn't match ---- "
    fi
  done
  cd ../
}
```

### 更新文件

```shell
function update () {
    cd $1
    git stash
    git checkout master
    git pull origin master
    cd ../
}
```



