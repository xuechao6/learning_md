# Github项目使用



## DPF翻译



### 1.项目地址

[Byaidu/PDFMathTranslate: PDF scientific paper translation with preserved formats - 基于 AI 完整保留排版的 PDF 文档全文双语翻译，支持 Google/DeepL/Ollama/OpenAI 等服务，提供 CLI/GUI/Docker](https://github.com/Byaidu/PDFMathTranslate?tab=readme-ov-file)

### 2.步骤

1.激活**conda activate python310**
2.在终端中输入**pdf2zh -i**

此时浏览器应该自动打开可视化翻译界面，如果没有打开界面，在**浏览器中**手动输入http://localhost:7860/

### 3.界面使用

![image-20250115203142991](D:\Learn\github\picture\image-20250115203142991.png)

1.上传待翻译文件

2.选择模型

3.点击最底下蓝色的翻译按钮

4.下载翻译文件-->第一个是纯中文，第二个是中英一页一页的对照

### 4.模型配置

免费翻译：google和bing，这两个直接翻译就行了

大模型：整了两个大模型的api，是gpt4o-mini和智谱的api

**1.chatgpt:**

白嫖的gpt4的API的网址: https://github.com/chatanywhere/GPT_API_free
个人的需要修改key和上面的Base_url
Key: sk-q3iH4No02ouGMwgBpGwVmsduq0bd6jJFX2gw7ZEZvy5UEvMH
Base_url: https://api.chatanywhere.tech/v1

**2.zhipu：**

官方的API网址： [智谱AI开放平台](https://www.bigmodel.cn/usercenter/proj-mgmt/apikeys)

个人申请的API，glm-4-flash模型的API好像是免费的，官网上写着免费

key: 2da91b21527c480680a18e8913488217.xiqPL72l1o7rCvUH

3.sillicon：

硅基流动平台