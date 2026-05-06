# 知行合一 (Unity of Knowledge and Action)

减少常见LLM编码错误的行为准则，来源于 Andrei Karpathy 对LLM编码陷阱的观察，并结合工程实践原则扩展。

**适用场景**：在编写、审查或重构代码时使用，以避免过度复杂化、进行外科式修改、明确假设，并定义可验证的成功标准。

**权衡**：这些准则更偏向谨慎而非速度。对于简单任务，请自行判断。



![1b79f227-fadb-40bb-8c40-7f5fd83379d5](./.imgs/1b79f227-fadb-40bb-8c40-7f5fd83379d5.png)

## 核心原则

### 1. 编码前先思考（存疑必问）

**不要假设，不要掩饰困惑，要明确权衡。**

在实现之前：
- 明确陈述你的假设。如果不确定，就提问。
- 如果存在多种理解，全部列出——不要默默选择一种。
- 如果有更简单的方法，直接说明。在必要时提出反对意见。
- 如果有不清楚的地方，停止。指出困惑之处并提问。

### 2. 简单优先（极简求解）

**用最少的代码解决问题。不做任何推测性扩展。**

- 不添加未被要求的功能。
- 不为一次性代码做抽象。
- 不增加未被请求的"灵活性"或"可配置性"。
- 不为不可能发生的情况添加错误处理。
- 如果你写了200行但可以用50行完成，那就重写。

问自己："一位资深工程师会觉得这过度复杂吗？"如果是，简化它。

### 3. 借势复用

**优先使用成熟方案，避免重复造轮子。**

- 优先采用稳定的开源库或已有实现。
- 不重新实现已有成熟解决方案。
- 评估引入成本与收益，避免过度依赖。
- 若选择自研，需明确理由。

### 4. 顺势而为

**依托现有环境与存量资源，因地制宜。**

- 优先复用现有架构、工具链与约定。
- 避免引入与当前系统不兼容的新模式。
- 在现有约束内寻找最优解，而非理想解。
- 尊重上下文，而不是重建上下文。

### 5. 外科式修改（最小改动）

**只修改必要部分。只清理你自己造成的问题。**

修改现有代码时：
- 不要"顺便优化"相邻代码、注释或格式。
- 不要重构没有问题的部分。
- 保持现有风格，即使你有不同偏好。
- 如果发现无关的死代码，可以指出——但不要删除。

当你的修改产生遗留问题时：
- 删除因你的修改而变得未使用的导入/变量/函数。
- 不要删除已有的死代码，除非被要求。

测试标准：每一行修改都应直接对应用户的需求。

### 6. 目标锚定（Goal-Driven Execution）

**先对齐背景与目标，再执行。**

在复杂任务或潜在破坏性操作前：
- 输出「背景、目标、执行计划」
- 等待用户确认后再执行

将任务转化为可验证目标：
- "添加校验" → "为非法输入编写测试，然后让其通过"
- "修复bug" → "写一个能复现问题的测试，然后让其通过"
- "重构X" → "确保重构前后测试都通过"

对于多步骤任务，给出简要计划：
```
1. [步骤] → 验证：[检查]
2. [步骤] → 验证：[检查]
3. [步骤] → 验证：[检查]
```

强有力的成功标准可以让你独立推进。弱标准（如"让它能运行"）则需要不断澄清。

### 7. 文档优先

**关键决策与结果必须可追溯、可复用。**

- 自动记录关键决策、假设与权衡
- 结构化输出重要结果
- 保证后续可以理解与复现过程
- 为未来维护者提供上下文，而不是谜题

## 安装

**选项 A：Claude Code 插件（推荐）**

在 Claude Code 中，首先添加插件市场：
```
/plugin marketplace add sweetwisdom/haichao-skills
```

然后安装插件：
```
/plugin install unity-of-knowledge-and-action@haichao-skills
```

这会将指南安装为 Claude Code 插件，使其在你所有项目中可用。

**选项 B：CLAUDE.md（按项目）**

新项目：
```bash
curl -o CLAUDE.md https://raw.githubusercontent.com/sweetwisdom/haichao-skills/main/CLAUDE.md
```

已有项目（追加）：
```bash
echo "" >> CLAUDE.md
curl https://raw.githubusercontent.com/sweetwisdom/haichao-skills/main/CLAUDE.md >> CLAUDE.md
```



### 作为通用行为准则

可以直接引用这些原则，或将其集成到你的开发流程中：

1. **代码审查清单**：将这些原则作为代码审查的检查项
2. **AI 辅助开发**：在使用 AI 编程工具时，将这些原则作为提示词的一部分
3. **团队规范**：作为团队编码规范的补充

## 使用示例

### 示例 1：避免过度复杂化

**需求**：添加一个用户验证函数

**违反原则的做法**：
```typescript
// 过度设计：引入了不必要的抽象和配置
interface ValidationConfig {
  rules: ValidationRule[];
  errorHandler: ErrorHandler;
  logger: Logger;
}

class UserValidator {
  constructor(private config: ValidationConfig) {}
  
  async validate(user: User): Promise<ValidationResult> {
    // 复杂的验证逻辑...
  }
}
```

**遵循原则的做法**：
```typescript
// 简单直接：只做必要的验证
function validateUser(user: User): boolean {
  return user.email.includes('@') && user.name.length > 0;
}
```

### 示例 2：外科式修改

**场景**：修复一个 bug，但发现附近有可以优化的代码

**违反原则的做法**：
```typescript
// 顺便"优化"了其他代码
function processOrder(order: Order) {
  // 修复的 bug
  if (!order.items) return null;
  
  // 顺便重构了这段代码（不应该）
  const total = order.items.reduce((sum, item) => {
    return sum + item.price * item.quantity;
  }, 0);
  
  // 顺便添加了错误处理（不应该）
  try {
    return calculateDiscount(total);
  } catch (error) {
    logger.error('Discount calculation failed', error);
    return total;
  }
}
```

**遵循原则的做法**：
```typescript
// 只修复 bug，不改动其他代码
function processOrder(order: Order) {
  if (!order.items) return null;
  
  const total = order.items.reduce((sum, item) => {
    return sum + item.price * item.quantity;
  }, 0);
  
  return calculateDiscount(total);
}
```

### 示例 3：目标锚定

**需求**：重构用户管理模块

**遵循原则的做法**：
```
背景：用户管理模块代码重复率高，维护困难
目标：将重复代码提取为公共函数，保持现有功能不变
执行计划：
1. 分析现有代码，识别重复模式 → 验证：列出所有重复代码
2. 提取公共函数 → 验证：所有现有测试仍然通过
3. 更新调用点 → 验证：功能行为与重构前一致
4. 添加单元测试 → 验证：覆盖率达到 80%
```

## 适用场景

- **AI 辅助开发**：使用 GitHub Copilot、Cursor、Claude 等 AI 工具时
- **代码审查**：作为代码审查的指导原则
- **重构任务**：在进行代码重构时遵循
- **新功能开发**：在实现新功能时避免过度设计
- **Bug 修复**：在修复 bug 时保持最小改动

## 最佳实践

### 对于个人开发者

1. **代码编写前**：问自己"这是最简单的解决方案吗？"
2. **代码编写中**：定期检查是否偏离了核心需求
3. **代码编写后**：审查是否有不必要的复杂性

### 对于团队

1. **代码审查**：将这些原则作为审查清单
2. **技术讨论**：在讨论技术方案时引用这些原则
3. **新人培训**：作为开发规范的一部分

### 对于 AI 辅助开发

1. **提示词优化**：在提示词中明确这些原则
2. **输出审查**：检查 AI 生成的代码是否遵循这些原则
3. **迭代改进**：根据反馈调整提示词

## 常见陷阱

### 1. 过度抽象
- **症状**：为一次性的需求创建复杂的抽象
- **解决方案**：问自己"这个抽象会被使用几次？"

### 2. 推测性编程
- **症状**：为未来可能的需求添加功能
- **解决方案**：只实现当前明确的需求

### 3. 风格不一致
- **症状**：在修改代码时顺便改变代码风格
- **解决方案**：保持现有风格，除非被要求改变

### 4. 忽略上下文
- **症状**：引入与现有系统不兼容的模式
- **解决方案**：先了解现有架构和约定

## 贡献

欢迎贡献！请遵循以下步骤：

1. Fork 项目
2. 创建功能分支 (`git checkout -b feature/amazing-feature`)
3. 提交更改 (`git commit -m 'Add amazing feature'`)
4. 推送到分支 (`git push origin feature/amazing-feature`)
5. 创建 Pull Request

### 贡献指南

- **原则补充**：如果发现新的编码原则，欢迎添加
- **示例完善**：为现有原则提供更多示例
- **文档改进**：改进文档的清晰度和完整性
- **翻译支持**：帮助翻译到其他语言

## 许可证

MIT License - 详见 [LICENSE](LICENSE) 文件

## 相关资源

- [Andrei Karpathy 的推文](https://twitter.com/karpathy/status/1617979122625712128) - 原始灵感来源
- [Clean Code](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882) - Robert C. Martin 的经典著作
- [The Pragmatic Programmer](https://www.amazon.com/Pragmatic-Programmer-Journey-Mastery/dp/0135957052) - David Thomas 和 Andrew Hunt 的实用指南

## 致谢

- Andrei Karpathy - 提供了原始的观察和灵感
- 所有贡献者和反馈者

---

**记住**：好的代码不是写得最多的代码，而是解决问题所需的最少代码。
