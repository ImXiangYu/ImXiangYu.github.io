# Git常用命令

```git
git add .
git commit -m "xxx"
git push origin jask-dev:Ayu

git pull origin Ayu:jask-dev
```

```
git checkout -b branch_name // 切换分支 如果不存在就创建
-b 不存在就创建
```

```
git status
git rebase
```

```
git log
git restore
```

```
git reset --hard //版本号 回滚到之前某版本，此时本地已经回滚，远程仓库还未回滚
git push -f      //回滚远程仓库版本
```

# Vim常用操作

1. 三种模式：插入模式、命令模式、尾行模式

2. HJKL分别代表光标的左下上右（记不住可以使用↑↓←→）

3. v 可视模式，可以对代码块进行选中，方便复制

4. `^ $` 分别代表跳转到行首（Home和End） `%` 代表跳转到被{}包围的代码块的尾部和头部

5. 操作内容(复制、粘贴、删除)
   ```
   yy 复制内容(Ctrl+c)
   p  粘贴内容(Ctrl+v)
   dd 删除内容(Ctrl+x)
   ```
   同时可以在其之前加上数字，表示执行的次数

6. 在尾行模式输入`set number(set nu)`可以查看行号 `set nonumber`关闭行号显示

7. 快速翻页
   
   ```
   Ctrl+f (forward)  向前翻页
   Ctrl+b (backward) 向后翻页
   Ctrl+u (up)          向上翻半页
   Ctrl+d (down)      向下翻半页
   大写G 跳转到文件的最后一行
   两个小写gg 跳转到文件的第一行
   100G 跳转指定行(跳转到第100行)
   :50 跳转到50行
   ```

8. 查找
   /hello 从光标所在位置开始向下查找
   ?hello 从光标所在位置开始向上查找
   找到目标后可以输入n(next) 相对于现在的查找方向的下一个
   输入大写N可以相对于现在查找方向的反方向查找下一个
   (默认区分大小写)
   如果不想匹配大小写可以输入/hello`\c`来匹配所有
   或者输入
   
   ```
   :set ic(ignore case) 修改全局大小写设置
   ```

9. 替换 `:n1,n2s/old/new/g` 范围n1, n2行，不加表示替换当前行，s表示替换，g表示全局，不加g会替换掉每一行第一个匹配到的内容，加上g后会替换掉所选范围内的所有内容
   1,$表示从1到文尾

10. 撤销
    u (Ctrl+z)

11. 配置文件
    .vimrc 每次打开时自动加载

# Linux常用命令

1. ls 命令 `ls -l` 显示更详细的内容 `ls -a` 显示包括隐藏文件在内的所有文件 `-h` 以人类可读方式显示文件大小 `-t` 按照修改时间顺序排序 `-r` 逆序显示

2. ln
   创建链接文件（类似于快捷方式） `ln -s hello.txt link.txt` 创建一个链接文件`link.txt`指向`hello.txt` -s 创建软链接（符号链接）文件
   如果不加默认创建硬链接文件，默认二者共享相同的`i节点` 相当于同一文件的不同名字
   软链接可以指向文件或者目录
   硬链接只能指向文件

3. 文件权限
   首字符表示文件类型 `- l r` `- 普通文件 l 链接文件 d 目录` 文件权限可以按照三个一组的方式来看，分别表示:
   
   - 文件所有者的权限（user）
   - 文件所属组别的权限（group）
   - 其他用户的权限（other）
   
   r w x (read write execute) 可读 可写 可执行

4. chmod
   change mode 可以用来修改权限 `chmod +x hello.txt` `chmod u+x hello.txt` 只给user添加权限
   可以使用二进制方式来添加权限 `rwx -> 421` 例如 `chmod 777`

5. touch
   用于更新文件的修改时间，如果文件不存在则创建

6. echo
   echo "hello" > hello.txt

7. pwd
   显示当前所处位置

8. `cd /` 回到根目录 `cd ~` home目录 `cd -` 上一次所在的目录

9. `cp file1.txt file2.txt`

10. `mv file3 file4`

11. `rm file4`

12. `mkdir folder` `mkdir -p folder1/folder2/folder3` 创建多级目录

13. `cp -r folder1 folder_copy`递归复制可以复制文件夹

14. `du` 查看文件和目录的大小 可以使用`tree`来以树状显示文件目录

15. `rmdir` 只能删除空文件夹
    可以选择使用 `rm -r` 来递归删除
