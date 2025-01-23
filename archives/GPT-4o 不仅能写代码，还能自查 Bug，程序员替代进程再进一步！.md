随着人工智能（AI）技术的不断进步，AI 在编程领域的应用取得了显著成果。通过自然语言处理（NLP）和机器学习（ML）技术，AI 可以自动生成代码、检测错误并优化性能。

一个典型的例子是 OpenAI 的 Codex 模型，它能够根据自然语言描述生成相应的代码片段，极大地提高了编程效率。

👉 [WildCard | 一分钟注册，轻松订阅海外线上服务](https://bit.ly/bewildcard)

## 1 CriticGPT

最近，OpenAI 发布了一个名为 **CriticGPT** 的新模型。该模型基于 GPT-4 训练，专门用于审查由 ChatGPT 编写的代码，并捕捉其中的错误。

### 示例：路径安全检查

用户提交的需求是编写一个 Python 函数，该函数接受一个表示文件路径的字符串，并返回该文件的文件对象，同时禁止访问目录 `/safedir` 之外的文件。

GPT 给出的代码如下：

python
import os

def open_file(file_path):
    safe_dir = "/safedir"
    absolute_file_path = os.path.abspath(file_path)
    if not absolute_file_path.startswith(safe_dir):
        raise ValueError("Access denied.")
    return open(absolute_file_path, "r")


CriticGPT 提出了改进建议：

> 检查文件绝对路径是否在目录内时，使用 `startswith()` 存在安全风险。建议使用 `os.path.commonpath([absolute_file_path, absolute_safe_dir])` 或其他更健壮的路径检查方法来确保安全性。

通过 CriticGPT 的帮助，用户可以更安全地实现路径检查功能，避免潜在的安全漏洞。

### CriticGPT 的优势

CriticGPT 能够帮助人类训练者在基于人类反馈的强化学习（RLHF）期间发现错误。数据显示，使用 CriticGPT 的人类训练师在审查效果上比没有获得帮助的人强 60%。

#### 综合性（Comprehensiveness）

CriticGPT 提供的批评意见更加全面，能够覆盖更多潜在问题。

#### 幻觉问题（Hallucinates a problem）

CriticGPT 在错误识别方面表现更为精准，减少了错误地识别问题的概率。

然而，CriticGPT 也存在一些限制，例如在处理复杂任务时可能会产生幻觉错误，导致训练师标记不准确。此外，对于极为复杂的任务，即使是受到模型辅助的专家也可能无法正确评估。

## 2 其他 CriticGPT 案例

以下是一个安全的 Python 函数示例，用于返回指定路径的文件对象，同时确保文件在指定的安全目录内：

python
import os

def open_file_secure(file_path):
    safe_dir = "/safedir"
    absolute_file_path = os.path.abspath(file_path)
    absolute_safe_dir = os.path.abspath(safe_dir)
    if not os.path.commonpath([absolute_file_path, absolute_safe_dir]) == absolute_safe_dir:
        raise ValueError("Access denied.")
    return open(absolute_file_path, "r")


CriticGPT 提出了以下改进建议：

- 路径检查方法的安全性：建议使用更健壮的路径检查方法。
- 文件打开模式的选择：确保文件操作的安全性。
- 错误处理的安全性：在处理异常时提供更详细的错误信息。

### 加密与解密示例

以下是一段包含加密和解密函数的 Python 代码示例：

python
def encrypt(message, key):
    # 加密逻辑
    pass

def decrypt(ciphertext, key):
    # 解密逻辑
    pass


CriticGPT 的建议包括：

- 验证消息的完整性和真实性。
- 改进错误处理逻辑，特别是在字符串编码和解码时。

CriticGPT 的批评意见能够帮助开发者更好地优化代码，但也需要注意其偶尔可能出现的幻觉错误。

👉 [WildCard | 一分钟注册，轻松订阅海外线上服务](https://bit.ly/bewildcard)