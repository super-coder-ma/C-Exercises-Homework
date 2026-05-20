# C 语言作业练习库

[![Build Status](https://img.shields.io/badge/build-passing-brightgreen)]()
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)]()

简介
----
这是一个面向学习与作业练习的 C 语言仓库，收集了常见的练习题、参考解答、测试用例以及构建工具，便于练习、批改与自动化测试。适合本科计算机/软件工程课程、在线编程训练或自学使用。

仓库目标
- 按主题组织练习（基础语法、数组与字符串、指针、数据结构、算法等）。
- 为每道题提供：题目描述、示例输入输出、参考实现、单元测试。
- 提供统一的构建/测试流程（Makefile / CI）。
- 便于学生提交作业与教师或助教自动化批改。

目录结构
----
```
.
├── exercises/           # 练习题目录（每题一个子目录）
│   ├── ex01_sum         # 题目目录示例
│   │   ├── README.md    # 题目描述（输入输出、限制、示例）
│   │   ├── src/         # 学生源代码目录（或作业提交目录）
│   │   ├── solution/    # 参考实现
│   │   └── tests/       # 单元测试 / 测试用例
│   └── ...
├── libs/                # 公共工具/库（可选）
├── tools/               # 辅助脚本（批量测试、打包、评分脚本）
├── tests/               # 全局测试（整合所有题目的测试）
├── Makefile             # 顶层构建与测试入口
├── .github/             # CI 配置（GitHub Actions）
└── README.md
```

练习目录约定
----
- 每个练习放在 `exercises/<id>_<slug>/` 下。
- 题目描述放 `README.md`（包括输入输出格式、约束、示例、评分标准）。
- 学生提交的文件放在 `src/`（或另行约定命名）。
- 参考实现放 `solution/`（供教师或参考）。
- 测试放 `tests/`，包含测试驱动或用例（例如使用 Check、CUnit、cmocka 或自制测试脚本）。

示例题目 README 模板（exercises/ex01_sum/README.md）
----
- 题目：计算一组整数的和
- 输入：第一行包含 n (1 ≤ n ≤ 1000)，接下来一行包含 n 个整数
- 输出：一个整数，表示输入整数的和
- 示例：
  ```
  输入：
  5
  1 2 3 4 5
  输出：
  15
  ```
- 评分：通过所有 tests/ 下的测试用例视为通过

环境与依赖
----
- 编译器：gcc（推荐 9+），也支持 clang
- 构建工具：make（顶层 Makefile 会调用子目录 Makefile）
- 单元测试（可选）：Check / CUnit / cmocka（根据仓库选择一种）
- 在 Linux / macOS 上测试通过，Windows 建议使用 WSL 或 MSYS2

快速开始（开发者 / 学生）
----
1. 克隆仓库：
   ```bash
   git clone https://github.com/<owner>/<repo>.git
   cd <repo>
   ```
2. 构建所有练习（默认生成到 `bin/`）：
   ```bash
   make all
   ```
3. 构建并运行某个练习（例如 ex01）：
   ```bash
   make ex01       # 依赖于仓库的 Makefile 命名规则
   ./bin/ex01 < exercises/ex01_sum/tests/input1.txt
   ```
4. 运行所有单元测试：
   ```bash
   make test
   ```
5. 清理：
   ```bash
   make clean
   ```

Makefile 建议规则
----
顶层 Makefile 可包含目标：
- `all`：构建所有题目的可执行文件
- `ex<N>`：构建单个题目
- `test`：运行所有测试并输出报告
- `clean`：清理构建产物

示例 make 片段（顶层 Makefile 中的伪示例）：
```makefile
.PHONY: all test clean ex01
all:
	$(MAKE) -C exercises/ex01_sum

ex01:
	$(MAKE) -C exercises/ex01_sum

test:
	for d in exercises/*; do \
	  if [ -f $$d/Makefile ]; then \
	    $(MAKE) -C $$d test || exit 1; \
	  fi; \
	done

clean:
	for d in exercises/*; do \
	  $(MAKE) -C $$d clean || true; \
	done
```

代码风格与约定
----
- 文件命名：小写，下划线分隔，如 `main.c`, `utils.c`
- 函数命名：小写加下划线，如 `compute_sum`
- 缩进：4 个空格（禁止 tab 混用）
- 注释：函数使用头注释说明输入/输出/副作用
- 禁止使用未初始化变量，返回值检查（尤其是 malloc / fopen）
- 尽量避免全局变量；必要时在题目说明中明确允许
- 内存应在不再需要时释放以避免内存泄漏（对于练习可使用工具检测）

测试与自动化
----
- 每个题目下的 `tests/` 包含若干测试用例（输入/预期输出），以及一个可执行的测试驱动脚本或基于单元测试框架的测试程序。
- CI（GitHub Actions）可配置为在 PR 提交时自动运行 `make test` 并给出通过/失败状态。
- 可加入内存检测（Valgrind）作为可选检查：
  ```bash
  valgrind --leak-check=full ./bin/ex01 < input.txt
  ```

作业提交与评分（示例流程）
----
- 学生在 `src/` 下提交实现文件，提交 PR 或提交到指定作业分支。
- 自动化脚本 `tools/grade.sh` 拉取学生提交，运行编译与测试，生成评分报告（stdout / JSON）。
- 教师/助教可结合手工检查与自动化结果给出最终分数。

贡献指南
----
- 欢迎提交题目（issue -> discussion -> PR）。
- 新增题目请包含：题目描述、样例、至少 5 个测试用例（含边界/特殊情况）、参考解答（可选）。
- PR 格式：在 PR 描述中说明题目来源、难度、测试覆盖点。
- 请在提交前运行 `make test` 并确保所有现有测试通过。

常见问题
----
- Q: 我可以直接把整个题目目录 fork 用于教学吗？  
  A: 可以，遵循 LICENSE 的约定并保留原作者信息。

- Q: 评测是否包含性能测试？  
  A: 默认以正确性为主。若题目涉及时间/空间复杂度，会在题目描述中注明并提供相应测试。

许可证
----
本仓库采用 MIT 许可证，详见 LICENSE 文件。

联系方式
----
维护者: super-coder-ma  
邮箱/联系方式：请在仓库主页填写（或在 Issues 中联系）

示例题目列表（可放在仓库根 README 或 docs/）
----
- 基础：Hello World / 输入输出 / 条件判断
- 数组与字符串：数组求和、最大子数组、字符串反转、回文判断
- 指针：指针与数组、字符串函数重写（strlen、strcpy）
- 数据结构：链表基本操作、栈与队列实现
- 算法：排序（快速/归并）、二分查找、递归题目
- 进阶：文件 I/O、动态规划、位运算

附录：评分脚本示例（tools/grade.sh 简要）
```bash
#!/usr/bin/env bash
set -e
# 假设每题目录下有 tests/run_tests.sh
for d in exercises/*; do
  if [ -x "$d/tests/run_tests.sh" ]; then
    echo "Running tests in $d"
    (cd "$d" && ./tests/run_tests.sh) || { echo "Tests failed in $d"; exit 1; }
  fi
done
echo "All tests passed"
```

欢迎按需修改这个模板以适配你们课程或教学流程。
