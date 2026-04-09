# API替换指南 - 从Anthropic切换到DeepSeek

## 概述

当前版本使用的是Anthropic Claude API。如果你想使用DeepSeek API，可以按照本指南进行修改。

---

## 为什么提供两种API选择？

### Anthropic Claude API
**优点：**
- ✅ 生成质量极高
- ✅ 英文写作能力强
- ✅ 遵循指令准确
- ✅ 输出格式规范

**缺点：**
- ❌ 需要国际支付（信用卡）
- ❌ 可能需要VPN访问
- ❌ 收费（有免费额度）

### DeepSeek API
**优点：**
- ✅ 支持国内支付
- ✅ 国内访问稳定
- ✅ 价格相对便宜
- ✅ 中文支持好

**缺点：**
- ❌ 英文能力可能稍弱
- ❌ 需要自己测试效果

---

## DeepSeek API使用准备

### 步骤1：注册DeepSeek账号
1. 访问 https://platform.deepseek.com
2. 注册账号（手机号或邮箱）
3. 实名认证（如需要）

### 步骤2：获取API密钥
1. 登录后台
2. 进入"API密钥"页面
3. 创建新密钥
4. 复制保存密钥

### 步骤3：充值（如需要）
- 查看定价信息
- 根据需要充值
- 通常有新用户免费额度

---

## 代码修改指南

### 方法1：完整替换（推荐给初学者）

我会为你创建一个新版本 `essay-generator-deepseek.html`，已经配置好DeepSeek API。

**主要修改点：**

**原始代码（Anthropic）：**
```javascript
const response = await fetch('https://api.anthropic.com/v1/messages', {
    method: 'POST',
    headers: {
        'Content-Type': 'application/json',
        'x-api-key': apiKey,
        'anthropic-version': '2023-06-01'
    },
    body: JSON.stringify({
        model: 'claude-sonnet-4-20250514',
        max_tokens: 2000,
        messages: [{
            role: 'user',
            content: prompt
        }]
    })
});

const data = await response.json();
const essay = data.content[0].text;
```

**修改后（DeepSeek）：**
```javascript
const response = await fetch('https://api.deepseek.com/v1/chat/completions', {
    method: 'POST',
    headers: {
        'Content-Type': 'application/json',
        'Authorization': `Bearer ${apiKey}`
    },
    body: JSON.stringify({
        model: 'deepseek-chat',
        messages: [{
            role: 'user',
            content: prompt
        }],
        max_tokens: 2000,
        temperature: 0.7
    })
});

const data = await response.json();
const essay = data.choices[0].message.content;
```

### 方法2：自己动手修改

**找到代码中的这部分（大约在第353行）：**

```javascript
const response = await fetch('https://api.anthropic.com/v1/messages', {
```

**替换为：**

```javascript
const response = await fetch('https://api.deepseek.com/v1/chat/completions', {
```

**找到headers部分：**
```javascript
headers: {
    'Content-Type': 'application/json',
    'x-api-key': apiKey,
    'anthropic-version': '2023-06-01'
},
```

**替换为：**
```javascript
headers: {
    'Content-Type': 'application/json',
    'Authorization': `Bearer ${apiKey}`
},
```

**找到body部分：**
```javascript
body: JSON.stringify({
    model: 'claude-sonnet-4-20250514',
    max_tokens: 2000,
    messages: [{
        role: 'user',
        content: prompt
    }]
})
```

**替换为：**
```javascript
body: JSON.stringify({
    model: 'deepseek-chat',
    messages: [{
        role: 'user',
        content: prompt
    }],
    max_tokens: 2000,
    temperature: 0.7
})
```

**找到数据提取部分：**
```javascript
const data = await response.json();
const essay = data.content[0].text;
```

**替换为：**
```javascript
const data = await response.json();
const essay = data.choices[0].message.content;
```

---

## 提示词优化（针对DeepSeek）

DeepSeek对中文提示理解更好，可以优化提示词：

**原始提示词：**
```javascript
let prompt = `Write a high-quality English ${essayTypeMap[essayType]} on the topic: "${topic}".\n\n`;
prompt += `Requirements:\n`;
prompt += `- Length: approximately ${wordCount} words\n`;
// ...
```

**优化后（混合中英文）：**
```javascript
let prompt = `请为中国高中生写一篇高质量的英文${essayTypeMap[essayType]}，主题是："${topic}"\n\n`;
prompt += `要求：\n`;
prompt += `- 字数：大约${wordCount}词\n`;
prompt += `- 水平：适合中国高中生\n`;
prompt += `- 结构：清晰的引言、主体段落和结论\n`;
prompt += `- 语言：使用多样的词汇和句式\n`;
if (additionalInfo) {
    prompt += `- 补充要求：${additionalInfo}\n`;
}
prompt += `\n请直接输出完整的英文作文，不要有任何中文解释或前言。`;
```

---

## 完整的DeepSeek版本

我现在为你创建一个完整的DeepSeek版本文件。

---

## 测试和对比

### 测试步骤
1. 准备相同的主题
2. 分别用两个版本生成
3. 对比质量：
   - 语言流畅度
   - 内容相关性
   - 结构合理性
   - 词汇丰富度

### 选择建议

**选择Anthropic，如果：**
- 你能获取API密钥
- 追求最高质量
- 网络条件允许
- 预算充足

**选择DeepSeek，如果：**
- 国内访问更方便
- 支付更便捷
- 成本考虑
- 中文提示需求

---

## 混合使用方案

### 方案1：双API版本
创建两个HTML文件：
- `essay-generator-claude.html` - 使用Claude API
- `essay-generator-deepseek.html` - 使用DeepSeek API

用户可以根据情况选择使用。

### 方案2：API选择器
在界面添加API选择功能：
```html
<select id="apiProvider">
    <option value="anthropic">Anthropic Claude</option>
    <option value="deepseek">DeepSeek</option>
</select>
```

然后在代码中根据选择调用不同的API。

---

## 常见问题

### Q: 两种API生成质量差异大吗？
**A:** Claude通常在英文写作方面表现更好，但DeepSeek也足够应对高中英语作文。建议实测对比。

### Q: 可以免费使用吗？
**A:** 
- Anthropic：新用户有免费额度（$5）
- DeepSeek：通常也有新用户额度，具体查看官网

### Q: 哪个更便宜？
**A:** DeepSeek通常价格更低，但具体价格以官网为准。

### Q: 需要VPN吗？
**A:** 
- Anthropic：可能需要（国内访问）
- DeepSeek：不需要（国内服务）

### Q: 参赛用哪个好？
**A:** 建议：
1. 技术实现和功能演示都可以
2. 重点在于创新和实用性
3. 选择你能稳定访问的
4. 在文档中说明技术选型理由

---

## 参赛建议

如果你选择使用DeepSeek：

**在项目文档中说明：**
1. **技术选型理由**
   - 国内访问稳定
   - 支持国内支付
   - 适合本地化需求

2. **创新点不变**
   - AI技术应用于教育
   - 用户体验优化
   - 功能设计合理

3. **技术可行性**
   - 实际可用
   - 成本可控
   - 易于维护

**两个版本都准备：**
- 主要版本用DeepSeek（稳定访问）
- 备用版本用Claude（技术演示）
- 说明两者差异和选择依据

---

**需要我创建DeepSeek版本的代码吗？** 

只需要告诉我，我会立即生成 `essay-generator-deepseek.html` 文件。
