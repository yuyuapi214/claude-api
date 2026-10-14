# GPT API、Claude API 国内接入完整指南：从注册到实战调用

想在国内调用 GPT API 或 Claude API？本文以最新的 GPT-6 Astra、GPT-5.6 系列、Claude Opus 5 和 Fable 5.1 为例，详细介绍从模型选择、账号注册、API 配置到实际调用的完整流程，并提供可直接运行的 `curl`、Python、Node.js 接入示例。

无论你是初次接触 API 的新手，还是希望优化成本和性能的开发者，都能从中找到实用的接入方案。

> **非官方声明**： 本项目为独立的第三方教程整理项目，与 OpenAI、Anthropic、GitHub 等公司不存在官方合作、授权或隶属关系。ChatGPT、OpenAI、Claude、Anthropic 等商标归各自权利人所有。
>
> 更新于 2026-10-14。如果对你有帮助的话，希望给一个小⭐️⭐️支持一下，本教程持续更新。

---

## 一、先选接入方式

国内开发者接入 GPT API、Claude API 主要有两种方式，各有优劣：

| 方式 | 需要准备 | 优势 | 劣势 | 适用情况 |
| --- | --- | --- | --- | --- |
| 官方 API | 开发者账号、API Key、海外支付方式、稳定网络环境 | 接口完整、功能最新、官方支持 | 注册门槛高、需要海外支付、网络不稳定 | 企业级应用、需要官方支持 |
| 兼容中转服务 | 服务商签发的 Token、Base URL | 注册简单、国内直连、价格优惠、支持多模型 | 功能可能滞后、需选择可靠服务商 | 个人开发者、中小企业、快速原型 |

### 中转服务的核心优势

1. **零门槛接入** - 无需海外信用卡，支持支付宝、微信支付
2. **国内直连** - 无需配置网络环境，稳定可靠
3. **统一接口** - 一个 Key 接入 GPT、Claude、Gemini、DeepSeek 等多模型
4. **价格优势** - 比官方便宜 20%-70%，按量计费，无最低消费
5. **余额永不过期** - 充值一次，永久可用

需要中转服务的话，推荐使用 [全智API - AI大模型API中转站](https://quanzil.com/)，支持国内外主流大模型，稳定运行 3 年+。

![中转服务接入示意图](assets/images/api-proxy-overview.png)

### 选择中转服务的注意事项

- ✅ **先小额测试** - 充值最小金额，测试模型响应和计费准确性
- ✅ **核对模型列表** - 确认所需模型已开放，查看实时价格
- ✅ **测试功能完整性** - 流式输出、函数调用、Vision 等关键功能
- ✅ **查看服务条款** - 了解数据隐私政策、SLA 保障
- ⚠️ **避免超低价格陷阱** - 价格过低可能意味着服务质量或稳定性问题

ChatGPT Plus、Claude 订阅与 API 用量分别计费；网页会员资格不能直接当作 API Key 使用。中转 Token 也不能登录官方产品。

---

## 二、主流模型对比与选择建议

### 2026 年热门模型一览

| 模型系列 | API 模型 ID | 特点 | 适用场景 | 价格参考（输入/输出） |
| --- | --- | --- | --- | --- |
| **GPT-6 Astra** | `gpt-6-astra` | 最新旗舰，推理能力顶尖 | 高难度推理、复杂任务编排 | $15/$45 每百万 token |
| **GPT-5.6 Luna** | `gpt-5.6-luna` | 轻量快速，性价比高 | 摘要、分类、简单问答 | $0.5/$1.5 每百万 token |
| **GPT-5.6 Terra** | `gpt-5.6-terra` | 平衡型，日常首选 | 编程、内容创作、日常应用 | $3/$9 每百万 token |
| **GPT-5.6 Sol** | `gpt-5.6-sol` | 专业型，深度任务 | 复杂代码、专业分析 | $6/$18 每百万 token |
| **Claude Opus 5** | `claude-opus-5` | 编程能力最强，200K 上下文 | 复杂编程、Agent 工作流 | $15/$75 每百万 token |
| **Claude Fable 5.1** | `claude-fable-5-1` | 超长推理，研究级 | 长流程推理、研究任务 | $25/$125 每百万 token |
| **Claude Sonnet 4.6** | `claude-sonnet-4-6` | 高性价比，200K 上下文 | 日常应用、长文本处理 | $3/$15 每百万 token |
| **DeepSeek V3** | `deepseek-chat` | 中文优秀，性价比极高 | 中文场景、代码生成 | $0.27/$1.1 每百万 token |
| **Gemini 3.5 Flash** | `gemini-3.5-flash` | 响应超快，多模态 | 高频调用、实时应用 | $0.075/$0.3 每百万 token |

### 按场景选择模型

#### 1. 编程开发
- **首选**：Claude Opus 5（代码质量最高）
- **性价比**：GPT-5.6 Terra 或 Claude Sonnet 4.6
- **高频场景**：DeepSeek V3（成本低，速度快）

#### 2. 内容创作
- **首选**：GPT-5.6 Terra（创意能力强）
- **专业写作**：Claude Opus 5（严谨专业）
- **快速生成**：GPT-5.6 Luna

#### 3. 数据分析与推理
- **首选**：GPT-6 Astra（推理能力最强）
- **复杂分析**：Claude Fable 5.1
- **日常分析**：GPT-5.6 Sol

#### 4. 中文场景
- **首选**：DeepSeek V3（中文优化，性价比最高）
- **复杂中文任务**：Claude Opus 5 或 GPT-5.6 Terra

#### 5. 长文本处理
- **首选**：Claude Sonnet 4.6（200K 上下文，性价比高）
- **复杂长文本**：Claude Opus 5 或 Fable 5.1

### 成本优化建议

1. **测试先用轻量模型** - 先用 Luna 或 Gemini Flash 测试，确认效果后再用高级模型
2. **合理设置参数** - temperature、max_tokens 等参数影响成本
3. **使用缓存** - 相同请求可缓存结果，避免重复调用
4. **监控用量** - 定期查看用量报表，优化高消耗环节

这只是选型起点，生产环境最好用自己的任务比较效果、延迟和每次完成任务的成本。[OpenAI 模型说明](https://developers.openai.com/api/docs/models)与[Claude 选型指南](https://platform.claude.com/docs/en/about-claude/models/choosing-a-model)提供最新定位。

---

## 三、中转站接入详细流程

### 步骤 1：注册账号

访问 [全智API](https://quanzil.com/)，使用邮箱或手机号快速注册，无需海外支付方式。

### 步骤 2：创建 API Key

进入后台管理页面，点击「创建新密钥」：

- ✅ 为密钥命名，方便管理多个项目
- ✅ 设置使用额度上限（可选）
- ✅ 复制并妥善保存密钥，**不要分享给他人**

### 步骤 3：充值与查看价格

- 进入「充值中心」，支持支付宝、微信支付
- 查看各模型实时价格，价格表会实时更新
- 建议先充值最小金额测试服务

### 步骤 4：获取接入信息

关键配置信息：

```
Base URL: https://quanzil.com/v1
API Key: sk-xxxxxxxxxxxxxx（你的密钥）
```

### 步骤 5：配置并测试

#### 方式 A：使用 OpenAI SDK（推荐）

Python 环境：

```bash
pip install openai
```

创建测试文件 `test_api.py`：

```python
from openai import OpenAI

client = OpenAI(
    api_key="sk-xxxxxxxxxxxxxx",  # 替换为你的密钥
    base_url="https://quanzil.com/v1"
)

# 测试连接
response = client.chat.completions.create(
    model="gpt-5.6-luna",  # 使用轻量模型测试
    messages=[{"role": "user", "content": "你好"}],
    max_tokens=50
)

print("✅ 连接成功！")
print(response.choices[0].message.content)
```

运行测试：

```bash
python test_api.py
```

#### 方式 B：使用 curl 测试

```bash
curl https://quanzil.com/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer sk-xxxxxxxxxxxxxx" \
  -d '{
    "model": "gpt-5.6-luna",
    "messages": [{"role": "user", "content": "测试连接"}],
    "max_tokens": 50
  }'
```

### 步骤 6：查看用量与账单

登录后台，可以实时查看：
- 各模型调用量
- Token 消耗明细
- 费用统计
- API 调用日志

### Claude Code 快捷配置

如果你使用 Claude Code，全智API 提供一键配置：

1. 按照官方文档安装 Claude Code
2. 运行配置命令：

```bash
quanzil install claude-code
```

3. 使用专属命令启动：

```bash
claude-quanzil
```

配置独立存储，不会影响原有的 `claude` 命令。

如需更换密钥：

```bash
quanzil reset claude-code
```

---

## 四、接入前后检查清单

### ✅ 接入前必须检查

| 检查项 | 说明 | 如何检查 |
| --- | --- | --- |
| **模型可用性** | 确认所需模型在平台已开放 | 查看模型列表页 |
| **实时价格** | 核对当前单价，避免价格变动 | 查看价格页 |
| **功能支持** | 确认流式输出、函数调用等功能可用 | 阅读文档或咨询客服 |
| **接口协议** | 确认兼容 OpenAI 格式还是其他格式 | 查看接入文档 |
| **并发限制** | 了解并发数限制，避免业务受限 | 查看服务条款 |

### ✅ 接入后验证测试

```python
# 1. 基础连通性测试
response = client.chat.completions.create(
    model="gpt-5.6-luna",
    messages=[{"role": "user", "content": "测试"}],
    max_tokens=10
)

# 2. 流式输出测试
stream = client.chat.completions.create(
    model="gpt-5.6-luna",
    messages=[{"role": "user", "content": "数到10"}],
    stream=True
)
for chunk in stream:
    if chunk.choices[0].delta.content:
        print(chunk.choices[0].delta.content, end="")

# 3. 用量计费验证
# 发送已知长度的文本，对比后台统计是否准确
```

### ⚠️ 常见错误排查

| 错误码 | 原因 | 解决方法 |
| --- | --- | --- |
| `401 Unauthorized` | API Key 错误或已过期 | 检查 Key 是否正确，是否被撤销 |
| `404 Not Found` | URL 路径或模型名错误 | 检查 Base URL、模型 ID 拼写 |
| `429 Too Many Requests` | 超过速率限制或余额不足 | 检查账户余额、降低请求频率 |
| `500 Internal Server Error` | 服务端异常 | 联系客服或稍后重试 |
| `503 Service Unavailable` | 服务暂时不可用 | 稍后重试，或查看服务状态页 |

### 🔒 安全最佳实践

1. **密钥管理**
   - ✅ 使用环境变量存储密钥：`export API_KEY="sk-xxx"`
   - ✅ 代码中使用：`api_key = os.getenv("API_KEY")`
   - ❌ 不要硬编码在代码中
   - ❌ 不要提交到 Git 仓库

2. **密钥泄露应对**
   - 立即在后台撤销泄露的密钥
   - 创建新密钥并更新配置
   - 检查用量是否有异常

3. **日常安全**
   - 定期更换密钥
   - 为不同项目创建独立密钥
   - 设置额度上限，防止意外消耗

---

## 五、实战代码示例

### 5.1 curl 示例

#### 基础调用

```bash
curl https://quanzil.com/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -d '{
    "model": "claude-opus-5",
    "messages": [
      {"role": "user", "content": "你好，请介绍一下你自己。"}
    ]
  }'
```

#### 带参数调用

```bash
curl https://quanzil.com/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -d '{
    "model": "gpt-6-astra",
    "messages": [
      {"role": "system", "content": "你是一个专业的Python开发者"},
      {"role": "user", "content": "写一个快速排序算法"}
    ],
    "temperature": 0.7,
    "max_tokens": 1000,
    "top_p": 0.9
  }'
```

---

### 5.2 Python 完整示例

#### 安装依赖

```bash
pip install openai
```

#### 基础调用

```python
from openai import OpenAI
import os

# 从环境变量读取密钥（推荐）
api_key = os.getenv("QUANZIL_API_KEY")

# 或者直接设置（不推荐在生产环境使用）
# api_key = "sk-xxxxxxxxxxxxxx"

# 初始化客户端
client = OpenAI(
    api_key=api_key,
    base_url="https://quanzil.com/v1"
)

# 发送请求
response = client.chat.completions.create(
    model="claude-opus-5",
    messages=[
        {"role": "user", "content": "你好，请介绍一下你自己。"}
    ]
)

print(response.choices[0].message.content)
```

#### 流式输出

```python
from openai import OpenAI

client = OpenAI(
    api_key="YOUR_API_KEY",
    base_url="https://quanzil.com/v1"
)

# 流式输出
stream = client.chat.completions.create(
    model="gpt-6-astra",
    messages=[
        {"role": "user", "content": "写一首关于AI的诗"}
    ],
    stream=True
)

print("AI回复：", end="")
for chunk in stream:
    if chunk.choices[0].delta.content:
        print(chunk.choices[0].delta.content, end="", flush=True)
print()  # 换行
```

#### 带上下文的对话

```python
from openai import OpenAI

client = OpenAI(
    api_key="YOUR_API_KEY",
    base_url="https://quanzil.com/v1"
)

# 维护对话历史
messages = [
    {"role": "system", "content": "你是一个友好的AI助手"},
]

while True:
    user_input = input("你: ")
    if user_input.lower() in ["退出", "exit", "quit"]:
        break
    
    # 添加用户消息
    messages.append({"role": "user", "content": user_input})
    
    # 调用 API
    response = client.chat.completions.create(
        model="gpt-5.6-terra",
        messages=messages
    )
    
    assistant_message = response.choices[0].message.content
    print(f"AI: {assistant_message}\n")
    
    # 添加助手回复到历史
    messages.append({"role": "assistant", "content": assistant_message})
```

#### 错误处理与重试

```python
from openai import OpenAI
import time

client = OpenAI(
    api_key="YOUR_API_KEY",
    base_url="https://quanzil.com/v1"
)

def call_api_with_retry(model, messages, max_retries=3):
    """带重试机制的 API 调用"""
    for attempt in range(max_retries):
        try:
            response = client.chat.completions.create(
                model=model,
                messages=messages
            )
            return response
        except Exception as e:
            print(f"第 {attempt + 1} 次尝试失败: {str(e)}")
            if attempt < max_retries - 1:
                time.sleep(2 ** attempt)  # 指数退避
            else:
                raise e

# 使用示例
try:
    response = call_api_with_retry(
        model="gpt-5.6-terra",
        messages=[{"role": "user", "content": "测试"}]
    )
    print(response.choices[0].message.content)
except Exception as e:
    print(f"API 调用失败: {str(e)}")
```

---

### 5.3 Node.js 示例

#### 安装依赖

```bash
npm install openai
```

#### 基础调用

```javascript
import OpenAI from 'openai';

const client = new OpenAI({
  apiKey: process.env.QUANZIL_API_KEY,
  baseURL: 'https://quanzil.com/v1'
});

async function main() {
  const response = await client.chat.completions.create({
    model: 'claude-opus-5',
    messages: [
      { role: 'user', content: '你好，请介绍一下你自己。' }
    ]
  });

  console.log(response.choices[0].message.content);
}

main();
```

#### 流式输出

```javascript
import OpenAI from 'openai';

const client = new OpenAI({
  apiKey: process.env.QUANZIL_API_KEY,
  baseURL: 'https://quanzil.com/v1'
});

async function streamChat() {
  const stream = await client.chat.completions.create({
    model: 'gpt-6-astra',
    messages: [
      { role: 'user', content: '写一首关于AI的诗' }
    ],
    stream: true
  });

  process.stdout.write('AI回复：');
  for await (const chunk of stream) {
    process.stdout.write(chunk.choices[0]?.delta?.content || '');
  }
  console.log(); // 换行
}

streamChat();
```

---

### 5.4 高级功能示例

#### 函数调用（Function Calling）

```python
from openai import OpenAI
import json

client = OpenAI(
    api_key="YOUR_API_KEY",
    base_url="https://quanzil.com/v1"
)

# 定义函数
functions = [
    {
        "name": "get_weather",
        "description": "获取指定城市的天气信息",
        "parameters": {
            "type": "object",
            "properties": {
                "city": {
                    "type": "string",
                    "description": "城市名称"
                }
            },
            "required": ["city"]
        }
    }
]

# 调用
response = client.chat.completions.create(
    model="gpt-5.6-terra",
    messages=[
        {"role": "user", "content": "北京今天天气怎么样？"}
    ],
    functions=functions,
    function_call="auto"
)

# 检查是否需要调用函数
message = response.choices[0].message
if message.function_call:
    function_name = message.function_call.name
    function_args = json.loads(message.function_call.arguments)
    print(f"需要调用函数: {function_name}")
    print(f"参数: {function_args}")
```

#### Vision 多模态（图像理解）

```python
from openai import OpenAI

client = OpenAI(
    api_key="YOUR_API_KEY",
    base_url="https://quanzil.com/v1"
)

# 图像 URL 或 base64
response = client.chat.completions.create(
    model="gpt-6-astra",
    messages=[
        {
            "role": "user",
            "content": [
                {"type": "text", "text": "这张图片里有什么？"},
                {
                    "type": "image_url",
                    "image_url": {
                        "url": "https://example.com/image.jpg"
                    }
                }
            ]
        }
    ],
    max_tokens=500
)

print(response.choices[0].message.content)
```

---

## 六、最佳实践与优化建议

### 6.1 成本优化

#### Token 使用优化

```python
# ❌ 不好的做法：发送过多冗余信息
messages = [
    {"role": "user", "content": "请帮我分析这篇文章：[粘贴 5000 字文章]"}
]

# ✅ 好的做法：提取关键信息
messages = [
    {"role": "user", "content": "请分析这篇文章的核心观点：[摘要或关键段落]"}
]
```

#### 模型选择策略

```python
# 根据任务复杂度选择模型
def get_model_for_task(task_type, complexity):
    """根据任务类型和复杂度选择最合适的模型"""
    
    if task_type == "simple_qa":
        return "gpt-5.6-luna"  # 简单问答用轻量模型
    
    elif task_type == "coding":
        if complexity == "high":
            return "claude-opus-5"  # 复杂编程任务
        else:
            return "gpt-5.6-terra"  # 日常编程
    
    elif task_type == "chinese":
        return "deepseek-chat"  # 中文场景优先 DeepSeek
    
    elif task_type == "analysis":
        return "gpt-6-astra"  # 复杂分析
    
    else:
        return "gpt-5.6-terra"  # 默认平衡型
```

### 6.2 性能优化

#### 并发控制

```python
import asyncio
from openai import AsyncOpenAI

client = AsyncOpenAI(
    api_key="YOUR_API_KEY",
    base_url="https://quanzil.com/v1"
)

async def process_batch(prompts, batch_size=5):
    """批量处理，控制并发数"""
    results = []
    
    for i in range(0, len(prompts), batch_size):
        batch = prompts[i:i+batch_size]
        
        tasks = [
            client.chat.completions.create(
                model="gpt-5.6-luna",
                messages=[{"role": "user", "content": prompt}]
            )
            for prompt in batch
        ]
        
        batch_results = await asyncio.gather(*tasks)
        results.extend([r.choices[0].message.content for r in batch_results])
        
        # 避免触发速率限制
        await asyncio.sleep(1)
    
    return results

# 使用示例
async def main():
    prompts = ["问题1", "问题2", "问题3", "问题4", "问题5"]
    results = await process_batch(prompts, batch_size=3)
    for prompt, result in zip(prompts, results):
        print(f"Q: {prompt}\nA: {result}\n")

# asyncio.run(main())
```

#### 缓存机制

```python
import hashlib
import json

# 简单缓存实现
cache = {}

def get_cache_key(model, messages):
    """生成缓存键"""
    content = json.dumps({"model": model, "messages": messages})
    return hashlib.md5(content.encode()).hexdigest()

def call_with_cache(model, messages, use_cache=True):
    """带缓存的 API 调用"""
    cache_key = get_cache_key(model, messages)
    
    # 检查缓存
    if use_cache and cache_key in cache:
        print("✅ 使用缓存结果")
        return cache[cache_key]
    
    # 调用 API
    response = client.chat.completions.create(
        model=model,
        messages=messages
    )
    
    result = response.choices[0].message.content
    
    # 存入缓存
    cache[cache_key] = result
    
    return result
```

### 6.3 错误处理最佳实践

```python
from openai import OpenAI, APIError, APIConnectionError, RateLimitError
import time
import logging

# 配置日志
logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

client = OpenAI(
    api_key="YOUR_API_KEY",
    base_url="https://quanzil.com/v1"
)

def robust_api_call(model, messages, max_retries=3, timeout=30):
    """健壮的 API 调用，包含完整的错误处理"""
    
    for attempt in range(max_retries):
        try:
            response = client.chat.completions.create(
                model=model,
                messages=messages,
                timeout=timeout
            )
            
            logger.info(f"API 调用成功，使用 {response.usage.total_tokens} tokens")
            return {
                "success": True,
                "content": response.choices[0].message.content,
                "usage": {
                    "prompt_tokens": response.usage.prompt_tokens,
                    "completion_tokens": response.usage.completion_tokens,
                    "total_tokens": response.usage.total_tokens
                }
            }
            
        except RateLimitError as e:
            logger.warning(f"速率限制，等待 {2 ** attempt} 秒后重试")
            time.sleep(2 ** attempt)
            
        except APIConnectionError as e:
            logger.error(f"网络连接错误: {str(e)}")
            if attempt < max_retries - 1:
                time.sleep(2)
            
        except APIError as e:
            logger.error(f"API 错误: {str(e)}")
            if e.status_code >= 500:
                # 服务端错误，可以重试
                time.sleep(2 ** attempt)
            else:
                # 客户端错误，不重试
                return {
                    "success": False,
                    "error": str(e),
                    "error_type": "client_error"
                }
        
        except Exception as e:
            logger.error(f"未知错误: {str(e)}")
            return {
                "success": False,
                "error": str(e),
                "error_type": "unknown"
            }
    
    return {
        "success": False,
        "error": "超过最大重试次数",
        "error_type": "max_retries_exceeded"
    }

# 使用示例
result = robust_api_call(
    model="gpt-5.6-terra",
    messages=[{"role": "user", "content": "测试"}]
)

if result["success"]:
    print(result["content"])
else:
    print(f"调用失败: {result['error']}")
```

### 6.4 监控与日志

```python
import time
from datetime import datetime

class APIMonitor:
    """API 调用监控"""
    
    def __init__(self):
        self.calls = []
    
    def log_call(self, model, success, tokens, duration):
        """记录调用"""
        self.calls.append({
            "timestamp": datetime.now(),
            "model": model,
            "success": success,
            "tokens": tokens,
            "duration": duration
        })
    
    def get_stats(self):
        """获取统计信息"""
        if not self.calls:
            return {}
        
        total_calls = len(self.calls)
        success_calls = sum(1 for c in self.calls if c["success"])
        total_tokens = sum(c["tokens"] for c in self.calls if c["success"])
        avg_duration = sum(c["duration"] for c in self.calls) / total_calls
        
        return {
            "total_calls": total_calls,
            "success_rate": success_calls / total_calls,
            "total_tokens": total_tokens,
            "avg_duration": avg_duration
        }

monitor = APIMonitor()

def call_with_monitor(model, messages):
    """带监控的 API 调用"""
    start_time = time.time()
    
    try:
        response = client.chat.completions.create(
            model=model,
            messages=messages
        )
        
        duration = time.time() - start_time
        tokens = response.usage.total_tokens
        
        monitor.log_call(model, True, tokens, duration)
        
        return response.choices[0].message.content
        
    except Exception as e:
        duration = time.time() - start_time
        monitor.log_call(model, False, 0, duration)
        raise e

# 查看统计
# print(monitor.get_stats())
```

---

## 七、常见问题 FAQ

### Q1: 中转站和官方 API 有什么区别？

**A:** 主要区别：
- **接入方式**：中转站无需海外支付和网络配置，国内直连
- **价格**：中转站通常更便宜（便宜 20%-70%）
- **功能**：功能基本相同，但最新功能可能延迟几天上线
- **稳定性**：选择可靠的中转站，稳定性有保障

### Q2: 中转站安全吗？数据会被泄露吗？

**A:** 选择正规中转站，数据安全有保障：
- 数据不落地，实时转发
- 有隐私政策和服务条款
- 建议不要发送高度敏感数据
- 可以加密后再发送

### Q3: 如何判断一个中转站是否靠谱？

**A:** 参考以下标准：
- ✅ 运营时间长（建议 2 年以上）
- ✅ 价格合理（不是越便宜越好）
- ✅ 文档完善，客服响应及时
- ✅ 用户评价好
- ✅ 支持小额测试

### Q4: API Key 泄露了怎么办？

**A:** 立即处理：
1. 登录后台，撤销泄露的 Key
2. 创建新 Key 并更新配置
3. 检查用量是否有异常消耗
4. 联系客服说明情况

### Q5: 如何选择合适的模型？

**A:** 根据场景选择：
- **编程开发**：Claude Opus 5 > GPT-5.6 Terra
- **日常应用**：GPT-5.6 Terra 或 Claude Sonnet 4.6
- **中文场景**：DeepSeek V3（性价比最高）
- **高频轻任务**：GPT-5.6 Luna 或 Gemini 3.5 Flash
- **复杂推理**：GPT-6 Astra 或 Claude Fable 5.1

### Q6: 流式输出和非流式有什么区别？

**A:**
- **非流式**：等待完整响应，适合批处理
- **流式**：实时输出，用户体验好，适合聊天应用

### Q7: 如何降低 API 调用成本？

**A:** 优化方法：
1. 选择合适的模型（不要过度使用高级模型）
2. 减少 prompt 长度，提取关键信息
3. 使用缓存，避免重复调用
4. 合理设置 `max_tokens` 参数
5. 监控用量，优化高消耗环节

### Q8: 支持哪些编程语言？

**A:** 所有支持 HTTP 请求的语言都可以，推荐：
- Python（OpenAI SDK）
- Node.js（OpenAI SDK）
- Go、Java、PHP 等（使用 HTTP 库）

---

## 八、资源链接

| 资源 | 说明 |
| --- | --- |
| [全智API 官网](https://quanzil.com/) | 注册与接入 |
| [模型价格页](https://quanzil.com/pricing) | 实时价格查询 |
| [接入文档](https://quanzil.com/docs) | 详细接入指南 |
| [OpenAI API 文档](https://platform.openai.com/docs) | 官方 API 参考 |
| [Claude API 文档](https://docs.anthropic.com/) | 官方 API 参考 |

---

## 免责声明

本教程为独立的第三方整理项目，与 OpenAI、Anthropic、GitHub 等公司不存在官方合作、授权或隶属关系。ChatGPT、OpenAI、Claude、Anthropic 等商标归各自权利人所有。

使用中转服务前，请仔细阅读平台的服务条款与隐私政策。本教程仅供参考，不构成任何投资或使用建议。
