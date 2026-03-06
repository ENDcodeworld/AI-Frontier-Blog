# AI API 注册操作指南

## 🎯 任务概述

为公司 AI 团队注册两个核心 API 服务：
1. **智谱 GLM-5** - 首席研发工程师
2. **Kimi2.5** - 首席分析师

---

## 📱 任务 1: 注册智谱 GLM-5 API

### 步骤 1: 访问官网
```
网址：https://open.bigmodel.cn
```

### 步骤 2: 创建账号
1. 点击右上角"注册"或"登录"按钮
2. 选择注册方式:
   - 手机号注册 (推荐)
   - 邮箱注册
   - 第三方登录 (微信/钉钉等)
3. 完成手机/邮箱验证

### 步骤 3: 实名认证
- 根据提示完成实名认证 (国内 API 服务必需)
- 准备身份证信息

### 步骤 4: 获取 API Key
1. 登录控制台
2. 进入"API 管理"或"密钥管理"
3. 点击"创建新密钥"
4. **重要:** 立即复制并安全保存 API Key (只显示一次)

### 步骤 5: 充值
1. 进入"充值"或"账单"页面
2. 选择充值金额: **¥50** (测试用)
3. 选择支付方式 (微信/支付宝/银行卡)
4. 完成支付

### 步骤 6: 验证
1. 查看账户余额确认充值成功
2. 记录以下信息:
   - API Key: `sk-xxxxxxxxxxxxxxxx`
   - 账户 ID
   - 余额

---

## 📱 任务 2: 注册 Kimi2.5 API

### 步骤 1: 访问官网
```
网址：https://platform.moonshot.cn
```

### 步骤 2: 创建账号
1. 点击右上角"注册"或"登录"按钮
2. 选择注册方式:
   - 手机号注册 (推荐)
   - 邮箱注册
3. 完成验证码验证

### 步骤 3: 实名认证
- 完成实名认证流程
- 准备身份证信息

### 步骤 4: 获取 API Key
1. 登录开放平台控制台
2. 进入"API Keys"页面
3. 点击"创建新 Key"
4. **重要:** 立即复制并安全保存 (只显示一次)

### 步骤 5: 充值
1. 进入"充值中心"
2. 选择充值金额: **¥50** (测试用)
3. 选择支付方式
4. 完成支付

### 步骤 6: 验证
1. 确认账户余额
2. 记录以下信息:
   - API Key: `sk-xxxxxxxxxxxxxxxx`
   - 账户信息
   - 余额

---

## 🔐 API Key 安全存储

### ⚠️ 重要安全提示
- API Key 等同于密码，**切勿泄露**
- 不要提交到 Git 仓库
- 不要分享給无关人员
- 定期轮换密钥

### 推荐存储方式

#### 方式 1: 环境变量 (开发环境)
```bash
# ~/.bashrc 或 ~/.zshrc
export ZHIPU_API_KEY="sk-your-key-here"
export MOONSHOT_API_KEY="sk-your-key-here"
```

#### 方式 2: .env 文件 (项目级)
```bash
# 项目根目录/.env
ZHIPU_API_KEY=sk-your-key-here
MOONSHOT_API_KEY=sk-your-key-here

# 确保.gitignore 包含.env
echo ".env" >> .gitignore
```

#### 方式 3: 密码管理器 (推荐)
- 1Password
- Bitwarden
- KeePass
- 苹果钥匙串

#### 方式 4: 云服务商密钥管理
- AWS Secrets Manager
- Azure Key Vault
- 阿里云密钥管理服务

---

## ✅ 验证清单

完成注册后，请确认以下事项:

- [ ] 智谱 AI 账号已创建
- [ ] 智谱 API Key 已获取并保存
- [ ] 智谱账户已充值 ¥50
- [ ] 月之暗面账号已创建
- [ ] Kimi API Key 已获取并保存
- [ ] Kimi 账户已充值 ¥50
- [ ] API Key 已安全存储
- [ ] 已更新 `ai-team-employees.md` 文档

---

## 🧪 测试 API (可选)

### 测试智谱 GLM-5
```bash
curl -X POST https://open.bigmodel.cn/api/paas/v4/chat/completions \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "glm-5",
    "messages": [{"role": "user", "content": "你好"}]
  }'
```

### 测试 Kimi2.5
```bash
curl -X POST https://api.moonshot.cn/v1/chat/completions \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "kimi-latest",
    "messages": [{"role": "user", "content": "你好"}]
  }'
```

---

## 📞 需要帮助?

### 智谱 AI 支持
- 官网：https://open.bigmodel.cn
- 文档：https://open.bigmodel.cn/docs
- 客服：官网在线客服

### 月之暗面支持
- 官网：https://platform.moonshot.cn
- 文档：https://platform.moonshot.cn/docs
- 客服：官网在线客服

---

**创建时间:** 2026-03-05  
**执行人:** 志哥 (COO)  
**协助:** HR 总监 (AI 代理)
