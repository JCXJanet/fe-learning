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

Version: 6.x

### .npmrc

pnpm 从命令行、环境变量和 `.npmrc` 文件中获取其配置。

`pnpm config` 命令可用于更新和编辑 用户和全局 `.npmrc` 文件的内容。

四个相关文件分别为：

- 每个项目的配置文件（`/path/to/my/project/.npmrc`）
- 每个工作区的配置文件（包含 `pnpm-workspace.yaml` 文件的目录）
- 每位用户的配置文件（ `~/.npmrc` ）
- 全局配置文件（ `/etc/npmrc` ）

所有 `.npmrc` 文件都遵循 [INI-formatted](https://en.wikipedia.org/wiki/INI_file) 列表，包含 `key = value` 参数。

#### 依赖提升设置

##### hoist

添加于：v4.0.0

- 默认值： **true**
- 类型： **boolean**

当 `true`，所有依赖项都被提升到 `node_modules/.pnpm`。 这使得 `node_modules`所有包都可以访问 未列出的依赖项。

##### hoist-pattern

添加于：v4.0.0

- 默认值： **['\*']**
- 类型： **string[]**

告诉 pnpm 哪些包应该被提升到 `node_modules/.pnpm`。 在默认情况下，所有的包都是被提升的，但是如果你知道只有一些有缺陷的包有幻影依赖关系， 您可以使用此选项来只提升有幻影依赖关系的包(推荐)。

例如：

```sh
hoist-pattern[]=*eslint*
hoist-pattern[]=*babel*
```

##### public-hoist-pattern

添加于：v5.2.0

- 默认值： **['\*types\*', '\*eslint\*', '@prettier/plugin-\*', '\*prettier-plugin-\*']**
- 类型： **string[]**

不同于 `hoist-pattern` 会把依赖提升到一个虚拟存储中的隐藏的模块目录中，`public-hoist-pattern` 将匹配的依赖提升至根模块目录中。 提升至根模块目录中意味着应用代码可以访问到幻影依赖，即使他们对解析策略做了不当的修改。

当处理一些有缺陷的可插拔工具不能正确解析依赖关系时，此设置很有用。

例如：

```sh
public-hoist-pattern[]=*plugin*
```

注意：设置 `shamefully-hoist` 为 `true` 与设置 `public-hoist-pattern` 为 `*` 是一样的。

##### shamefully-hoist

添加于：v1.34.0 为 `shamefully-flatten`，在 v4.0.0 中重命名

- 默认值： **false**
- 类型：**Boolean**

默认情况下，pnpm 创建一个半严格的 `node_modules`，这意味着依赖项可以访问未声明的依赖项，但 `node_modules` 之外的模块不行。 通过这种布局，生态系统中的大多数的包都可以正常工作。 但是，如果某些工具仅在提升的依赖项位于根目录的 `node_modules` 时才有效，您可以将其设置为 `true` 来为您提升它们。

#### Node 模块设置

##### store-dir

添加于: v4.2.0 作为 `store`

- 默认值：**~/.pnpm-store**
- 类型：**path**

所有包被保存在磁盘上的位置。

该存储应始终位于进行安装的同一磁盘上，因此每个磁盘将有一个存储。 如果磁盘上存在主目录，存储则会被创建在 `<home dir>/.pnpm-store`。 如果磁盘上没有主目录，那么将在文件系统的根目录中创建该存储。 例如，如果安装发生在挂载在 `/mnt` 的文件系统上，那么存储将在 `/mnt/.pnpm-store` 处创建。 Windows 系统上也是如此。

可以从不同的磁盘设置同一个存储，但在这种情况下，pnpm 将复制包而不是硬链接它们，因为硬链接只能发生在同一文件系统上。

##### modules-dir

添加于：v4.14.0

- 默认值：**node_modules**
- 类型：**path**

将安装依赖项的目录（而不是 `node_modules`）。

##### node-linker

添加于：v5.9.0

- 默认值：**isolated**
- 类型: **isolated**, **hoisted**, **pnp**

定义应该使用什么链接器来安装 Node 包。

- **isolated** - 依赖项从虚拟存储 `node_modules/.pnpm` 中建立符号链接

- hoisted

  \- 创建一个没有符号链接的扁平的

  ```
  node_modules
  ```

  。 与 npm 或 Yarn Classic 创建

  ```
  node_modules 
  ```

  一致。 使用此设置的正当理由：

  1. 您的工具不适用于符号链接。 React Native 项目很可能只有在你使用提升的 `node_modules` 才能工作。
  2. 您的项目会被部署到 serverless 服务提供商。 一些 serverless 提供商（例如 AWS Lambda）不支持符号链接。 此问题的另一种解决方案是在部署之前打包您的应用程序。
  3. 如果你想用 [`"bundledDependencies"`](https://docs.npmjs.com/cli/v8/configuring-npm/package-json#bundleddependencies) 发布你的包。
  4. 如果您使用 [--preserve-symlinks](https://nodejs.org/api/cli.html#cli_preserve_symlinks) 标志运行 Node.js。

- **pnp** - 没有 `node_modules`。 Plug'n'Play 是一种 [Yarn Berry 使用的](https://yarnpkg.com/features/pnp)创新的 Node 依赖策略。 当使用 `pnp` 作为您的链接器时，建议还将 `symlink` 设置为 `false`。

##### symlink

添加于：v5.9.0

- 默认值： **true**
- 类型：**Boolean**

当 `symlink` 设置为 `false` 时，pnpm 创建一个没有任何符号链接的虚拟存储目录。 与 `node-linker=pnp` 一起是一个有用的设置。

##### enable-modules-dir

添加于：v5.15.0

- 默认值： **false**
- 类型：**Boolean**

当 `false` 时，pnpm 不会将任何文件写入模块目录（`node_modules`）。 这对于在用户空间的文件系统 (FUSE) 中挂载模块目录时很有用。 有一个实验性 CLI 允许您在 FUSE 中挂载模块目录：[@pnpm/mount-modules](https://www.npmjs.com/package/@pnpm/mount-modules)。

##### virtual-store-dir

添加于：v4.1.0

- 默认值：**node_modules/.pnpm**
- 类型：**path**

带有指向存储的链接的目录。 所有直接和间接依赖项都链接到此目录中。

这是一个有用的设置，可以解决 Windows 上长路径的问题。 如果您有一些路径很长的依赖项，您可以选择将虚拟存储放在驱动器的根目录中（例如 `C:\my-project-store`）。

或者您可以将虚拟存储设置为 `.pnpm` 并将其添加到 `.gitignore`。 这将使堆栈跟踪更清晰，因为依赖项的路径将会提高一个目录层级。

**注意：** 虚拟存储不能在多个项目之间共享。 每个项目都应该有自己的虚拟存储（除了在工作空间中被共享的根目录）。

##### package-import-method

添加于: v1.25.0

- 默认值：**auto**
- 类型：**auto**, **hardlink**, **copy**, **clone**

控制从存储导入包的方式。

- **auto** - 尝试从存储克隆包。 如果不支持克隆则从存储硬链接包。 如果克隆和链接都不支持，则回退到复制
- **hardlink** - 从存储硬链接包
- **copy** - 从存储复制包
- **clone** - 从商店克隆（也称为 copy-on-write 或参考链接）包

##### modules-cache-max-age

添加于：v6.0.0

- 默认值： **10080** （以分钟为单位的 7 天）
- 类型：**number**

孤立包应该从模块目录中被删除的时间（以分钟为单位）。 pnpm 在模块目录中保存了一个包的缓存。 切换分支或降级依赖项时，这会提高安装速度。

#### Lockfile 设置

##### lockfile

添加于：v1.32.0 作为 `shrinkwrap`

- 默认值： **true**
- 类型：**Boolean**

当设置为 `false` 时，pnpm 不会读取或生成 `pnpm-lock.yaml` 文件。

##### prefer-frozen-lockfile

添加于：v1.37.1 作为 `prefer-frozen-shrinkwrap`

- 默认值：**true**（自 v1.38.0 起）
- 类型：**Boolean**

当设置为 `true` 并且存在 `pnpm-lock.yaml` 满足 `package.json` 中的依赖关系时，执行无头安装。 因此无头安装会直接跳过解析依赖项，并且不会修改lockfile

#### 注册源 & 身份验证设置

##### registry

- 默认值：**https://registry.npmjs.org/**
- 类型：**url**

npm包注册源地址 (包括末尾斜杠) 。

```
<scope>:registry
```

用于指定包的注册源范围 例如，设置 `@babel:registry=https://example.com/packages/npm/` 将在您使用 `pnpm add @babel/core` 或任何 `@babel` 范围内的包时，该包将强制从 `https://example.com/packages/npm` 获取而不是默认注册源。

```
<URL>:_authToken
```

访问指定注册源时要使用的身份验证承载令牌。 例如：

```sh
//registry.npmjs.org/:_authToken=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx 
```

您也可以使用环境变量。 例如：

```undefined
//registry.npmjs.org/:_authToken=${NPM_TOKEN}
```

##### `<URL>:tokenHelper`

添加于：v6.25.0

令牌助手是输出身份验证令牌的可执行文件。 这可以用于 authToken 不是常量值而是定期刷新值的情况，其中脚本或其他工具可以使用现有的刷新令牌来获取新的访问令牌。

助手路径的配置必须是绝对路径，没有参数。 为了安全起见，只允许在用户 `.npmrc`设置此值。 否则，项目可以在项目的本地 `.npmrc` 放置一个值并运行任意可执行文件。

为默认注册表设置令牌助手：

```undefined
tokenHelper=/home/ivan/token-generator
```

为指定注册源设置令牌助手：

```undefined
//registry.corp.com:tokenHelper=/home/ivan/token-generator
```

##### `<URL>:always-auth`

- 默认值： **false**
- 类型：**Boolean**

在访问指定的注册源时，强制 pnpm 验证身份（即使是 GET 请求）。 例如：

```sh
@babel:registry=https://gitlab.com/api/v4/packages/npm/
//gitlab.com/api/v4/packages/npm/:always-auth=true

registry=https://registry.npmjs.org/
//registry.npmjs.org/:always-auth=true
```

#### 请求设置

##### ca

- 默认值：**npm CA 证书**
- 类型：字符串、数组或 null

设置通过 SSL 连接源服务器时所使用的 CA 证书 值应采用 PEM 格式（AKA “Base-64 encoded X.509 (.CER)”）。 例如：

```sh
ca="-----BEGIN CERTIFICATE-----\nXXXX\nXXXX\n-----END CERTIFICATE-----"
```

设置为 null 时仅允许已知注册商，若指定 CA 证书将只信任指定的证书颁发机构。

通过指定 CA 数组来信任多个 CA：

```sh
ca[]="..."
ca[]="..."
```

See also the `strict-ssl` config.

##### cafile

- 默认值：**null**
- 类型：**path**

包含一个或多个证书颁发机构签名证书的文件路径。 类似于 `ca` 设置，但允许将一个或多个 CA 信息存储在文件中，而不是通过命令行指定。

##### cert

- 默认值：**null**
- 类型：**String**

A client certificate to pass when accessing the registry. Values should be in PEM format (AKA "Base-64 encoded X.509 (.CER)"). 例如：

```test
cert="-----BEGIN CERTIFICATE-----\nXXXX\nXXXX\n-----END CERTIFICATE-----"
```

它不是证书文件的路径（也没有 `certfile` 选项）。

##### https-proxy

- 默认值：**null**
- 类型：**url**

用于传出 HTTPS 请求的代理。 如果设置了 `HTTPS_PROXY`、 `https_proxy`、`HTTP_PROXY` 或 `http_proxy` 环境变量，将使用环境变量的值。

##### key

- 默认值：**null**
- 类型：**String**

客户端访问注册源时要传递的密钥。 值应采用 PEM 格式（AKA “Base-64 encoded X.509 (.CER)”）。 例如：

```sh
key="-----BEGIN PRIVATE KEY-----\nXXXX\nXXXX\n-----END PRIVATE KEY-----"
```

它不是密钥文件的路径（也没有 `keyfile` 选项）。

##### local-address

- 默认值：**undefined**
- 类型：**IP 地址**

The IP address of the local interface to use when making connections to the npm registry.

##### proxy

- 默认值：**null**
- 类型：**url**

A proxy to use for outgoing http requests. If the HTTP_PROXY or http_proxy environment variables are set, proxy settings will be honored by the underlying request library.

##### maxsockets

添加于：v6.18.0

- 默认值：**network-concurrency x 3**
- 类型：**Number**

每个源使用的最大连接数（协议/主机/端口组合）。

##### noproxy

添加于：v5.18.8

- 默认值：**null**
- 类型：**String**

A comma-separated string of domain extensions that a proxy should not be used for.

##### strict-ssl

- 默认值： **true**
- 类型：**Boolean**

Whether or not to do SSL key validation when making requests to the registry via HTTPS.

See also the `ca` option.

##### network-concurrency

- 默认值：**16**
- 类型：**Number**

Controls the maximum number of HTTP(S) requests to process simultaneously.

##### fetch-retries

- 默认值：**2**
- 类型：**Number**

How many times to retry if pnpm fails to fetch from the registry.

##### fetch-retry-factor

- 默认值：**10**
- 类型：**Number**

The exponential factor for retry backoff.

##### fetch-retry-mintimeout

- 默认值：**10000（10 秒）**
- 类型：**Number**

The minimum (base) timeout for retrying requests.

##### fetch-retry-maxtimeout

- 默认值：**60000（1 分钟）**
- 类型：**Number**

The maximum fallback timeout to ensure the retry factor does not make requests too long.

##### fetch-timeout

添加于：v6.2.0

- 默认值：**60000（1 分钟）**
- 类型：**Number**

等待 HTTP 请求完成的最长时间。

#### Peer Dependency 设置

##### auto-install-peers

添加于：v6.26.0

- 默认值： **false**
- 类型：**Boolean**

当设置为 `true` 时，`pnpm add <pkg>` 将会自动安装任何缺少的 peer dependencies 作为 dev dependencies。

##### strict-peer-dependencies

添加于：v2.15.0

- 默认值： **false**
- 类型：**Boolean**

If this is enabled, commands will fail if there is a missing or invalid peer dependency in the tree.

#### 命令行设置

##### [no-]color

添加于：v4.1.0

- 默认值：**auto**
- 类型：**auto**, **always**, **never**

设置输出的颜色.

- **auto** - output uses colors when the standard output is a terminal or TTY.
- **always** - ignore the difference between terminals and pipes. You’ll rarely want this; in most scenarios, if you want color codes in your redirected output, you can instead pass a `--color` flag to the pnpm command to force it to use color codes. The default setting is almost always what you’ll want.
- **never** - 关闭颜色. This is the setting used by `--no-color`.

##### loglevel

添加于：v4.13.0

- 默认值：**info**
- 类型：**debug**, **info**, **warn**, **error**

Any logs at or higher than the given level will be shown. You can instead pass `--silent` to turn off all output logs.

##### use-beta-cli

Added in: v3.6.0

- 默认值： **false**
- 类型：**Boolean**

Experimental option that enables beta features of the CLI. This means that you may get some changes to the CLI functionality that are breaking changes, or potentially bugs.

##### recursive-install

添加于：v5.4.0

- 默认值： **true**
- 类型：**Boolean**

If this is enabled, the primary behaviour of `pnpm install` becomes that of `pnpm install -r`, meaning the install is performed on all workspace or subdirectory packages.

Else, `pnpm install` will exclusively build the package in the current directory.

##### engine-strict

- 默认值： **false**
- 类型：**Boolean**

If this is enabled, pnpm will not install any package that claims to not be compatible with the current Node version.

Regardless of this configuration, installation will always fail if a project (not a dependency) specifies an incompatible version in its `engines` field.

##### npm-path

添加于：v4.8.0

- 类型：**path**

The location of the npm binary that pnpm uses for some actions, like publishing.

#### 构建设置

##### child-concurrency

- 默认值：**5**
- 类型：**Number**

The maximum number of child processes to allocate simultaneously to build node_modules.

##### side-effects-cache

添加于：v1.31.0

- 默认值： **false**
- 类型：**Boolean**

Use and cache the results of (pre/post)install hooks.

##### side-effects-cache-readonly

添加于：v1.31.0

- 默认值： **false**
- 类型：**Boolean**

Only use the side effects cache if present, do not create it for new packages.

##### unsafe-perm

- 默认值：如果以 root 身份运行则为 **false** ，否则为 **true**
- 类型：**Boolean**

Set to true to enable UID/GID switching when running package scripts. If set explicitly to false, then installing as a non-root user will fail.

#### Node.js 设置

##### use-node-version

添加于：v6.5.0

- 默认值：**undefined**
- 类型：**semver**

指定应用于项目运行时的确切 Node.js 版本。 pnpm 将自动安装指定版本的 Node.js 并将其用于执行 `pnpm run` 命令或 `pnpm node` 命令。

##### `node-mirror:<releaseDir>`

添加于：v6.24.0

- 默认值： **`https://nodejs.org/download/<releaseDir>/`**
- 类型：**URL**

设置用于下载 Node.js 的基本 URL。 此设置的 `<releaseDir>` 部分可以是 https://nodejs.org/download: `release`, `rc`, `nightly`, `v8-canary` 等中的任何目录。

以下是如何配置 pnpm 从中国的 Node.js 镜像下载 Node.js：

```undefined
node-mirror:release=https://npmmirror.com/mirrors/node/
node-mirror:rc=https://npmmirror.com/mirrors/node-rc/
node-mirror:nightly=https://npmmirror.com/mirrors/node-nightly/
```

#### 其它设置

##### use-running-store-server

添加于：v2.5.0

- 默认值： **false**
- 类型：**Boolean**

Only allows installation with a store server. If no store server is running, installation will fail.

##### save-prefix

- 默认值：**'^'**
- 类型：**String**

Configure how versions of packages installed to a `package.json` file get prefixed.

For example, if a package has version `1.2.3`, by default its version is set to `^1.2.3` which allows minor upgrades for that package, but after `pnpm config set save-prefix='~'` it would be set to `~1.2.3` which only allows patch upgrades.

This setting is ignored when the added package has a range specified. For instance, `pnpm add foo@2` will set the version of `foo` in `package.json` to `2`, regardless of the value of `save-prefix`.

##### tag

- 默认值：**latest**
- 类型：**String**

如果您 `pnpm add` 一个包并且您没有提供特定版本，那么它将安装这个包在设置中的标记下注册的版本。

This also sets the tag that is added to the `package@version` specified by the `pnpm tag` command if no explicit tag is given.

##### global-dir

添加于：v4.2.0

- 默认值：**<path to node>/pnpm-global**
- 类型：**path**

Specify a custom directory to store global packages.

##### global-bin-dir

添加于：v6.15.0

允许设置全局安装包的 bin 文件的目标目录。

##### state-dir

添加于：v6.10.0

- 默认值：**$XDG_STATE_HOME/pnpm**
- 类型：**path**

pnpm 创建的当前仅由更新检查器使用的 `pnpm-state.json` 文件的目录。

##### cache-dir

添加于：v6.10.0

- 默认值：**$XDG_CACHE_HOME/pnpm**
- 类型：**path**

包元数据缓存的位置。

##### use-stderr

添加于：v6.5.0

- 默认值： **false**
- 类型：**Boolean**

当为 true 时，所有输出都写入 stderr。

##### extend-node-path

添加于：v6.16.0

- 默认值： **true**
- 类型：**Boolean**

当 `false`时，命令 shims 中不会设置 `NODE_PATH` 环境变量。 如果在运行命令时遇到如下错误，建议将此设置设置为 `false`：

> The input line is too long. The syntax of the command is incorrect.

##### update-notifier

添加于：v6.29.0

- 默认值： **true**
- 类型：**Boolean**

设置为 `false` 以便在使用较旧版本的 pnpm 时关闭更新通知。

### 工作空间

pnpm 内置了对单一存储库（也称为多包存储库、 多项目存储库或单体存储库）的支持。 您可以创建一个工作区以将多个项目合并到一个存储库中。

工作空间的根目录中必须有 [`pnpm-workspace.yaml`](https://pnpm.io/zh/pnpm-workspace_yaml) 文件。 工作空间的根目录中也可能有 [`.npmrc`](https://pnpm.io/zh/npmrc)。

#### 工作空间协议 (workspace:)

添加于： v3.7.0.

默认情况下，如果可用的 `packages` 与已声明的可用范围相匹配，pnpm 将从工作区链接这些 `packages`。 例如，如果 `bar` 中有 `"foo"："^1.0.0"` 的这个依赖项，则 `foo@1.0.0` 链接到 `bar` 。 但是，如果 `bar` 的依赖项中有`"foo": "2.0.0"` ，而 `foo@2.0.0` 在工作空间中并不存在，则将从 npm registry 安装 `foo@2.0.0` 。 这种行为带来了一些不确定性。

幸运的是，pnpm 支持工作空间协议 `workspace:` 。 当使用此协议时，pnpm 将拒绝解析除本地工作区 `package` 之外的任何内容。 因此，如果您设置为 `"foo": "workspace:2.0.0"` 时，安装将会失败，因为 `"foo@2.0.0"` 不存在于工作空间中。

当 [link-workspace-packages](https://pnpm.io/zh/workspaces#link-workspace-packages) 选项 设置为 `false` 时，这个协议就尤其有用。 在这种情况下，仅当使用 `workspace:` 协议声明依赖，pnpm 才会从工作空间链接所需的包。

#### 通过别名引用工作空间包

添加于：v5.12.0

假设您在工作区有一个名为 `foo` 的包。 通常你会像这样引用：`"foo": "workspace:*"`。

如果要使用其他别名，那么以下语法也将起作用: `"bar": "workspace:foo@*"`。

在发布之前，别名被转换为常规名称。 上面的示例将变为：`"bar": "npm:foo@1.0.0"`。

#### 通过相对路径引用工作区包

添加于：v5.12.0

工作区内有两个软件包：

```undefined
+ packages
    + foo
    + bar
```

`bar` 可能有 `foo` 其依赖项声明为这样`"foo": "workspace:../foo"`。 在发布之前，这些将转换为所有包管理器支持的常规的版本规范。

#### 发布工作空间包

当工作空间包打包到归档（无论它是通过 `pnpm pack` ，还是 `pnpm publish` 之类的发布命令）时，我们将动态替换这些 `workspace:` 依赖:

- 目标工作空间中的对应版本（如果使用 `workspace:*`, `workspace:~`, or `workspace:^`）
- 相关的 semver 范围（对于任何其他范围类型）

因此，例如，如果我们的工作空间中有 `foo`、 `bar`、 `qar`、 `zoo` 并且它们的版本都是 `1.5.0`，则如下：

```json
{
    "dependencies": {
        "foo": "workspace:*",
        "bar": "workspace:~",
        "qar": "workspace:^",
        "zoo": "workspace:^1.5.0"
    }
}
```

将转化为：

```json
{
    "dependencies": {
        "foo": "1.5.0",
        "bar": "~1.5.0",
        "qar": "^1.5.0",
        "zoo": "^1.5.0"
    }
}
```

这个功能允许您可以发布转化之后的包到远端，并且可以正常使用本地工作空间的 `packages`，而不需要其它中间步骤。包的使用者也可以像常规的包那样正常使用，且仍然可以受益于语义化版本。

#### 发布工作空间 (release workspace)

工作空间内的 `packages` 的版本管理是个十分复杂的任务， `pnpm`暂未支持内置的解决方案。 不过，有两个测试完好的工具可以处理版本管理且支持 `pnpm` ：

- [changesets](https://github.com/changesets/changesets)
- [Rush](https://rushjs.io/).

有关如何用 `Rush` 建立仓库，请阅读[这篇文章](https://rushjs.io/pages/maintainer/setup_new_repo)。

有关在 `pnpm` 中使用`Changesets` ，请阅读[这里](https://pnpm.io/zh/using-changesets)。

#### 配置项

##### link-workspace-packages

添加于：v2.14.0

- 默认值： **true**
- 类型: **true**, **false**, **deep**

如果启用了此选项，本地可用的`packages`将被链接到 `node_modules` 中而不是从注册表下载。 这在 `monorepo` 项目中使用起来将十分方便。 如果您需要将本地` packages` 也链接到子依赖项，则可以设置为 `deep` （自 v5 版本起）。

否则，` packages` 将全部从注册表下载并安装。 不过，工作空间的` packages` 仍然可以通过 `workspace:` 范围协议被链接到。

##### prefer-workspace-packages

添加于： v5.13.0

- 默认值： **false**
- 类型：**Boolean**

如果启用了此选项，工作空间中的本地 `package `将优先于注册表，即使注册表中有了该 `package `的新版本。

该设置只在工作空间未开启 `save-workspace-protocol` 时有效。

##### shared-workspace-lockfile

添加于：v2.17.0 为 `shared-workspace-shrinkwrap减`

- 默认值： **true**
- 类型：**Boolean**

如果启用此选项，pnpm 会在工作空间的根目录中创建一个唯一的 `pnpm-lock.yaml` 文件。 这也意味着工作空间的`packages`的所有依赖项都将位于单个 `node_modules` 中。（同时软链接到它们`packages` 的 `node_modules` 文件夹中用于 Node 的模块解析）。

此选项的好处：

- 每个依赖都是一个单例
- 在 monorepo 中的安装更快
- 代码更改都在一个文件中、代码审查（Cr ）减少

::: 注意

尽管所有依赖项都将硬链接到根 `node_modules` 中，但`packages` 只能访问 `package.json` 中声明的 ，因此 pnpm 的严格性得以保留。 这是上述软链接的效果。

##### save-workspace-protocol

- 默认值： **true**
- 类型：**Boolean**

如果启用此选项，新的依赖将会被工作空间协议 `workspace:`添加，当且仅当依赖存在于工作空间时。

如果您项目中的工具不支持工作空间协议，您可能希望将此设置更改为 `false`（最好提交一个 PR 让其在后续可以支持）。

Version: 6.x

### 别名

别名让您可以使用自定义名称安装软件包。

假设您在整个项目中 `lodash`。 `lodash` 中有一个错误会破坏您的项目。 你有一个修复，但 `lodash` 不会合并它。 通常 ，您可以直接从您的 fork 中安装 `lodash` (作为 git 托管的 依赖) ，也可以用其他名称发布。 如果您使用第二种解决方案，则必须使用新的依赖名称（`require('lodash')` => `require('awesome-lodash')`）替换项目中的所有需求。 使用别名，您 具有第三个选项。

发布一个名为 `awesome-lodash` 的新包，并使用 `lodash` 作为 的别名安装它：

```undefined
pnpm add lodash@npm:awesome-lodash
```

不需要更改代码。 `lodash` 所有要求现在将解析为 `awesome-lodash`。

有时，您会希望在 项目中使用两个不同版本的包。 很简单：

```sh
pnpm add lodash1@npm:lodash@1
pnpm add lodash2@npm:lodash@2
```

现在，您可以通过 `require('lodash1')` 引入第一个版本的 lodash 并通过 `require('lodash2')` 引入第二个。

当与钩子结合使用时，这会变得更加强大。 也许你想将 `node_modules` 里所有的 `lodash` 替换为 `awesome-lodash` 。 用下面的 `.pnpmfile.cjs` 轻松实现：

```js
function readPackage(pkg) {
  if (pkg.dependencies && pkg.dependencies.lodash) {
    pkg.dependencies.lodash = 'npm:awesome-lodash@^1.0.0'
  }
  return pkg
}

module.exports = {
  hooks: {
    readPackage
  }
}
```

### 使用 Git

#### Lockfile

您应该始终提交Lockfile（pnpm生成的`pnpm-lock.yaml`文件）。 这是出于多种原因，主要是：

- 在 CI 和生产环境中能够更快地完成安装，因为解析依赖的过程可以被跳过。
- 开发，测试和生产环境之间强制执行一致的安装和解析方案，这意味着测试和生产中使用的包将与您开发项目时完全相同

#### 合并冲突

pnpm 可以自动解决 `pnpm-lock.yaml` 的合并冲突。 如果有冲突，只需运行 `pnpm install` 并提交更改。

但是，请注意。 建议您提交之前查看更改，因为我们无法保证 pnpm 会选择正确的头（head） - 它会构建大部分更新的锁文件，这在大多数情况下是理想的。

### PNPM的局限

1. `npm-shrinkwrap.json` 和 `package-lock.json` 被忽略。 与 pnpm 不同，npm可以多次安装相同的 `name@version` ，并且具有不同的依赖项组合。 npm 的锁文件旨在反映平铺的 `node_modules` 布局，但是，由于 pnpm 默认创建隔离布局，它无法由 npm 的锁文件格式反映出来。 但是，如果您希望将锁定文件转换为 pnpm 的格式，请看 [pnpm import](https://pnpm.io/zh/cli/import)。
2. Binstubs（在 `node_modules/.bin`中的文件）总是 shell 文件，而不是指向 JS 文件的符号链接。 创建 shell 文件是为了帮助支持插件的 CLI 的程序在特殊的 `node_modules` 结构中能够正确地找到它们的插件。 这是很少有的问题，如果您希望文件是 JS 文件，请直接引用原始文件，如 [#736](https://github.com/pnpm/pnpm/issues/736) 所示。

### 如何处理 peers

pnpm 的最佳特征之一是，在一个项目中，`package`的一个特定版本将始终只有一组依赖项。 这个规则有一个例外 -那就是具有 [peer dependencies ](https://docs.npmjs.com/files/package.json#peerdependencies)的`package`。

peer 依赖项（peer dependencies）会从依赖图中更高的已安装的依赖项中解析（resolve），因为它们与父级共享相同的版本。 这意味着，如果 `foo@1.0.0` 有两个`peers`（`bar@^1` 和 `baz@^1`），那么它可能在一个项目中有多个不同的依赖项集合。

```text
- foo-parent-1
  - bar@1.0.0
  - baz@1.0.0
  - foo@1.0.0
- foo-parent-2
  - bar@1.0.0
  - baz@1.1.0
  - foo@1.0.0
```

在上面的示例中， `foo@1.0.0` 已安装在 `foo-parent-1` 和 `foo-parent-2` 中。 这两个包（`package`）都同样有 `bar` 和 `baz`，但它们依赖不同版本的 `baz`。 因此， `foo@1.0.0` 有两组不同的依赖项：一组具有 `baz@1.0.0` ，另一组具有 `baz@1.1.0`。 若要支持这些用例，pnpm 必须有几组不同的依赖项，就去硬链接几次 `foo@1.0.0`。

通常，如果一个`package`没有 peer 依赖项（peer dependencies），它会被硬链接到其依赖项的软连接（symlinks）旁的 `node_modules`，就像这样：

```text
node_modules
└── .pnpm
    ├── foo@1.0.0
    │   └── node_modules
    │       ├── foo
    │       ├── qux   -> ../../qux@1.0.0/node_modules/qux
    │       └── plugh -> ../../plugh@1.0.0/node_modules/plugh
    ├── qux@1.0.0
    ├── plugh@1.0.0
```

但是，如果 `foo` 有 peer 依赖（peer dependencies），那么它可能就会有多组依赖项，所以我们为不同的 peer 依赖项创建不同的解析：

```text
node_modules
└── .pnpm
    ├── foo@1.0.0_bar@1.0.0+baz@1.0.0
    │   └── node_modules
    │       ├── foo
    │       ├── bar   -> ../../bar@1.0.0/node_modules/bar
    │       ├── baz   -> ../../baz@1.0.0/node_modules/baz
    │       ├── qux   -> ../../qux@1.0.0/node_modules/qux
    │       └── plugh -> ../../plugh@1.0.0/node_modules/plugh
    ├── foo@1.0.0_bar@1.0.0+baz@1.1.0
    │   └── node_modules
    │       ├── foo
    │       ├── bar   -> ../../bar@1.0.0/node_modules/bar
    │       ├── baz   -> ../../baz@1.1.0/node_modules/baz
    │       ├── qux   -> ../../qux@1.0.0/node_modules/qux
    │       └── plugh -> ../../plugh@1.0.0/node_modules/plugh
    ├── bar@1.0.0
    ├── baz@1.0.0
    ├── baz@1.1.0
    ├── qux@1.0.0
    ├── plugh@1.0.0
```

我们创建` foo@1.0.0_bar@1.0.0+baz@1.0.0` 或`foo@1.0.0_bar@1.0.0+baz@1.1.0`内到`foo`的软链接。 因此，Node.js 模块解析器将找到正确的 peers。

*如果一个`package`没有 peer 依赖（peer dependencies），不过它的依赖项有 peer 依赖，这些依赖会在更高的依赖图中解析*, 则这个传递`package`便可在项目中有几组不同的依赖项。 例如，`a@1.0.0` 具有单个依赖项 `b@1.0.0`。 `b@1.0.0` 有一个 peer 依赖为 `c@^1`。 `a@1.0.0` 永远不会解析`b@1.0.0`的 peer, 所以它也会依赖于 `b@1.0.0` 的 peer 。

以下是该结构在 `node_modules` 的情况。 在这个例子中，`a@1.0.0` 需要在项目的`node_modules` 中出现两次 - 其中一次是被` c@1.0.0` resolve，另一次被 `c@1.1.0`再次 resolve。

```text
node_modules
└── .pnpm
    ├── a@1.0.0_c@1.0.0
    │   └── node_modules
    │       ├── a
    │       └── b -> ../../b@1.0.0_c@1.0.0/node_modules/b
    ├── a@1.0.0_c@1.1.0
    │   └── node_modules
    │       ├── a
    │       └── b -> ../../b@1.0.0_c@1.1.0/node_modules/b
    ├── b@1.0.0_c@1.0.0
    │   └── node_modules
    │       ├── b
    │       └── c -> ../../c@1.0.0/node_modules/c
    ├── b@1.0.0_c@1.1.0
    │   └── node_modules
    │       ├── b
    │       └── c -> ../../c@1.1.0/node_modules/c
    ├── c@1.0.0
    ├── c@1.1.0
```

### pnpm vs npm

#### npm 的扁平树

从 npm v3 开始，npm 维护了一个[扁平依赖树](https://github.com/npm/npm/issues/6912) 这导致磁盘空间减少， 并且`node_modules` 目录是混乱的。

另一方面，pnpm 通过使用硬链接和符号链接链接到全局硬盘来管理`node_modules`。 这将使你的磁盘空间使用量大大减少，同时保持`node_modules` 的整洁。 如果你希望了解更多信息，可以参考[store layout](https://pnpm.io/zh/symlinked-node-modules-structure)。

pnpm 正确的 `node_modules` 结构的好处在于，它"[有助于避免愚蠢的错误](https://www.kochan.io/nodejs/pnpms-strictness-helps-to-avoid-silly-bugs.html)"，因为它让你无法使用不是 `package.json` 中指定的模块。

#### 安装

pnpm 不允许安装 `package.json` 中没有包含的包。 如果没有参数传递给 `pnpm add`，包将保存为常规依赖项。 与 npm 一样， `--save-dev` 和 `--save-optional` 可以是用于安装包作为开发或可选的依赖。

由于此限制，项目在使用 pnpm 时不会有任何无关的包，除非它们删除依赖项并将其保留为孤立的。 这就是为什么 pnpm 的实现的 [prune command](https://pnpm.io/zh/cli/prune) 不允许你指定包来修剪 - 它总是去除所有多余的和孤儿包。

#### 目录依赖

目录依赖以 `file:` 前缀开始，指向文件系统的目录。 与 npm 一样，pnpm 符号链接这些依赖项。 与 npm 不同的是，pnpm 不执行这些文件依赖项的安装。

这意味着如果您有一个名为 `foo` (`<root>/foo`) 的包，它有 `bar@file:../bar` 作为依赖项，则当你在 `foo` 上执行 `pnpm install` 时， pnpm 将不会为 `<root>/bar` 安装。

如果您需要同时在多个包中运行安装，例如在 monorepo 的情况下，您应该查看 [`pnpm -r`](https://pnpm.io/zh/cli/recursive) 的文档。
