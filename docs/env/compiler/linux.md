你可以使用 Linux 发行版中提供的包管理器安装编译器。

我们将以 Ubuntu 为例，介绍如何安装编译器。

### 测试 g++ 是否已经安装

在终端中，输入以下命令：

```bash
g++ --version
```

如果你的 g++ 版本低于 7，或者 `command not found`，那么你需要参考以下内容安装 g++。

### 更换源为 SJTU 源

境外的源可能会导致下载速度过慢，因此我们建议更换源为 SJTU 源，方法是将 `/etc/apt/sources.list` 中的 `archive.ubuntu.com` 全部替换为 `mirror.sjtu.edu.cn`。你可以使用以下命令完成这一操作。

```bash
sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak # 备份原有的 source.list
sudo sed -i 's/archive.ubuntu.com/mirror.sjtu.edu.cn/g' /etc/apt/sources.list # 将 archive.ubuntu.com 替换为 mirror.sjtu.edu.cn
```

请注意，如果你安装的是中文系统，上述命令中的 `archive.ubuntu.com` 可能需要替换为 `cn.archive.ubuntu.com`。你可以使用以下命令查看你的系统中的源是什么。

```bash
cat /etc/apt/sources.list | grep -v '^#'
```

### 安装 g++

```bash
sudo apt update
sudo apt install g++ -y
```

安装完成后，你应当可以测试出 g++ 已经安装好了。

### 升级

如果你的 GCC 版本比较老，以下命令可以升级所有已安装的软件。

```bash
sudo apt update
sudo apt upgrade -y
```