这个文档记录了	关于 `packages.json` 中是所有相关字段内容

### name

如果你需要发布包，那么`package.json`中最重要的是`name`和`version`它们是必需的。`name`和`version` 组合起来形成一个标识符，该标识符被认为是完全唯一的。如果你不需要发布包，则名称和版本字段是可选的。

- 长度必须少于或等于214个字符
- name字段最终成为URL、命令行上的参数，文件夹名称的一部分。因此，name不能包含任何非URL安全字符。

需要注意的是

- 不要使用与node模块重复的名称
- 如果你需要发布包则应该先检查是否有重名的包

name 可以有选择的以作用于为前缀，例如 `@myorg/mypackage`. 更多请查看 [`scope`](https://docs.npmjs.com/cli/v9/using-npm/scope) 

### version

包版本，版本字段内容必须符合 [node-semver](https://github.com/npm/node-semver) 解析规则

### description

一个字符串字段，用于描述此包，可以被 `npm search` 命令搜索到

### keywords

一个关键字字符串数组，用于描述此包，可以被 `npm search` 命令搜索到

### homepage

项目主页地址

```json
"homepage": "https://github.com/owner/project#readme"
```

### bugs

bug信息接收的地方

```json
{
  "url" : "https://github.com/owner/project/issues",
  "email" : "project@hostname.com"
}
```

可以指定一个值，也可以同时指定两个值。如果您只想提供一个url，您可以将“bugs”的值指定为一个简单的字符串，而不是一个对象。

如果提供了一个`url`，它将被 `npm-bugs` 命令使用。

### license

开源许可证

```json
{
  "license" : "BSD-3-Clause"
}
```

```json
{
  "license" : "(ISC OR GPL-3.0)"
}
```

```json
{
  "license" : "SEE LICENSE IN <filename>"
}
```

```json
// Not valid metadata
{
  "license" : {
    "type" : "ISC",
    "url" : "https://opensource.org/licenses/ISC"
  }
}

// Not valid metadata
{
  "licenses" : [
    {
      "type": "MIT",
      "url": "https://www.opensource.org/licenses/mit-license.php"
    },
    {
      "type": "Apache-2.0",
      "url": "https://opensource.org/licenses/apache2.0.php"
    }
  ]
}
```

```json
{
  "license": "ISC"
}
```

```json
{
  "license": "(MIT OR Apache-2.0)"
}
```

```json
{
  "license": "UNLICENSED"
}
```

### 人员字段: author, contributors

`author`是一个人员信息对象。`contributors`是一系列的人员信息对象。人员信息对象包括：

```json
{
  "name" : "Barney Rubble",
  "email" : "b@rubble.com",
  "url" : "http://barnyrubble.tumblr.com/"
}
```

或者，你可以将所有这些缩短为一个字符串，`npm`将为你解析：

```json
{
  "author": "Barney Rubble <b@rubble.com> (http://barnyrubble.tumblr.com/)"
}
```

`email`和`url`都是可选的。

`npm`还为你的`npm`用户信息设置了一个顶级的`maintainers`字段。

### funding

您可以指定一个对象，该对象包含一个URL，该URL提供有关帮助资助软件包开发的方法的最新信息，或字符串URL，或以下数组：

```json
{
  "funding": {
    "type" : "individual",
    "url" : "http://example.com/donate"
  },

  "funding": {
    "type" : "patreon",
    "url" : "https://www.patreon.com/my-account"
  },

  "funding": "http://example.com/donate",

  "funding": [
    {
      "type" : "individual",
      "url" : "http://example.com/donate"
    },
    "http://example.com/donateAlso",
    {
      "type" : "patreon",
      "url" : "https://www.patreon.com/my-account"
    }
  ]
}
```

### files

可选的 `files` 字段是一个`文件模式`数组，用于描述将此包作为依赖项安装的时候需要包含的文件或目录。文件模式遵循与“.gitignore”类似的语法，但相反：包括文件、目录或glob模式（""、"/\*\*/*"等）省略该字段将使其默认为`[“*”]`，这意味着它将包括所有文件。

一些特殊的文件和目录也被默认包含在内，无论它们是否存在于files数组中

您还可以在包的根目录或子目录中提供一个`.npmignore`文件，这将阻止文件被包括在内。在包的根目录中，它不会覆盖`files`字段，但在子目录中会覆盖。`.npmignore`文件的工作原理与`.gitignore`类似。如果存在`.gitgnore`文件，并且缺少`.npmmignore`，则将使用`.gitimmore`的内容。

`files`字段中包含的文件不能通过`.npmignore`或`.gitigner`排除。

默认包含的文件

- `package.json`
- `README`
- `LICENSE` / `LICENCE`
- `main` 字段指定的文件

`README` & `LICENSE` 可以有任何扩展名和不区分大小写

相反某些文件总是被忽略

- `.git`
- `CVS`
- `.svn`
- `.hg`
- `.lock-wscript`
- `.wafpickle-N`
- `.*.swp`
- `.DS_Store`
- `._*`
- `npm-debug.log`
- `.npmrc`
- `node_modules`
- `config.gypi`
- `*.orig`
- `package-lock.json` (use [`npm-shrinkwrap.json`](https://docs.npmjs.com/cli/v9/configuring-npm/npm-shrinkwrap-json) if you wish it to be published)

### main

它是项目的主要入口文件，如果你的包名为 foo，并且用户安装了它，然后执行了`require("foo")`将引用main字段指定的模块

main 应该是一个相对于包文件夹根目录的模块。

对于大多数模块来说，有一个主脚本是最有意义的，而通常没有太多其他内容。

如果未设置 `main`，则默认为包根文件夹中的 `index.js`。

### browser

如果你的模块要在客户端使用，则应使用`browser`字段而不是`main`。这样由助于提示用户它可能依赖于 Node.js 模块中不可用的模块

### bin

许多软件包都有一个或多个可执行文件，它们希望将这些文件安装到`PATH`中。`npm`使这变得非常容易

要使用它，请在你的`package.json`中提供一个`bin`字段，它是**命令名到本地文件名的映射**。当全局安装此程序包时，该文件将链接到全局`bins`目录中，或者将创建一个`cmd`（Windows命令文件）来执行`bin`字段中的指定文件，因此它可以通过“name”或“name.cmd”（在Windows PowerShell上）运行。当此程序包作为依赖项安装在另一个程序包中时，文件将被链接到该程序包可以直接通过`npm exec`或通过`npm run script`调用其他脚本时通过其他脚本中的名称使用的位置。.

```json
{
  "bin": {
    "myapp": "./cli.js"
  }
}
```

So, when you install myapp, in case of unix-like OS it'll create a symlink from the `cli.js` script to `/usr/local/bin/myapp` and in case of windows it will create a cmd file usually at `C:\Users\{Username}\AppData\Roaming\npm\myapp.cmd` which runs the `cli.js` script.

如果您有一个可执行文件，并且它的名称是包的名称，那么您可以将其作为字符串提供。例如

```json
{
  "name": "my-program",
  "version": "1.2.5",
  "bin": "./path/to/program"
}
```

将与此相同

```json
{
  "name": "my-program",
  "version": "1.2.5",
  "bin": {
    "my-program": "./path/to/program"
  }
}
```

请确保 `bin`中引用的文件以``#!/usr/bin/env node`开头，否则脚本将在没有`node`可执行文件的情况下启动！

请注意你也可以使用 [directories.bin](https://docs.npmjs.com/cli/v9/configuring-npm/package-json?v=true#directoriesbin) 来设置可执行文件

请参阅[folders](https://docs.npmjs.com/cli/v9/configuring-npm/folders#executables) 以获取更多可执行文件的信息

### man

指定一个文件或一组文件名，以便 `man`程序查找。

如果只提供了一个文件，那么无论其实际文件名如何，都会将其安装为`man <pkgname>`的结果。例如：

```json
{
  "name": "foo",
  "version": "1.2.3",
  "description": "A packaged foo fooer for fooing foos",
  "main": "foo.js",
  "man": "./man/doc.1"
}
```

将连接 `./man/doc.1` 文件 使其成为 `man foo`的目标文件

如果文件名不是以包名开头的，则会以它为前缀

```json
{
  "name": "foo",
  "version": "1.2.3",
  "description": "A packaged foo fooer for fooing foos",
  "main": "foo.js",
  "man": [
    "./man/foo.1",
    "./man/bar.1"
  ]
}
```

将创建执行`man foo`和`man foo-bar`的目标文件。

`man`文件必须以数字结尾，如果是压缩的，还可以选择以`.gz`结尾。数字指示文件安装到哪个man部分。

```json
{
  "name": "foo",
  "version": "1.2.3",
  "description": "A packaged foo fooer for fooing foos",
  "main": "foo.js",
  "man": [
    "./man/foo.1",
    "./man/foo.2"
  ]
}
```

将为`man foo`和`man 2 foo`创建目标

### directories

The CommonJS [Packages](http://wiki.commonjs.org/wiki/Packages/1.0) spec details a few ways that you can indicate the structure of your package using a `directories` object. If you look at [npm's package.json](https://registry.npmjs.org/npm/latest), you'll see that it has directories for doc, lib, and man.

In the future, this information may be used in other creative ways.

#### directories.bin

If you specify a `bin` directory in `directories.bin`, all the files in that folder will be added.

Because of the way the `bin` directive works, specifying both a `bin` path and setting `directories.bin` is an error. If you want to specify individual files, use `bin`, and for all the files in an existing `bin` directory, use `directories.bin`.

#### directories.man

A folder that is full of man pages. Sugar to generate a "man" array by walking the folder.

### repository

指定远程代码库的位置

```json
{
  "repository": {
    "type": "git",
    "url": "https://github.com/npm/cli.git"
  }
}
```

URL应该是一个公开可用的URL，可以直接交给VCS程序，而无需任何修改。它不应该是你放在浏览器中的html项目页面的url。这是给电脑的。

对于 `GitHub`, `GitHub gist`, `Bitbucket`,  `GitLab` 你可以使用与 `npm install`相同的快捷方式语法：

```json
{
  "repository": "npm/npm",
  "repository": "github:user/repo",
  "repository": "gist:11081aaa281",
  "repository": "bitbucket:user/repo",
  "repository": "gitlab:user/repo"
}
```

如果包的 `package.json`不在根目录中（例如，如果它是`monoreo`的一部分），则可以指定它所在的目录：

```json
{
  "repository": {
    "type": "git",
    "url": "https://github.com/facebook/react.git",
    "directory": "packages/react-dom"
  }
}
```

### scripts

一个对象记录了可执行的命令

```json
{
    "scripts":{
        "build":"tsc index.ts"
    }
}
```

请在此[`scripts`](https://docs.npmjs.com/cli/v9/using-npm/scripts) 更多关于scripts的信息

### config

`config`对象可用于设置包脚本中使用的配置参数，这些参数在升级过程中保持不变。例如，如果一个包具有以下内容：

```json
{
  "name": "foo",
  "config": {
    "port": "8080"
  }
}
```

It could also have a "start" command that referenced the `npm_package_config_port` environment variable.

### dependencies

依赖关系在一个简单的对象中指定，该对象将包名称映射到版本范围。 版本范围是一个字符串，它有一个或多个空格分隔的描述符。 依赖项也可以用压缩包 URL或 git URL 来标识。

请不要将测试工具或转译器或其他“开发”时间工具放入您的“依赖项”对象中。 请参阅下面的“devDependencies”。

有关指定版本范围的更多详细信息，请查看[semver](https://github.com/npm/node-semver#versions) 

- `version` Must match `version` exactly
- `>version` Must be greater than `version`
- `>=version` etc
- `<version`
- `<=version`
- `~version` "Approximately equivalent to version" See [semver](https://github.com/npm/node-semver#versions)
- `^version` "Compatible with version" See [semver](https://github.com/npm/node-semver#versions)
- `1.2.x` 1.2.0, 1.2.1, etc., but not 1.3.0
- `http://...` See 'URLs as Dependencies' below
- `*` Matches any version
- `""` (just an empty string) Same as `*`
- `version1 - version2` Same as `>=version1 <=version2`.
- `range1 || range2` Passes if either range1 or range2 are satisfied.
- `git...` See 'Git URLs as Dependencies' below
- `user/repo` See 'GitHub URLs' below
- `tag` A specific version tagged and published as `tag` See [`npm dist-tag`](https://docs.npmjs.com/cli/v9/commands/npm-dist-tag)
- `path/path/path` See [Local Paths](https://docs.npmjs.com/cli/v9/configuring-npm/package-json?v=true#local-paths) below

For example, these are all valid:

```json
{
  "dependencies": {
    "foo": "1.0.0 - 2.9999.9999",
    "bar": ">=1.0.2 <2.1.2",
    "baz": ">1.0.2 <=2.3.4",
    "boo": "2.0.1",
    "qux": "<1.0.0 || >=2.3.1 <2.4.5 || >=2.5.2 <3.0.0",
    "asd": "http://asdf.com/asdf.tar.gz",
    "til": "~1.2",
    "elf": "~1.2.3",
    "two": "2.x",
    "thr": "3.3.x",
    "lat": "latest",
    "dyl": "file:../dyl"
  }
}
```

#### URLs as Dependencies

您可以指定一个 压缩包URL 来代替版本范围。

此压缩包将在安装时下载并安装到您的本地包中。

#### Git URLs as Dependencies

Git urls are of the form:

```bash
<protocol>://[<user>[:<password>]@]<hostname>[:<port>][:][/]<path>[#<commit-ish> | #semver:<semver>]
```

`<protocol>` is one of `git`, `git+ssh`, `git+http`, `git+https`, or `git+file`.

If `#<commit-ish>` is provided, it will be used to clone exactly that commit. If the commit-ish has the format `#semver:<semver>`, `<semver>` can be any valid semver range or exact version, and npm will look for any tags or refs matching that range in the remote repository, much as it would for a registry dependency. If neither `#<commit-ish>` or `#semver:<semver>` is specified, then the default branch is used.

Examples:

```bash
git+ssh://git@github.com:npm/cli.git#v1.0.27
git+ssh://git@github.com:npm/cli#semver:^5.0
git+https://isaacs@github.com/npm/cli.git
git://github.com/npm/cli.git#v1.0.27
```

When installing from a `git` repository, the presence of certain fields in the `package.json` will cause npm to believe it needs to perform a build. To do so your repository will be cloned into a temporary directory, all of its deps installed, relevant scripts run, and the resulting directory packed and installed.

This flow will occur if your git dependency uses `workspaces`, or if any of the following scripts are present:

- `build`
- `prepare`
- `prepack`
- `preinstall`
- `install`
- `postinstall`

If your git repository includes pre-built artifacts, you will likely want to make sure that none of the above scripts are defined, or your dependency will be rebuilt for every installation.

#### GitHub URLs

As of version 1.1.65, you can refer to GitHub urls as just "foo": "user/foo-project". Just as with git URLs, a `commit-ish` suffix can be included. For example:



```json
{
  "name": "foo",
  "version": "0.0.0",
  "dependencies": {
    "express": "expressjs/express",
    "mocha": "mochajs/mocha#4727d357ea",
    "module": "user/repo#feature\/branch"
  }
}
```

#### Local Paths

从 2.0.0 版开始，您可以提供包含包的本地目录的路径。 可以使用以下任何一种形式使用 `npm install -S` 或 `npm install --save` 保存本地路径：



```bash
../foo/bar
~/foo/bar
./foo/bar
/foo/bar
```

在这种情况下，它们将被标准化为相对路径并添加到您的 `package.json` 中。 例如：

```json
{
  "name": "baz",
  "dependencies": {
    "bar": "file:../foo/bar"
  }
}
```

This feature is helpful for local offline development and creating tests that require npm installing where you don't want to hit an external server, but should not be used when publishing packages to the public registry.

*note*: Packages linked by local path will not have their own dependencies installed when `npm install` is ran in this case. You must run `npm install` from inside the local path itself.

### devDependencies

如果有人想在在他们的程序中`install`您的模块，那么他们不需要`install`您的模块在构建阶段使用的依赖项。

在这种情况下，最好将这些依赖项添添加到 `devDependencies` 中。

这些东西将在从包的根目录执行 `npm link` 或 `npm install` 时安装，并且可以像任何其他 `npm` 配置参数一样进行管理。 有关该主题的更多信息，请参阅 [config](https://docs.npmjs.com/cli/v9/using-npm/config)

对于不特定于平台的构建步骤，例如将 `CoffeeScript` 或其他语言编译为 JavaScript，请使用 `prepare` 脚本来执行此操作，并将所需的包设为 `devDependency`。

```json
{
  "name": "ethopia-waza",
  "description": "a delightfully fruity coffee varietal",
  "version": "1.2.3",
  "devDependencies": {
    "coffee-script": "~1.6.3"
  },
  "scripts": {
    "prepare": "coffee -o lib/ -c src/waza.coffee"
  },
  "main": "lib/waza.js"
}
```

`prepare` 脚本将在发布之前运行，这样用户就可以使用这些功能而无需他们自己编译。 在开发模式下（即在本地运行 `npm install`），它也会运行这个脚本，这样您就可以轻松地对其进行测试。

### peerDependencies

在某些情况下，您希望表达您的包与主机工具或库的兼容性，而不必对该主机或库执行“require”。

In some cases, you want to express the compatibility of your package with a host tool or library, while not necessarily doing a `require` of this host. This is usually referred to as a *plugin*. Notably, your module may be exposing a specific interface, expected and specified by the host documentation.

For example:



```json
{
  "name": "tea-latte",
  "version": "1.3.5",
  "peerDependencies": {
    "tea": "2.x"
  }
}
```

This ensures your package `tea-latte` can be installed *along* with the second major version of the host package `tea` only. `npm install tea-latte` could possibly yield the following dependency graph:



```bash
├── tea-latte@1.3.5
└── tea@2.2.0
```

In npm versions 3 through 6, `peerDependencies` were not automatically installed, and would raise a warning if an invalid version of the peer dependency was found in the tree. As of npm v7, peerDependencies *are* installed by default.

Trying to install another plugin with a conflicting requirement may cause an error if the tree cannot be resolved correctly. For this reason, make sure your plugin requirement is as broad as possible, and not to lock it down to specific patch versions.

Assuming the host complies with [semver](https://semver.org/), only changes in the host package's major version will break your plugin. Thus, if you've worked with every 1.x version of the host package, use `"^1.0"` or `"1.x"` to express this. If you depend on features introduced in 1.5.2, use `"^1.5.2"`.

### peerDependenciesMeta

When a user installs your package, npm will emit warnings if packages specified in `peerDependencies` are not already installed. The `peerDependenciesMeta` field serves to provide npm more information on how your peer dependencies are to be used. Specifically, it allows peer dependencies to be marked as optional.

For example:



```json
{
  "name": "tea-latte",
  "version": "1.3.5",
  "peerDependencies": {
    "tea": "2.x",
    "soy-milk": "1.2"
  },
  "peerDependenciesMeta": {
    "soy-milk": {
      "optional": true
    }
  }
}
```

Marking a peer dependency as optional ensures npm will not emit a warning if the `soy-milk` package is not installed on the host. This allows you to integrate and interact with a variety of host packages without requiring all of them to be installed.

### bundleDependencies

This defines an array of package names that will be bundled when publishing the package.

In cases where you need to preserve npm packages locally or have them available through a single file download, you can bundle the packages in a tarball file by specifying the package names in the `bundleDependencies` array and executing `npm pack`.

For example:

If we define a package.json like this:



```json
{
  "name": "awesome-web-framework",
  "version": "1.0.0",
  "bundleDependencies": [
    "renderized",
    "super-streams"
  ]
}
```

we can obtain `awesome-web-framework-1.0.0.tgz` file by running `npm pack`. This file contains the dependencies `renderized` and `super-streams` which can be installed in a new project by executing `npm install awesome-web-framework-1.0.0.tgz`. Note that the package names do not include any versions, as that information is specified in `dependencies`.

If this is spelled `"bundledDependencies"`, then that is also honored.

Alternatively, `"bundleDependencies"` can be defined as a boolean value. A value of `true` will bundle all dependencies, a value of `false` will bundle none.

### optionalDependencies

If a dependency can be used, but you would like npm to proceed if it cannot be found or fails to install, then you may put it in the `optionalDependencies` object. This is a map of package name to version or url, just like the `dependencies` object. The difference is that build failures do not cause installation to fail. Running `npm install --omit=optional` will prevent these dependencies from being installed.

It is still your program's responsibility to handle the lack of the dependency. For example, something like this:



```js
try {
  var foo = require('foo')
  var fooVersion = require('foo/package.json').version
} catch (er) {
  foo = null
}
if ( notGoodFooVersion(fooVersion) ) {
  foo = null
}

// .. then later in your program ..

if (foo) {
  foo.doFooThings()
}
```

Entries in `optionalDependencies` will override entries of the same name in `dependencies`, so it's usually best to only put in one place.

### overrides

If you need to make specific changes to dependencies of your dependencies, for example replacing the version of a dependency with a known security issue, replacing an existing dependency with a fork, or making sure that the same version of a package is used everywhere, then you may add an override.

Overrides provide a way to replace a package in your dependency tree with another version, or another package entirely. These changes can be scoped as specific or as vague as desired.

To make sure the package `foo` is always installed as version `1.0.0` no matter what version your dependencies rely on:



```json
{
  "overrides": {
    "foo": "1.0.0"
  }
}
```

The above is a short hand notation, the full object form can be used to allow overriding a package itself as well as a child of the package. This will cause `foo` to always be `1.0.0` while also making `bar` at any depth beyond `foo` also `1.0.0`:



```json
{
  "overrides": {
    "foo": {
      ".": "1.0.0",
      "bar": "1.0.0"
    }
  }
}
```

To only override `foo` to be `1.0.0` when it's a child (or grandchild, or great grandchild, etc) of the package `bar`:



```json
{
  "overrides": {
    "bar": {
      "foo": "1.0.0"
    }
  }
}
```

Keys can be nested to any arbitrary length. To override `foo` only when it's a child of `bar` and only when `bar` is a child of `baz`:



```json
{
  "overrides": {
    "baz": {
      "bar": {
        "foo": "1.0.0"
      }
    }
  }
}
```

The key of an override can also include a version, or range of versions. To override `foo` to `1.0.0`, but only when it's a child of `bar@2.0.0`:



```json
{
  "overrides": {
    "bar@2.0.0": {
      "foo": "1.0.0"
    }
  }
}
```

You may not set an override for a package that you directly depend on unless both the dependency and the override itself share the exact same spec. To make this limitation easier to deal with, overrides may also be defined as a reference to a spec for a direct dependency by prefixing the name of the package you wish the version to match with a `$`.



```json
{
  "dependencies": {
    "foo": "^1.0.0"
  },
  "overrides": {
    // BAD, will throw an EOVERRIDE error
    // "foo": "^2.0.0"
    // GOOD, specs match so override is allowed
    // "foo": "^1.0.0"
    // BEST, the override is defined as a reference to the dependency
    "foo": "$foo",
    // the referenced package does not need to match the overridden one
    "bar": "$foo"
  }
}
```

### engines

你可以指定你的项目需要的node版本：

```json
{
  "engines": {
    "node": ">=0.10.3 <15"
  }
}
```

和`dependencies`一样，如果不指定版本（或者指定`*`作为版本），那么任何版本的`node`都可以。

您还可以使用`engines`字段来指定哪些版本的`npm`能够正确安装您的程序。例如：

```json
{
  "engines": {
    "npm": "~1.0.20"
  }
}
```

除非用户设置了 [`engine-strict` config](https://docs.npmjs.com/cli/v9/using-npm/config#engine-strict) 标志, 否则此字段值作为咨询性字段，并且仅当您的包作为依赖项安装时才会产生警告。

### os

您可以指定模块将在哪些操作系统上运行：

```json
{
  "os": [
    "darwin",
    "linux"
  ]
}
```

你也可以阻止而不是允许操作系统，只需在被阻止的操作系统前加一个`!`：

```json
{
  "os": [
    "!win32"
  ]
}
```

主机操作系统由`process.platform`查看

它可以阻止和允许一个项目，尽管没有任何好的理由这样做。

### cpu

如果你的代码只在某些`cpu`架构上运行，那么您可以指定哪些架构。

```json
{
  "cpu": [
    "x64",
    "ia32"
  ]
}
```

与`os`选项一样，您也可以阻止某些`cpu`架构运行：

```json
{
  "cpu": [
    "!arm",
    "!mips"
  ]
}
```

`cpu`架构可以根据`process.arch`查看

### private

如果 `package.json `中设置了`"private"：true`，那么`npm`将不会发布此包。

这是一种防止私人存储库意外发布的方法。如果您想确保给定的包只发布到特定的`npm`库（例如私有`npm`库），那么在发布时使用下面描述的`publishConfig`字段来覆盖`registry`配置参数。

### publishConfig

这是一组在publish时候使用的配置值，如果你想设置 tags，访问权限，远程库地址，那么可以使用它

请查看 [`config`](https://docs.npmjs.com/cli/v9/using-npm/config) 以获取所有的可配置项

### workspaces

此字段是一个文件模式数组，用于指定子工作区的位置

以下展示了将`packages`目录下的每一个文件夹作为子工作区

```json
{
  "name": "workspace-example",
  "workspaces": [
    "./packages/*"
  ]
}
```

### DEFAULT VALUES

npm will default some values based on package contents.

- `"scripts": {"start": "node server.js"}`

  If there is a `server.js` file in the root of your package, then npm will default the `start` command to `node server.js`.

- `"scripts":{"install": "node-gyp rebuild"}`

  If there is a `binding.gyp` file in the root of your package and you have not defined an `install` or `preinstall` script, npm will default the `install` command to compile using node-gyp.

- `"contributors": [...]`

  If there is an `AUTHORS` file in the root of your package, npm will treat each line as a `Name <email> (url)` format, where email and url are optional. Lines which start with a `#` or are blank, will be ignored.

