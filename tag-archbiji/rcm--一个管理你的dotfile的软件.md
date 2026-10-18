# rcm--一个管理你的dotfile的软件






[傻馒我们结婚吧](https://space.bilibili.com/393271893/?spm_id_from=333.788.upinfo.detail.click)【Linux下如何优雅的管理配置文件】 https://www.bilibili.com/video/BV1RJ411H7SC/?share_source=copy_web&vd_source=86d8054059c93694e41495365d81e66e





## 我的体会

### 安装

yay rcm

### 用法

- 将某些文件纳入rcm的管理  mkrc

  - 直接mkrc .zshrc会直接将这个配置文件放到rc的配置目录里面
  - mkrc -t zsh .zshrc 将某个配置文件打一个标签，这样就会在。dotfiles/里增加一个文件夹，将那个文件去掉小数点变成非隐藏文件，同时在家目录里生成一个点文件来连接这个文件。
  - 要制作一个自己的rcm的配置文件，他是.rcrc   这个里面的配置内容.

- 显示已纳入管理的文件  lsrc


### 实际运用

用git将   .dotfile管理起来,恢复配置文件的时候,要先恢复rcm本身的配置文件,再去恢复其他的配置文件.  

- 建立rcm的管理:mkrc -t rcm ~/.rcrc  
- 恢复rcm配置文件: rcup  -t rcm
- 恢复其他配置文件  rcup 

dotfiles,dotfiles  tag,rcrc中的tages代表了三种模式.

- rcup 和lsrc  rcdn  应该是默认命令,默认管理没有打标签的和打了标签并且写入rcrc文件的  
- 命令加-t,则是管理打了标签,但是没有写入rcrc文件的.

### 一个重要的问题:DOTFILES文件里面的点文件不会被链接或者复制.

### 一个重要的问题:关于参数

-C -S -U 等等这些参数都是分别单独与自己的rc命令来结合的,不会对其他rc命令产生影响.rcrc会对rcup,lsrc产生影响,而不会对mkrc产生影响.

### 一个重要的问题:关于rcrc文件

如果先将某个文件夹写入 `SYMLINK_DIRS`，然后运行不带参数的 `mkrc`，这个文件夹**不会**因为 `SYMLINK_DIRS` 的设置而在 `mkrc` 阶段被特殊处理。

###### 关键原因：两个工具的分工不同

`SYMLINK_DIRS` 是 `rcup`（以及 `lsrc`）在执行安装/同步时读取的配置，用于决定“遇到目录时是展开内部还是整体建链”。而 `mkrc` 的作用是把主目录里已有的文件**纳入** dotfiles 目录管理，它的行为由自己的命令行选项控制，并不读取 `SYMLINK_DIRS` 来决定如何“接收”一个目录。

`mkrc` 对目录的默认行为（不带 `-S`，即 `-s` 默认模式）是：如果传入的是目录，它会按照 `rcup` 的算法创建目录结构，在里面为文件分别建立链接关系。也就是说，`mkrc` 会按自己的默认逻辑处理这个文件夹，`SYMLINK_DIRS` 此时不参与决策。

###### 实际会发生什么

当你对这个文件夹运行 `mkrc`（不带参数）时，`mkrc` 会把它纳入 dotfiles 目录，并按默认方式处理。之后你再运行 `rcup`，`SYMLINK_DIRS` 才会生效：`rcup` 看到这个目录匹配 `SYMLINK_DIRS`，就会把它整体创建为符号链接，而不是展开内部结构。

所以，`SYMLINK_DIRS` 影响的是**后续 `rcup` 安装时的行为**，而不是 `mkrc` 接纳该文件夹时的行为。如果你希望 `mkrc` 本身就按“整体建链”的方式处理这个目录，需要在 `mkrc` 命令中显式加上 `-S` 选项。

## 官方文件

| Miscellaneous Information Manual 杂项信息手册 | RCM(7) RCM（7） |
| --------------------------------------------- | --------------- |
|                                               |                 |

# [NAME 名称](https://thoughtbot.github.io/rcm/rcm.7.html#NAME)

`rcm` — 

dotfile management


`rcm` --点文件管理

# [SYNOPSIS 剧情简介](https://thoughtbot.github.io/rcm/rcm.7.html#SYNOPSIS)

| `lsrc` |      |
| ------ | ---- |
|        |      |

| `mkrc` |      |
| ------ | ---- |
|        |      |

| `rcdn` |      |
| ------ | ---- |
|        |      |

| `rcup` |      |
| ------ | ---- |
|        |      |

# [DESCRIPTION 描述](https://thoughtbot.github.io/rcm/rcm.7.html#DESCRIPTION)

The rcm suite of tools is for managing dotfiles directories. This is a directory containing all the `.*rc` files in your home directory (*.zshrc*, *.vimrc*, and so on). These files have gone by many names in history, such as “rc files” because they typically end in `rc` or “dotfiles” because they begin with a period.
rcm工具套件用于管理点文件目录。这是一个包含主目录（.zshrc、.vimrc等）中所有 `.*rc` 文件的目录。这些文件在历史上有很多名字，比如“rc文件”，因为它们通常以#1结尾，或者“dotfiles”，因为它以句点开头。

This suite is useful for committing your rc files to a central repository to share, but it also scales to a more complex situation such as multiple source directories shared between computers with some host-specific or task-specific files.
此套件对于将rc文件提交到中央存储库进行共享非常有用，但它也可以扩展到更复杂的情况，例如在具有某些主机特定或任务特定文件的计算机之间共享多个源目录。

This guide serves as a tutorial motivating the suite. For a list of quick reference documentation see the [SEE ALSO](https://thoughtbot.github.io/rcm/rcm.7.html#SEE_ALSO) section below.
本指南作为激励套件的教程。有关快速参考文档的列表，请参阅下面的“另见”部分。

# [QUICK START FOR EXISTING DOTFILES DIRECTORIES 快速启动现有的DOTFILES目录](https://thoughtbot.github.io/rcm/rcm.7.html#QUICK_START_FOR_EXISTING_DOTFILES_DIRECTORIES)

This section is for those who already have an existing dotfiles directory; this directory is *~/.dotfiles*; the directory only contains rc files; and these rc filenames do not begin with a period. See the caveats below if this is not you.
本节适用于已经拥有现有dotfiles目录的人；这个目录是~/.dotfiles；该目录仅包含rc文件；这些rc文件名不以句点开头。如果这不是你，请参阅下面的注意事项。

1. Dry run with

    

   lsrc(1)

   . Look for anything unexpected in here, such as

    

   ~/.install

    

   or

    

   ~/.Makefile

   , or an empty list of dotfiles.

   
   使用lsrc（1）进行试运行。在此处查找任何意外的内容，例如~/.install或~/。Makefile，或一个空的点文件列表。

   

   `lsrc`

2. Update any symlinks with

    

   rcup(1)

   . This is likely to do nothing, since your dotfiles already exist.

   
   使用rcup（1）更新所有符号链接。这可能什么都不会做，因为您的点文件已经存在。

   

   `rcup -v`

3. When necessary, add new rc files to the dotfiles directory with

    

   mkrc(1)

   .

   
   必要时，使用mkrc（1）将新的rc文件添加到dotfiles目录中。

   

   `mkrc ~/.tigrc`

   In the other direction, you can use [rcup(1)](https://thoughtbot.github.io/rcm/rcup.1.html) to create the symlinks from *~/.dotfiles* to your home directory.
   在另一个方向上，您可以使用rcup（1）创建从~/.dotfiles到主目录的符号链接。

   

   `rcup tigrc`

## [COMMON PROBLEM: EXISTING INSTALL SCRIPTS 常见问题：现有安装脚本](https://thoughtbot.github.io/rcm/rcm.7.html#COMMON_PROBLEM:_EXISTING_INSTALL_SCRIPTS)

Many existing dotfile directories have scripts named *install* or *Makefile* in the directory. This will cause a *~/.install* or *~/.Makefile* symlink to be created in your home directory. Use an exclusion pattern to ignore these.
许多现有的点文件目录中都有名为install或Makefile的脚本。这将导致~/.install或~/。在您的主目录中创建Makefile符号链接。使用排除模式忽略这些。



```
rcup -x install -x Rakefile -x Makefile -x install.sh
```

## [COMMON PROBLEM: DOTTED FILENAMES IN DOTFILES DIRECTORY 常见问题：DOTFILES目录中的点文件名](https://thoughtbot.github.io/rcm/rcm.7.html#COMMON_PROBLEM:_DOTTED_FILENAMES_IN_DOTFILES_DIRECTORY)

A less common situation is for all the filenames in your dotfiles directory to be prefixed with a period. These files are skipped by the rcm suite, and thus would result in nothing happening. The only option in this case is to rename all the files, for example by using a shell command like the following.
一种不太常见的情况是，dotfiles目录中的所有文件名都以句点作为前缀。这些文件被rcm套件跳过，因此不会导致任何事情发生。在这种情况下，唯一的选择是重命名所有文件，例如使用以下shell命令。



```
find ~/.dotfiles -name '.*' -exec echo mv {} `echo {} | sed 's/.//'` ;
```

Note that this will break any existing symlinks. Those can be safely removed using the [rcdn(1)](https://thoughtbot.github.io/rcm/rcdn.1.html) command.
请注意，这将破坏任何现有的符号链接。可以使用rcdn（1）命令安全地删除这些内容。



```
rcdn -v
```

## COMMON PROBLEM: DOTFILES DIRECTORY NOT IN *~/.dotfiles* 常见问题：DOTFILES目录不在~/.DOTFILES中

This all assumes that your dotfiles directory is *~/.dotfiles*. If it is elsewhere and you do not want to move it you can use the `-d` DIR option to [rcup(1)](https://thoughtbot.github.io/rcm/rcup.1.html) or modify `DOTFILES_DIRS` in [rcrc(5)](https://thoughtbot.github.io/rcm/rcrc.5.html).
这一切都假设您的dotfiles目录是~/.dotfiles。如果它在别处，而你不想移动它，你可以使用 `-d` DIR选项来rcup（1）或修改rcrc（5）中的 `DOTFILES_DIRS` 。



```
rcup -d configs -v
```

## [COMMON PROBLEM: CONFIGURATION FILES/DIRECTORIES WITHOUT DOTS 常见问题：没有点的配置文件/目录](https://thoughtbot.github.io/rcm/rcm.7.html#COMMON_PROBLEM:_CONFIGURATION_FILES/DIRECTORIES_WITHOUT_DOTS)

By default, the rcm suite will prefix every file and directory it manages with a dot. If that is not desired, for example in the case of *~/bin* or *~/texmf*, you can add that file or directory to `UNDOTTED` in [rcrc(5)](https://thoughtbot.github.io/rcm/rcrc.5.html) or use the `-U` option. For example:
默认情况下，rcm套件将在其管理的每个文件和目录前添加一个点。如果不希望这样，例如在~/bin或~/texmf的情况下，您可以将该文件或目录添加到rcrc（5）中的 `UNDOTTED` ，或使用 `-U` 选项。例如：



```
mkrc -U bin
```

# [QUICK START FOR EMPTY DOTFILES DIRECTORIES 空DOTFILES目录的快速启动](https://thoughtbot.github.io/rcm/rcm.7.html#QUICK_START_FOR_EMPTY_DOTFILES_DIRECTORIES)

This section is for those who do not have an existing dotfiles directory and whose dotfiles are standard.
本节适用于那些没有现有点文件目录且点文件是标准的人。

1. Add your rc files to a dotfiles directory with

    

   mkrc(1)

   .

   
   使用mkrc（1）将rc文件添加到dotfiles目录中。

   

   `mkrc .zshrc .gitconfig .tigrc`

2. Synchronize your home directory with

    

   rcup(1)

   
   将主目录与rcup同步（1）

   

   `rcup -v`

This will give you a directory named *~/.dotfiles* with your dotfiles in it. Your original dotfiles will be symlinks into this directory. For example, *~/.zshrc* will be a symlink to *~/.dotfiles/zshrc*.
这将为您提供一个名为~/.dotfiles的目录，其中包含您的点文件。您的原始点文件将是指向此目录的符号链接。例如，~/.zshrc将是~/.dotfiles/zshrc的符号链接。

# [TAGGED DOTFILES 标记的点文件](https://thoughtbot.github.io/rcm/rcm.7.html#TAGGED_DOTFILES)

This suite becomes more powerful if you share your dotfiles directory between computers, either because multiple people share the same directory or because you have multiple computers.
如果您在计算机之间共享您的dotfiles目录，无论是因为多个人共享同一目录还是因为您有多台计算机，此套件都会变得更加强大。

If you share the dotfiles directory between people, you may end up with some irrelevant or even incorrect rc files. For example, you may have a *.zshrc* while your other contributor has a *.bashrc*. This situation can be handled with tags.
如果你在人与人之间共享dotfiles目录，你可能会得到一些不相关甚至不正确的rc文件。例如，您可能有一个.zshrc，而您的其他贡献者有一个.bashrc。这种情况可以用标签来处理。

1. A tag is a directory under the dotfiles directory that starts with the letters

    

   ```
   tag-
   ```

   . We can handle the competing shell example by making a

    

   tag-zsh

    

   directory and moving the

    

   .zshrc

    

   file into it using

    

   mkrc(1)

    

   and passing the

    

   ```
   -t
   ```

    

   option.

   
   标签是dotfiles目录下以字母 `tag-` 开头的目录。我们可以通过创建一个标签zsh目录，使用mkrc（1）将.zshrc文件移动到其中并传递 `-t` 选项来处理竞争的shell示例。

   

   `mkrc -t zsh .zshrc`

2. When updating with

    

   rcup(1)

    

   you can pass the

    

   ```
   -t
   ```

    

   option to include the tags you want. This can also be set in the

    

   rcrc(5)

    

   configuration file with the

    

   ```
   TAGS
   ```

    

   variable.

   
   使用rcup（1）更新时，可以传递 `-t` 选项以包含所需的标签。这也可以在rcrc（5）配置文件中使用 `TAGS` 变量进行设置。

   

   `rcup -t zsh`

# [MULTIPLE DOTFILE DIRECTORIES 多个点文件目录](https://thoughtbot.github.io/rcm/rcm.7.html#MULTIPLE_DOTFILE_DIRECTORIES)

Another common situation is combining multiple dotfiles directories that people have shared with you. For this we have the `-d` flag or the `DOTFILES_DIRS` option in *.rcrc*.
另一种常见的情况是合并人们与您共享的多个点文件目录。为此，我们在.rcrc中有 `-d` 标志或 `DOTFILES_DIRS` 选项。

The following rcup invocation will go in sequence through the three dotfiles directories, updating any symlinks as needed. Any overlapping rc files will use the first result, not the last; that is, *.dotfiles/vimrc* will take precedence over *marriage-dotfiles/vimrc*.
以下rcup调用将按顺序遍历三个dotfiles目录，根据需要更新任何符号链接。任何重叠的rc文件都将使用第一个结果，而不是最后一个结果；也就是说，.dotfiles/vimrc将优先于marriage.dotfiles/vimrc。



```
rcup -d .dotfiles -d marriage-dotfiles -d thoughtbot-dotfiles
```

An exclusion pattern can be tied to a specific dotfiles directory.
排除模式可以绑定到特定的dotfiles目录。



```
rcup -d .dotfiles -d work-dotfiles -x 'work-dotfiles:powrc'
```

# [HOST-SPECIFIC DOTFILES 特定于主机的点文件](https://thoughtbot.github.io/rcm/rcm.7.html#HOST_SPECIFIC_DOTFILES)

You can also mark host-specific files. This will go by the hostname. The [rcrc(5)](https://thoughtbot.github.io/rcm/rcrc.5.html) configuration file is a popular candidate for a host-specific file, since the tags and dotfile directories listed in there are often specific to a single machine.
您还可以标记特定于主机的文件。这将按主机名进行。rcrc（5）配置文件是特定于主机的文件的常用候选文件，因为其中列出的标签和点文件目录通常特定于一台机器。



```
mkrc -o .rcrc
```

If your hostname is difficult to compute, or you otherwise want to use a different hostname, you can use the `-B` flag.
如果您的主机名难以计算，或者您想使用其他主机名，可以使用 `-B` 标志。



```
mkrc -B eggplant .rcrc
```

macOS users should see the [BUGS](https://thoughtbot.github.io/rcm/rcm.7.html#BUGS) section for more details.
macOS用户应该查看BUGS部分以了解更多详细信息。

# [STANDALONE INSTALLATION SCRIPT 独立安装脚本](https://thoughtbot.github.io/rcm/rcm.7.html#STANDALONE_INSTALLATION_SCRIPT)

The [rcup(1)](https://thoughtbot.github.io/rcm/rcup.1.html) tool can be used to generate a portable shell script. Instead of running a command such as [ln(1)](https://thoughtbot.github.io/rcm/ln.1.html) or [rm(1)](https://thoughtbot.github.io/rcm/rm.1.html), it will print the command to `stdout`. This is controlled with the `-g` flag. Note that this will generate a script to create an exact replica of the synchronization, including tags, host-specific files, and dotfiles directories.
rcup（1）工具可用于生成可移植的shell脚本。它不会运行ln（1）或rm（1）等命令，而是将命令打印到 `stdout` 。这是用 `-g` 标志控制的。请注意，这将生成一个脚本来创建同步的精确副本，包括标签、特定于主机的文件和点文件目录。



```
env RCRC=/dev/null rcup -B 0 -g > install.sh
```

Using the above command, you can now run `install.sh` to install (or re-install) your rc files. The `install.sh` script can be stored in your dotfiles directory, copied between computers, and so on.
使用上述命令，您现在可以运行 `install.sh` 来安装（或重新安装）您的rc文件。 `install.sh` 脚本可以存储在您的dotfiles目录中，在计算机之间复制，等等。

# [RATIONALE 基本原理](https://thoughtbot.github.io/rcm/rcm.7.html#RATIONALE)

The rcm suite was built as an abstraction over the shell, Ruby, Python, and make scripts people were writing and sharing. It is intended to run on any unix system and support the range from simple to complex dotfile directories.
rcm套件是作为人们编写和共享的shell、Ruby、Python和make脚本的抽象而构建的。它旨在在任何unix系统上运行，并支持从简单到复杂的点文件目录。

As such, this suite is useful as a common base. Through this we can share tools and develop this further as a first-class entity. It is also our hope that a common set of tools will encourage others to share their dotfiles, too.
因此，这个套件可以作为一个通用的基础。通过这种方式，我们可以共享工具，并将其进一步发展为一流的实体。我们还希望，一套通用的工具也能鼓励其他人分享他们的点文件。

# [FILES 文件](https://thoughtbot.github.io/rcm/rcm.7.html#FILES)

*~/.dotfiles* *~/.rcrc* ~/.dotfiles~/.rcrc

# [SEE ALSO 参见](https://thoughtbot.github.io/rcm/rcm.7.html#SEE_ALSO)

[lsrc(1)](https://thoughtbot.github.io/rcm/lsrc.1.html), [mkrc(1)](https://thoughtbot.github.io/rcm/mkrc.1.html), [rcdn(1)](https://thoughtbot.github.io/rcm/rcdn.1.html), [rcup(1)](https://thoughtbot.github.io/rcm/rcup.1.html), [rcrc(5)](https://thoughtbot.github.io/rcm/rcrc.5.html)
lsrc（1）、mkrc（1，rcdn（1），rcup（1）和rcrc（5）

# [AUTHORS 作者](https://thoughtbot.github.io/rcm/rcm.7.html#AUTHORS)

`rcm` is maintained by Mike Burns <[mburns@thoughtbot.com](mailto:mburns@thoughtbot.com)> and [thoughtbot](http://thoughtbot.com/)
`rcm` 由Mike Burns维护mburns@thoughtbot.com>和thoughtbot

# [BUGS 虫子](https://thoughtbot.github.io/rcm/rcm.7.html#BUGS)

For macOS systems, we strongly encourage the use of the HOSTNAME variable in your [rcrc(5)](https://thoughtbot.github.io/rcm/rcrc.5.html). We use the [hostname(1)](https://thoughtbot.github.io/rcm/hostname.1.html) program to determine the unique identifier for the host. This program is not specified by POSIX and can vary by system. On macOS, the hostname is unpredictable, and can even change as part of the DHCP handshake.
对于macOS系统，我们强烈建议在rcrc（5）中使用HOSTNAME变量。我们使用hostname（1）程序来确定主机的唯一标识符。此程序不是由POSIX指定的，并且可能因系统而异。在macOS上，主机名是不可预测的，甚至可以在DHCP握手过程中更改。

# [CONTRIBUTORS 贡献者](https://thoughtbot.github.io/rcm/rcm.7.html#CONTRIBUTORS)

Alan Yee <[alyee@ucsd.edu](mailto:alyee@ucsd.edu)>
易alyee@ucsd.edu>
Alexander Goldstein <[alexg@alexland.org](mailto:alexg@alexland.org)>
亚历山大·戈尔茨坦
Andrei Dziahel <[develop7@develop7.info](mailto:develop7@develop7.info)>  
Anton Ilin <[anton@ilin.dn.ua](mailto:anton@ilin.dn.ua)>  
Ben Stephens <[BKStephens@outlook.com](mailto:BKStephens@outlook.com)>  
Ben Turrubiates <[ben@turrubiat.es](mailto:ben@turrubiat.es)>  
Blake Williams <[blake@blakewilliams.me](mailto:blake@blakewilliams.me)>  
Caleb Land <[caleb@land.fm](mailto:caleb@land.fm)>  
Carl van Tonder <[carl@supervacuo.com](mailto:carl@supervacuo.com)>  
Casey Rodarmor <[casey@rodarmor.com](mailto:casey@rodarmor.com)>  
Christian Höltje <[docwhat@gerf.org](mailto:docwhat@gerf.org)>  
Christian Höltje <[docwhat@gerf.org](mailto:docwhat@gerf.org)>  
Christopher Koch <[ckoch@cs.nmt.edu](mailto:ckoch@cs.nmt.edu)>  
Dan Croak <[dan@thoughtbot.com](mailto:dan@thoughtbot.com)>  
Daniel Watson <[dwatson@thig.com](mailto:dwatson@thig.com)>  
Darcy Parker <[darcyparker@gmail.com](mailto:darcyparker@gmail.com)>  
David Alexander <[davidpaulalexander@gmail.com](mailto:davidpaulalexander@gmail.com)>  
Devraj Mehta <[devm33@gmail.com](mailto:devm33@gmail.com)>  
Edd Salkield <[edd@salkield.uk](mailto:edd@salkield.uk)>  
Eric Collins <[eric@tabfugni.cc](mailto:eric@tabfugni.cc)>  
Florian Tham <[fgtham@gmail.com](mailto:fgtham@gmail.com)>  
George Brocklehurst <[george@thoughtbot.com](mailto:george@thoughtbot.com)>  
Graham Bennett <[graham@simulcra.org](mailto:graham@simulcra.org)>  
Jarkko Kniivilä <[jkniiv@gmail.com](mailto:jkniiv@gmail.com)>  
Jason Daniel Augustine Gilliland <[jdagilliland@gmail.com](mailto:jdagilliland@gmail.com)>  
Javier López <[linux.kitten@gmail.com](mailto:linux.kitten@gmail.com)>  
Joe Ferris <[jferris@thoughtbot.com](mailto:jferris@thoughtbot.com)>  
John Axel Eriksson <[john@insane.se](mailto:john@insane.se)>  
Jordan Eldredge <[jordaneldredge@gmail.com](mailto:jordaneldredge@gmail.com)>  
Leonardo Brondani Schenkel <[leonardo@schenkel.net](mailto:leonardo@schenkel.net)>  
Martin Frost <[frost@ceri.se](mailto:frost@ceri.se)>  
Mat M <[matm@gmx.fr](mailto:matm@gmx.fr)>  
Matthew Horan <[matt@matthoran.com](mailto:matt@matthoran.com)>  
Melissa Xie <[melissa@thoughtbot.com](mailto:melissa@thoughtbot.com)>  
Michael Reed <[supertron421@gmail.com](mailto:supertron421@gmail.com)>  
Mike Burns <[mburns@thoughtbot.com](mailto:mburns@thoughtbot.com)>  
Mike Burns and Eric Collins <[mburns@thoughtbot.com](mailto:mburns@thoughtbot.com)>  
Nick Novitski <[github@nicknovitski.com](mailto:github@nicknovitski.com)>  
Pablo Olmos de Aguilera Corradini <[pablo@glatelier.org](mailto:pablo@glatelier.org)>  
Patrick Brisbin <[pat@thoughtbot.com](mailto:pat@thoughtbot.com)>  
Rafael Santos <[formigarafa@gmail.com](mailto:formigarafa@gmail.com)>  
Rebecca Meritz <[rebecca@meritz.com](mailto:rebecca@meritz.com)>  
Roberto Pedroso <[roberto@rpedroso.com](mailto:roberto@rpedroso.com)>  
Scott Stevenson <[scott@stevenson.io](mailto:scott@stevenson.io)>  
Stephen <[stephengroat@users.noreply.github.com](mailto:stephengroat@users.noreply.github.com)>  
Teo Ljungberg <[teo@teoljungberg.com](mailto:teo@teoljungberg.com)>  
Tyson Gach <[tyson@tysongach.com](mailto:tyson@tysongach.com)>  
Vlad GURDIGA <[gurdiga@gmail.com](mailto:gurdiga@gmail.com)>  
Yota Toyama <[raviqqe@gmail.com](mailto:raviqqe@gmail.com)>  
Zach Latta <[zach@zachlatta.com](mailto:zach@zachlatta.com)>  
ivan tkachenko <[me@ratijas.tk](mailto:me@ratijas.tk)>  
kajisha <[kajisha@gmail.com](mailto:kajisha@gmail.com)>  
maxice8 <[thinkabit.ukim@gmail.com](mailto:thinkabit.ukim@gmail.com)>  
subpop <[subpop@users.noreply.github.com](mailto:subpop@users.noreply.github.com)>  
wplatter-cb <[39812934+wplatter-cb@users.noreply.github.com](mailto:39812934+wplatter-cb@users.noreply.github.com)>  

| July 28, 2013 2013年7月28日 | Debian |
| --------------------------- | ------ |
|                             |        |