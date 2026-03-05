# 发布指南

本文档描述将 `@gwokhou/eslint-config` 发布到 npm 的完整流程。

---

## 前置条件

### 1. npm 账号与组织

1. 确保已拥有 npm 账号，并在 [npmjs.com](https://www.npmjs.com) 上创建了 `@gwokhou` 组织（Organization）
2. 将自己的账号加入该组织，并拥有 **Publish** 权限

### 2. 本地登录 npm

```bash
npm login
```

按提示输入用户名、密码和邮箱（或通过浏览器完成 OTP 验证）。验证登录状态：

```bash
npm whoami
# 输出你的 npm 用户名
```

### 3. 依赖检查

确保本地依赖是最新的：

```bash
pnpm install
```

---

## 发布步骤

### 第一步：运行测试

在发布前确保所有测试通过：

```bash
pnpm test
```

### 第二步：类型检查

```bash
pnpm typecheck
```

### 第三步：构建

```bash
pnpm build
```

构建产物会输出到 `dist/` 目录。`package.json` 中配置了 `"prepack": "nr build"`，执行 `pnpm publish` 时会自动触发构建，但建议手动先验证一次。

> **发布内容**：根据 `package.json` 的 `files` 字段，只有 `bin/` 和 `dist/` 目录会被打包上传。

### 第四步：更新版本号

使用项目内置的 [bumpp](https://github.com/antfu/bumpp) 交互式提升版本号：

```bash
pnpm release
```

该命令会：

- 询问新版本号（patch / minor / major / 自定义）
- 自动更新 `package.json` 中的 `version` 字段
- 创建 git tag 并提交

### 第五步：发布到 npm

**首次发布**（scoped 包默认为私有，需要显式指定为公开）：

```bash
pnpm publish --access public
```

**后续发布**（版本已是公开包，可省略 `--access public`）：

```bash
pnpm publish
```

---

## 发布后验证

确认包已成功发布：

```bash
npm info @gwokhou/eslint-config
```

浏览器访问：[https://www.npmjs.com/package/@gwokhou/eslint-config](https://www.npmjs.com/package/@gwokhou/eslint-config)

---

## 常见问题

### `403 Forbidden` / `You do not have permission to publish`

- 确认已登录（`npm whoami`）
- 确认账号在 `@gwokhou` 组织中拥有发布权限

### `402 Payment Required`

- scoped 包发布到 npm 公共注册表必须使用 `--access public`，否则 npm 认为你要发布私有包（需要付费）

### `ENEEDAUTH` 未登录

```bash
npm login
```

### 发布了错误版本，想撤回

npm 允许在发布后 **72 小时内** 撤回某个版本：

```bash
npm unpublish @gwokhou/eslint-config@<version>
```

超过 72 小时只能使用 `npm deprecate` 标记废弃：

```bash
npm deprecate @gwokhou/eslint-config@<version> "该版本存在问题，请升级到最新版本"
```

---

## 完整发布流程速查

```bash
# 1. 测试 & 检查
pnpm test && pnpm typecheck

# 2. 版本号升级（交互式）
pnpm release

# 3. 推送 git tag
git push --follow-tags

# 4. 发布（首次加 --access public）
pnpm publish --access public
```
