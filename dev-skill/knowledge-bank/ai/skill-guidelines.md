# SKILL 编写规范

## Anthropic 标准 Skill 目录格式

每个 skill 是一个独立目录，目录里必须有一个 SKILL.md

最小可用结构就是：

```
skill-name/
└── SKILL.md
```

templates/、scripts/、references/、examples.md、reference.md 全部是可选的扩展。官方推荐的"完整"目录大概长这样：

```
skill-name/
├── SKILL.md          # 必需，主入口指令
├── reference.md      # 可选，详细参考资料
├── examples.md       # 可选，使用示例
└── scripts/          # 可选，工具脚本
    ├── validate.py
    └── helper.sh
```

标准里没有的目录，允许你自由扩展任何子目录，只要在 SKILL.md 中告诉 agent 该怎么用即可

## SKILL 内容格式

SKILL.md 的开头必须有 YAML 前置元数据（frontmatter）：

```md
---
name: skill-name # 必填，小写字母/数字/连字符，最长 64 字符
description: ... # 必填，最长 1024 字符
disable-model-invocation: true # 可选，用来决定这个 skill 是让 Agent 自动判断要不要用，ture 表示必须手动调用该技能
---
```

官方建议 SKILL 500 行以内。因为 SKILL 一旦被加载，全文都会进入上下文中，耗费 token。有一种渐进式披露的方案，SKILL 中脂肪启动时必要的指令，把详细资料脚本拆解到子文件中

eg：
|子目录|作用|SKILL 中怎么写|
|--|--|--|
|reference.md|相关资料|在 SKILL.md 里写"详细信息见 reference.md"，Agent 会按需读取|
|scripts/|预先写好的可执行脚本（Python/Bash 等）|SKILL.md 写"执行 python scripts/xxx.py 完成 Y"，比让 Agent 现场生成代码更可靠、更省 token|
|examples.md |输入/输出示例|让输出风格稳定|