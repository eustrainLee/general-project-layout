# 多项目布局

## 摘要

如果在一个 git 仓库中存储了多个项目，那么这个仓库实际充当了一个工作空间（Workspace），而不是项目的根目录。因此本项目提供一个用于同时管理多个项目的解决方案。

此解决方案参考了一些常用语言使用的项目布局和约定，并添加了额外的约束，以减少预期之外的冲突。

因为各个编程语言使用的术语各不相同，所以用词可能会出现混乱，请按照对应的编程语言来理解。

不约定使用什么方案构建项目，可以自由地选择 CMake、meson、XMake 等构建工具，也可以使用 Shell、Python 等脚本语言，但此处以 meson 为例。

## 支持的编程语言

- C
- C++
- Golang[^1]
- Rust[^2]

以下编程语言待提供

- JavaScript / TypeScript (Node.JS)
- Java（maven）
- Python (Python3)

## 示例

为了便于阅读，此处没有按照字母顺序排列。

- `bin` *为编译得到的目标程序预留*
  - `{project}` *项目名称*
    - `{executable-file}` *可执行文件名，可能还有其他相关文件*
- `build` *一个临时目录，用于编译项目*
- `docs` *存储项目文档*
- `include` *总 include 目录，包含项目中所有以 C/C++ 头文件形式提供的接口*
  - `{module}` *模块名称*
    - `common.h / common.hpp` *提供模块中最基础的定义，如声明公用宏、定义基础类型等*
    - `a.h / a.hpp` *头文件*
    - `b` *子模块，用于对头文件分类管理*
      - `c.h / c.hpp` *子模块头文件*
- `{c-project}` *一个 C 项目名称*
  - `src` *用于存放源代码文件*
    - `main.c` *main 函数所在的文件*
  - `meson.build` *构建脚本文件*
- `{cpp-project}` *一个 C++ 项目名称*
  - `src` *用于存放源代码文件*
    - `main.cpp` *main 函数所在的文件*
  - `meson.build` *构建脚本文件*
- `{go-project}` *Golang 项目名称*
  - `cmd` *用于存放 main 包*
    - `{go-project}` *main 包，此目录应与项目名称相同*
      - `main.go` *main 函数所在的文件*
  - `internal` *用于存放源代码文件*
  - `~~pkg~~` *不要使用 pkg 目录，因为在Go项目标准布局[^1]中 /pkg 用于存放外部应用程序可以使用的库代码，如果项目被外部引用，那它应为一个独立的 Go module，而不仅是作为项目的一个包*
  - `vendor` *存储在本地的外部依赖，按需使用 go vendor 功能*
  - `meson.build` *如果需要，可以提供构建脚本文件*
  - `go.mod` *go 模块文件，格式 {project-url}/{go-project}*
  - `go.sum` *go 模块文件*
- `{rust-project}` *Rust 项目（包）名称*
  - `src` *用于存放源文件*
    - `main.rs` *main 函数所在的文件*
  - `Cargo.toml`
  - `Cargo.lock`
  - `build.rs` *如果需要引入非 Rust 代码，可以提供 build.rs 文件*
  - `meson.build` *如果需要，可以提供构建脚本文件*
- `shared` *用于存放多个项目共用的代码。名称不应与上级目录中的项目冲突，这里可以互相引用，但是应尽量不要出现互相依赖*
  - `{c-module}` *一个 C/C++ 模块，应当唯一对应 include 中的一个目录*
    - `a.c / a.cpp`
      - `b`
        - `c.c / c.cpp`
    - `meson.build` *构建脚本文件*
  - `{rust-library}` *一个库类型的 Rust crate，使用 `cargo new shared/{rust-library} --lib` 创建，在 Cargo.toml 文件中，并不会出现 `shared/` 前缀*
    - `src`
      - `lib.rs` *crate 的根*
      - `a.rs`
        - `b`
          - `c.rs`
    - `build.rs` *如果需要引入非 Rust 代码，可以提供 build.rs 文件*
    - `Cargo.toml`
    - `Cargo.lock`
    - `meson.build` *如果需要，可以提供构建脚本文件*
  - `{go-module}` *一个 Go 模块*
    - `api` *接口声明*
    - `pkg` *提供外部使用的代码*
    - `internal` *模块私有代码*
    - `go.mod` *go 模块文件，格式 {project-url}/shared/{go-module}*
    - `go.sum` *go 模块文件*
- `modules` *通过 git summodule 引入的外部模块*
  - `{external-git-module}` *外部 git submodule*
- `script` *非核心项目代码的脚本文件*
- `docker` *存放 Dockerfile 文件*
- `docker-compose` *存放 Docker compose 文件*
- `Cargo.toml` *如果此项目包含了 Rust 代码，应当在根目录提供 `Cargo.toml`，声明其是一个 Rust packege。*
- `Cargo.lock`
- `~~go.mod~~` *不要在根目录提供 Golang 模块声明，因为 Golang 的相关工具在处理这种项目时可能会出现问题*
- `~~go.sum~~`  *因为没有 go.mod 文件，所以也不需要 go.sum*
- `go.work` *如果需要，可以使用 go.work 管理各个 go 模块*
- `meson.build` *构建脚本文件*

[^1]: [Go项目标准布局](https://github.com/golang-standards/project-layout/blob/master/README_zh-CN.mdhttps://github.com/golang-standards/project-layout)
[^2]: [Rust 项目标准布局](https://doc.rust-lang.org/cargo/guide/project-layout.html)
