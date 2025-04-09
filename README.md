这个案例通过 **多包管理** + **依赖优化** + **统一工作流** 的典型组合，完整展示了 monorepo 的核心优势。以下是具体展示方式：

---

### 一、多包共存：集中管理

```bash
lerna-pnpm-demo/
├── packages/
│   ├── app/          # 应用包
│   └── utils/        # 工具包
```

- **展示点**：多个独立模块（包）共存于同一仓库
- **对比 polyrepo**：传统方式需要维护多个独立仓库，协作成本高

---

### 二、依赖共享：避免重复

#### 1. 公共依赖提升

```bash
node_modules/
├── lodash/          # 公共依赖提升到根目录
└── @demo/           # 本地包软链接
```

- **展示点**：所有子包共享根 `node_modules`，避免重复安装
- **优势**：节省磁盘空间，统一依赖版本

#### 2. 本地包引用

```json
// packages/app/package.json
{
  "dependencies": {
    "@demo/utils": "workspace:*"  # 直接引用本地包
  }
}
```

- **展示点**：通过 `workspace:*` 协议实现本地包即时引用
- **对比传统方式**：无需 `npm link` 或 `file:../utils` 路径引用

---

### 三、统一操作：批量执行

#### 1. 跨包命令执行

```bash
lerna run build     # 所有子包执行 build 命令
lerna run test      # 所有子包执行测试
```

- **展示点**：一键操作所有包（无需逐个进入目录执行）

#### 2. 依赖统一安装

```bash
pnpm add lodash -w          # 全局安装依赖
pnpm add react --filter app # 为指定包安装依赖
```

- **展示点**：精准控制依赖安装位置

---

### 四、版本协同：原子化更新

#### 1. 版本联动

```bash
lerna version        # 自动检测改动的包并提示版本号
lerna publish        # 统一发布所有更新的包
```

- **展示点**：相关联的包可以同步更新版本（例如工具包更新后，依赖它的应用包自动升级）

---

### 五、项目结构：标准化管理

```bash
├── .github/         # 统一 CI/CD 配置
├── packages/        # 所有业务模块
├── scripts/         # 公共脚本
└── docs/            # 统一文档
```

- **展示点**：基础设施/工具链集中管理，避免重复配置

---

### 六、典型工作流演示

#### 1. 开发流程

```bash
pnpm install         # 一次性安装所有依赖
pnpm build           # 构建所有子包
pnpm start           # 启动主应用
```

#### 2. 修改联动

1. 修改 `utils` 包的代码
2. 立即在 `app` 包中看到变更效果（无需手动重新安装依赖）

---

### 七、对比传统 polyrepo 模式

| 场景       | polyrepo 模式            | 本案例的 monorepo 模式  |
| ---------- | ------------------------ | ----------------------- |
| 代码复用   | 需要发布到 npm 再安装    | 直接 `workspace:*` 引用 |
| 依赖安装   | 每个仓库重复安装相同依赖 | 依赖提升共享            |
| 跨包修改   | 需切换多个仓库提交 PR    | 单次提交覆盖关联改动    |
| CI/CD 配置 | 每个仓库独立配置         | 统一配置+缓存优化       |
| 新人上手   | 需 clone 多个仓库        | 只需 clone 一个仓库     |

---

### 八、扩展场景演示

#### 1. 添加新包

```bash
mkdir packages/logger
pnpm add chalk --filter logger
```

#### 2. 跨包测试

```bash
lerna run test --since main  # 只测试 main 分支后有改动的包
```

#### 3. 依赖检查

```bash
pnpm licenses list           # 统一检查所有包的许可证
```

---

### 总结：本案例如何体现 monorepo 核心价值

1. **集中管理**：多包统一维护，降低协作成本
2. **依赖优化**：避免重复安装，提升磁盘/网络利用率
3. **原子操作**：跨包修改+版本发布一体化
4. **标准化**：统一配置/脚本/工作流
5. **高效开发**：本地包即时引用，修改实时生效

这种模式特别适合：

- 多模块强关联的项目（如组件库+示例应用）
- 需要频繁跨包修改的场景
- 追求基础设施统一化的中大型项目

<!-- 本地仓库关联远程仓库 -->

```bash
# 1. 关联远程仓库
git remote add origin https://github.com/你的用户名/lerna-pnpm-workplace.git

# 2. 首次提交代码
# 添加所有文件到暂存区（注意排除 .gitignore 的内容）
git add .
# 提交代码
git commit -m "初始提交: 初始化 Lerna + pnpm monorepo 项目"

# 3.推送代码到远程仓库
# 强制推送（如果远程仓库是新建的且为空）
git push -u origin main      # 如果默认分支是 main
git push -u origin master    # 如果默认分支是 master

# 如果遇到冲突（比如远程仓库已有 README 等文件）：
git pull origin main --rebase  # 先拉取并合并
git push -u origin main

# 4. 验证关联状态
# 查看远程仓库配置
git remote -v
# 应该显示：
# origin  git@github.com:你的用户名/lerna-pnpm-workplace.git (fetch)
# origin  git@github.com:你的用户名/lerna-pnpm-workplace.git (push)

# 查看分支关联
git branch -vv
# 应该显示本地分支跟踪了远程分支

```
