## Pnpm

### 节约磁盘空间并提升安装速度

![img](https://pnpm.io/zh/assets/images/cafs-illustration-7be6bd97e43ba11a031b099869321deb.jpg)

当使用 npm 或 Yarn 时，如果你有100个项目使用了某个依赖（dependency），就会有100份该依赖的副本保存在硬盘上。  而在使用 pnpm 时，依赖会被存储在内容可寻址的存储中，所以：

1. 如果你用到了某依赖项的不同版本，那么只会将有差异的文件添加到仓库。 例如，如果某个包有100个文件，而它的新版本只改变了其中1个文件。那么 `pnpm update` 时只会向存储中心额外添加1个新文件，而不会因为仅仅一个文件的改变复制整新版本包的内容。
2. 所有文件都会存储在硬盘上的某一位置。 当软件包被被安装时，包里的文件会硬链接到这一位置，而不会占用额外的磁盘空间。 这允许你跨项目地共享同一版本的依赖。

 因此，您在磁盘上节省了大量空间，这与项目和依赖项的数量成正比，并且安装速度要快得多！

### 创建非扁平化的 node_modules 文件夹

![img](https://pnpm.io/zh/assets/images/node-modules-structure-8ab301ddaed3b7530858b233f5b3be57.jpg)

使用 npm 或 Yarn Classic 安装依赖项时，所有包都被提升到模块目录的根目录。 因此，项目可以访问到未被添加进当前项目的依赖。

默认情况下，pnpm 使用软链的方式将项目的直接依赖添加进模块文件夹的根目录。 

### 过滤filter

```
pnpm <command> --filter <package_selector>
```

### pnpm run

#### 与 `npm run` 的差异

默认情况下， pnpm 不会任意运行用户定义的 `pre` 和` post` 钩子脚本（例如 `prestart`）。 这种从 npm 继承的行为导致脚本是隐式的而不是显式的，从而混淆了执行流程。 它还会导致意外执行 `pnpm serve` 和 `pnpm preserve`。

如果出于某种原因您需要 npm 的前置、后置钩子脚本，可使用`enable-pre-post-scripts` 选项。

### pnpm add

安装软件包及其依赖的任何软件包。 默认情况下，任何新软件包都安装为生产依赖项。

| Command              | 含义                          |
| -------------------- | ----------------------------- |
| `pnpm add sax`       | 保存到 `dependencies`         |
| `pnpm add -D sax`    | 保存到 `devDependencies`      |
| `pnpm add -O sax`    | 保存到 `optionalDependencies` |
| `pnpm add sax@next`  | 安装 `next` tag               |
| `pnpm add sax@3.0.0` | 安装指定版本 `3.0.0`          |

`pnpm add package-name` 默认会从 [npm registry](https://www.npmjs.com/)安装最新的 `package-name`.

如果在 workspace 中执行，该命令将首先去检查这个 worksapce 中的其他项目是否已经使用了这个指定的包。 如果是的话，就使用这个包的版本范围来进行安装。

您可以通过以下方式安装包:

- tag: `pnpm add express@nightly`
- version: `pnpm add express@1.0.0`
- version range: `pnpm add express@2 react@">=0.1.0 <0.2.0"`

#### 从 workspace 安装

需要注意的是当我们使用 [workspace](https://pnpm.io/zh/workspaces)安装依赖时, 会从已配置的源处进行安装，当然取决于是否设置了 [`link-workspace-packages`](https://pnpm.io/zh/workspaces#link-workspace-packages),以及是否使用了 [`workspace: range protocol`](https://pnpm.io/zh/workspaces#workspace-ranges-workspace).

#### 从本地安装

从本地安装的两种方法:

1. 源码文件 (`.tar`, `.tar.gz`, or `.tgz`)
2. 本地目录

示例：

```sh
pnpm add ./package.tar.gz
pnpm add ./some-directory
```

当从目录安装时，会在当前项目目录中生成一个 symlink `node_modules`, 因此这里跟执行 `pnpm link` 是一致的.

#### 从远端安装 Tar 包

参数必须是一个可访问的 URL, "http://" 或者 "https://"开头的.

示例：

```sh
pnpm add https://github.com/indexzero/forever/tarball/v0.5.6
```

#### 从 git 安装

```sh
pnpm add <git remote url>
```

通过 git clone, 从 git 作者处安装包. 可以用协议准确的指定 git 作者 For example, `pnpm add github:user/repo`

您可以通过以下方式从 Git 安装:

- 来自 master 的最新提交： `pnpm add kevva/is-positive`
- 提交: `pnpm add keva/is-positive#97edff6f525f192a3f83cea194765f769ae2678`
- 分支: `pnpm add keva/is-positive#master`
- 版本范围： `pnpm add kevva/is-positive#semver:^2.0.0`

#### 选项

##### --save-prod, -P

将指定的软件包安装为常规的 `dependencies`。

##### --save-dev, -D

将指定的 packages 安装为 `devDependencies`。

##### --save-optional, -O

将指定的 packages 安装为 `optionalDependencies`。

##### --save-exact, -E

保存的依赖会被指定为一个确切的版本, 而不是使用 pnpm 的默认 semver range operator 配置.

##### --save-peer

添加于：v3.2.0

使用 `--save-peer` 会添加一个或多个 `peerDependencies` 的 package 并安装到 dev dependencies.

##### --ignore-workspace-root-check, -W

Added in: v3.6.0

除非使用`--ignore-workspace-root-check` 或 `-W`来标记. 否则在 root workspace 包添加依赖项时会失败.

例如, `pnpm add debug -W`.

##### --global

安装全局依赖

##### --workspace

添加于：v4.4.0

仅添加在 workspace 找到的依赖项.

##### --filter <package_selector>
