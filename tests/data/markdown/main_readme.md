# 主自述文件
> 允许使用基于transformer的模型进行自然语言搜索，与您的文档聊天

这是一个具有多个嵌套子条目的测试markdown文件。

## 依赖项

- Python3
- [Miniconda](https://docs.conda.io/en/latest/miniconda.html#latest-miniconda-installer-links)

## 安装

```bash
pip install khoj
```

## 运行
  加载ML模型，生成嵌入并公开API来查询指定的org-mode文件

  ```shell
  python3 main.py --input-files ~/Notes/Schedule.org ~/Notes/Incoming.org --verbose
  ```

## 使用

### **通过API使用Khoj**
- 查询：`GET` [http://localhost:42110/api/search?q="What is the meaning of life"](http://localhost:42110/api/search?q=%22what%20is%20the%20meaning%20of%20life%22)
- 更新索引：`GET` [http://localhost:42110/api/update](http://localhost:42110/api/update)
- [Khoj API文档](http://localhost:42110/docs)

### *通过Web使用Khoj*

- 在浏览器中打开http://localhost:42110
- 在搜索框中输入查询

## 致谢

- [MiniLM模型](https://huggingface.co/sentence-transformers/multi-qa-MiniLM-L6-cos-v1)用于非对称文本搜索。请参阅(SBert文档)[https://www.sbert.net/examples/applications/retrieve_rerank/README.html]
- [OpenAI CLIP模型](https://github.com/openai/CLIP)用于图像搜索。请参阅[SBert文档](https://www.sbert.net/examples/applications/image-search/README.html)
