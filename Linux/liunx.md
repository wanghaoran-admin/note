# 第一张 liunx命令

### 1.基本 Linux 命令

#### **cd**：改变目录

```shell
cd /path/to/directory  # 切换到指定目录
cd ..                  # 返回到上一级目录
cd ~                   # 进入到用户的主目录
```

#### **pwd**：显示当前所在的目录路径

```shell
pwd
```

#### **ls**：列出目录内容

```shell
ls       # 列出当前目录的文件和文件夹
ls -l    # 以详细格式列出文件和文件夹
ls -a    # 列出包括隐藏文件在内的所有文件和文件夹
```

#### **touch**：新建一个文件

```shell
touch file_name  # 创建一个新的空文件
```

#### **rm**：删除文件

```shell
rm file_name        # 删除指定文件
rm -r directory_name  # 删除指定目录及其内容
rm -rf /           # 强制删除根目录下的所有文件和文件夹（极其危险，切勿尝试！）
```

#### **mkdir**：新建一个目录

```shell
mkdir directory_name  # 创建一个新目录
```

#### **mv**：移动或重命名文件

```shell
mv source_file target_file  # 移动或重命名文件
mv source_file target_directory/  # 移动文件到指定目录
```

#### **reset**：重新初始化终端/清屏

```shell
reset
```

#### **clear**：清屏

```shell
clear
```

#### **history**：查看命令历史

```shell
history
```

#### **help**：帮助

```shell
help  # 显示内置命令的帮助信息
```

#### **exit**：退出

```shell
exit
```

#### **#**：表示注释

```shell
# 这是一条注释，shell 不会执行这行内容
```







#### **注意事项**

- `rm -rf /`：**极其危险**的命令，会删除根目录下的所有文件和文件夹，切勿尝试。





### 2.vim命令

#### 基本模式

1. **普通模式（Normal Mode）**：用于浏览和操作文件内容。按 `Esc` 键进入普通模式。
2. **插入模式（Insert Mode）**：用于编辑文本。按 `i`、`I`、`a`、`A`、`o`、`O` 等进入插入模式。按 `Esc` 返回普通模式。
3. **视觉模式（Visual Mode）**：用于选择文本。按 `v` 进入字符选择模式，按 `V` 进入行选择模式，按 `Ctrl-v` 进入块选择模式。按 `Esc` 返回普通模式。
4. **命令行模式（Command-line Mode）**：用于执行命令。按 `:` 进入命令行模式。





#### :%s/hello/test/g  替换

`%` 表示对整个文件进行操作。

`s/hello/test/g` 表示将 `hello` 替换为 `test`，`g` 表示全局替换。



#### :g/hello php/d  删除

输入 `:g/hello php/d`，然后按 `Enter`。这会删除所有包含 `hello php` 的行。

