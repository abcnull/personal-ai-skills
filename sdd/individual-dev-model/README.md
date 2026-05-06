# 个人怎么用 SDD+TDD 让 AI 主导项目开发

## 简介
这篇文档非 skill，只是为了记录 `sdd/individual-dev-model` 目录下的其他 skill 对于个人开发者来说具体怎么使用。如下有具体使用方式

## 前置环节
trae 中的「技能与命令」中配置好 `prd-inquiry.md`,`dev-design-requirement.md`,`dev-requirement.md`

trae 中的「智能体」中配置好 `general-professional-test-engineer.md` 这个测试智能体

## 具体步骤
1. 人工编写完整的 `doc/prd.md` 需求
2. 人工编写尽量全面的 `doc/testcase.md` 测试用例
3. 对话框编写提示词，让找出需求的问题：
    ```md
    /prd-inquiry 帮我看看这个 `doc/prd.md` 需求文档存在什么问题？
    ```
5. 等待智能体回复后，人工维护 `doc/prd.md` 和 `doc/testcase.md` 文件
6. 对话框编写提示词，让产出技术方案文档：
    ```md
    /dev-design-requirement 技能，根据 `doc/prd.md` 需求文档来产出技术方案文档。
    ```
    如果你有想要指定的技术栈，你需要在提示词中来指定，这是推荐的方式！
7. 等待智能体完成后，看看 `doc/dev-design.md` 是否符合自己的要求并作维护
8. 对话框编写提示词，让进行技术开发：
    ```md
    /spec
    
    使用 dev-requirement 的技能，根据 `doc/prd.md` 需求文档和 `doc/dev-design.md` 技术方案文档等内容来进行长线的开发任务。注意：严格按照 dev-requirement 的要求进行。
    ```


