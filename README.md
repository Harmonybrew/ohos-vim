# ohos-vim

本项目为 OpenHarmony 平台编译了 vim，并发布预构建包。

## 获取预构建包

前往 [release 页面](https://github.com/Harmonybrew/ohos-vim/releases) 获取。

## 用法
**1\. 在鸿蒙 PC 中使用**

因系统安全规格限制等原因，暂不支持通过“解压 + 配 PATH” 的方式使用这个软件包。

你可以尝试将 tar 包打成 hnp 包再使用，详情请参考 [DevBox](https://gitcode.com/OpenHarmonyPCDeveloper/devbox) 的方案。

**2\. 在鸿蒙开发板中使用**

用 hdc 把它推到设备上，然后以“解压 + 配 PATH” 的方式使用。

示例：
```sh
hdc file send vim-9.2.0150-ohos-arm64.tar.gz /data
hdc shell

cd /data
tar -zxf vim-9.2.0150-ohos-arm64.tar.gz
export PATH=/data/vim-9.2.0150-ohos-arm64/bin:$PATH
export TERM=screen-256color

# 现在可以使用 vim 命令了
```

在 hdc shell 环境中使用 vim，需要多一个 `export TERM=screen-256color` 的步骤。因为 hdc 在连接 OpenHarmony 设备时会创建一个伪终端（pseudo-terminal），它的行为并不完全像一个标准的 xterm 或 xterm-256color 终端，而更接近于 screen 或 tmux 这类多路复用器的终端模拟方式。我们必须手动设置一个有效的 TERM 类型，hdc 发送的键码和 terminfo 定义的键码才能匹配，才不会出现按键错位。

**3\. 在 [鸿蒙容器](https://github.com/hqzing/docker-mini-openharmony) 中使用**

在容器中用 curl 下载这个软件包，然后以“解压 + 配 PATH” 的方式使用。

示例：
```sh
docker run -itd --name=ohos ghcr.io/hqzing/docker-mini-openharmony:latest
docker exec -it ohos sh

cd /root
curl -L -O https://github.com/Harmonybrew/ohos-vim/releases/download/9.2.0150/vim-9.2.0150-ohos-arm64.tar.gz
tar -zxf vim-9.2.0150-ohos-arm64.tar.gz -C /opt
export PATH=/opt/vim-9.2.0150-ohos-arm64/bin:$PATH

# 现在可以使用 vim 命令了
```

## 从源码构建

**1\. 手动构建**

这个项目使用本地编译（native compilation，也可以叫本机编译或原生编译）的做法来编译鸿蒙版 vim，而不是交叉编译。

需要在 [鸿蒙容器](https://github.com/hqzing/docker-mini-openharmony) 中运行项目里的 build.sh，以实现 vim 的本地编译。

示例：
```sh
git clone https://github.com/Harmonybrew/ohos-vim.git
cd ohos-vim

docker run \
  --rm \
  -it \
  -v "$PWD":/workdir \
  -w /workdir \
  ghcr.io/hqzing/docker-mini-openharmony:latest \
  ./build.sh
```

**2\. 使用流水线构建**

如果你熟悉 GitHub Actions，你可以直接复用项目内的工作流配置，使用 GitHub 的流水线来完成构建。

这种情况下，你使用的是 GitHub 提供的构建机，不需要自己准备构建环境。

只需要这么做，你就可以进行你的个人构建：
1. Fork 本项目，生成个人仓
2. 在个人仓的“Actions”菜单里面启用工作流
3. 在个人仓提交代码或发版本，触发流水线运行

## 常见问题


**1\. 语法高亮和按键功能异常**

vim 的正常工作依赖 terminfo 数据库，如果 vim 找不到 terminfo 数据库，就有可能产生语法高亮和按键功能异常。

本项目在构建 vim 时将默认的 terminfo 搜索路径设置为：
```text
/opt/vim-9.2.0150-ohos-arm64/share/terminfo:/data/vim-9.2.0150-ohos-arm64/share/terminfo
```

如果你在使用过程中没有将 vim 解压到 /opt 或 /data 目录下，且系统中不存在有效的 `TERMINFO` 环境变量，vim 将会搜索不到任何 terminfo 数据库，将无法正常工作。

这种情况下，你需要设置 `TERMINFO` 环境变量，强制指定 vim 去它的安装目录中搜索自带的 terminfo 数据库。

```sh
export TERMINFO=<vim安装目录的绝对路径>/share/terminfo
```

注意，这个变量不仅会影响 vim，也会影响其他依赖 terminfo 的程序，请谨慎使用。
