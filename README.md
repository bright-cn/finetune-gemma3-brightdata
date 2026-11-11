# ✨ finetune-gemma3-bright

> 自动采集、处理和转换 Trustpilot 客户评论，将其制作成高质量的 QA 数据集，用于微调 [Gemma 3](https://ai.google.dev/gemma) 和类似的 LLM。

---

## 🚀 概述

此仓库提供完整的工作流，用于：

1. 使用 [Bright Data API](https://docs.brightdata.com/api-reference/introduction) **采集 Trustpilot 客户评论**。
2. **将评论转换为 Markdown**，便于查看或分享。
3. **使用 OpenAI LLM 对评论进行分块和优化**，提高连贯性和质量。
4. **生成富有洞察力的 QA 对**，适用于微调客户服务或产品反馈模型。
5. **将数据集直接上传**到 [Hugging Face Hub](https://huggingface.co/)，并划分训练集和测试集。

---

## 🏗️ 目录结构

- `collect_reviews.py`：通过 Bright Data 下载 Trustpilot 评论。
- `json_to_md.py`：将评论 JSON 转换为 Markdown 格式。
- `md_chunker.py`：将 Markdown 拆分并清理为多个分块，再使用 LLM 进行优化。
- `generate_qa_pairs.py`：使用 OpenAI 从评论分块中生成问答对。
- `upload_to_hf.py`：验证结果并将其作为 Hugging Face 数据集上传。

---

## 📦 安装

**要求：** Python 3.8+  
**推荐：** [virtualenv](https://docs.python.org/3/tutorial/venv.html)

```bash
git clone https://github.com/youraccount/finetune-gemma3-bright.git
cd finetune-gemma3-bright
pip install -r requirements.txt
```

### 🔑 环境设置

1. 创建一个 `.env` 文件并添加：

   ```
   OPENAI_API_KEY=your-openai-key
   HF_TOKEN=your-huggingface-key
   ```

2. 在 `collect_reviews.py` 中更新你的 Bright Data API 密钥和数据集 ID。

---

## 📚 主要依赖项

- [requests](https://docs.python-requests.org/)：稳健的 HTTP 请求
- [python-dotenv](https://github.com/theskumar/python-dotenv)：管理密钥
- [openai](https://platform.openai.com/docs/api-reference)：LLM 补全和聊天 API
- [tenacity](https://tenacity.readthedocs.io/)：重试机制
- [langchain-text-splitters](https://python.langchain.com/docs/modules/data_connection/document_transformers/)：高级文本分块
- [数据集](https://huggingface.co/docs/datasets/)：快速处理 ML/NLP 数据
- [huggingface-hub](https://huggingface.co/docs/huggingface_hub/)：无缝上传模型和数据

---

## 🔄 评论采集流程

1. **首先采集评论**

   ```bash
   python collect_reviews.py
   ```

   - 触发对 Trustpilot 页面进行 Bright Data [快照采集](https://docs.brightdata.com/api-reference/web-scraper-api/trigger-a-collection)。
   - 持续轮询，直到评论准备就绪，然后将其保存到 `trustpilot_reviews.json`。

2. **将 JSON 评论转换为美观的 Markdown**

   ```bash
   python json_to_md.py --input trustpilot_reviews.json --output trustpilot_reviews.md
   ```

3. **对 Markdown 进行分块，使用 GPT 提高连贯性，并保存为 JSONL**

   ```bash
   python md_chunker.py --input trustpilot_reviews.md --output trustpilot_reviews_chunks.jsonl
   ```

4. **使用 OpenAI 生成问答对**

   ```bash
   python generate_qa_pairs.py --input trustpilot_reviews_chunks.jsonl --output trustpilot_qa_pairs.json
   ```

5. **将数据集上传到 Hugging Face Hub 🚀**

   ```bash
   python upload_to_hf.py --input trustpilot_qa_pairs.json --repo yourusername/trustpilot-reviews-qa-dataset
   ```

---

## 📝 工作流示例

```bash
# Step 1: Collect data
python collect_reviews.py

# Step 2: Markdown conversion
python json_to_md.py

# Step 3: Chunk and enhance
python md_chunker.py

# Step 4: QA generation
python generate_qa_pairs.py

# Step 5: Upload to Hugging Face
python upload_to_hf.py
```

---

## 💡 扩展与自定义

- **更改目标公司：**  
  编辑 `collect_reviews.py` 中的 `TARGET_URL`。

- **替换密钥：**  
  将你的 API 密钥和其他密钥放入 `.env`。

- **调整分块/重叠大小：**  
  在分块脚本中使用 `--chunk-size` 和 `--chunk-overlap` 参数。

- **自定义 QA 提示词：**  
  修改 `generate_qa_pairs.py` 中的 `SYSTEM_PROMPT`。

---

## 🌎 实用链接

- Bright Data API 文档：[API 参考](https://docs.brightdata.com/api-reference/introduction)
- OpenAI API 文档：[参考文档](https://platform.openai.com/docs)
- Hugging Face Hub：[上传数据集](https://huggingface.co/docs/datasets/upload_dataset)
- LangChain 文本拆分器：[文档](https://python.langchain.com/docs/how_to/#text-splitters)

---

## 📝 许可证

MIT

---

**祝你微调愉快！** 🎉
