飞书云文档连接：https://yp8uiaigqd.feishu.cn/docx/MyJfd47ISo2DvixKMKPcGxtMnSI

N8N方案
https://n8n.io/
https://n8n.io/workflows/
1. 可以参考他人的方案快速尝试，并试用n8n提供的云服务workflow，不需要自己搭建服务器
ChatGPT Automatic Code Review in Gitlab MR：https://n8n.io/workflows/2167-chatgpt-automatic-code-review-in-gitlab-mr/
[图片]

3. 这里给出个人配置好的方案
暂时无法在飞书文档外展示此内容
[图片]
[图片]

配置
1. 主要需要github的token，设置webhook，配置openAI的key
2. 在workflow中配置token

Github webhook
https://docs.github.com/zh/webhooks/using-webhooks/creating-webhooks
1. 设置webhook，地址从n8n的webhook节点中复制
[图片]
[图片]
2. 可以选择指定的事件才触发，例如pull request事件
[图片]

[图片]

Github token
https://docs.github.com/zh/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens#%E5%88%9B%E5%BB%BA-personal-access-token-classic
https://github.com/settings/tokens/new
1. setting——》developer settings——》personal access tokens——》generate new token，访问这里
[图片]
2. 允许仓库的权限，以及discussion的读写权限
[图片]
openAI Key
https://platform.openai.com/api-keys
https://gptforwork.com/help/knowledge-base/create-openai-api-key
PS: openAI的key需要单独充值缴费，即使开通了chatGPT plus也不没有API key的额度；需要的可以teams联系我
[图片]

Workflow
1. 配置openAI的key，没有配置key的话，前面的流程也可以跑，最后提交chatGPT时会失败
[图片]
[图片]
2. 在【Get Changes】和【Post Discussions】这里配置Git token
[图片]
3. 其他的Git api不用调整，可以直接跑

执行
1. 点击【test workflow】就开始监听PR事件
[图片]
[图片]
[图片]
2. 执行效果如下，会针对每个文件的修改单独review，并给出comments
[图片]
[图片]

workflow节点解释
1. webhook节点用于监听github的PR事件
2. Need review筛选需要执行review的PR，当前筛选条件为comments=0
[图片]
3. Get changes节点为请求github的PR files接口，获取当前PR设计的文件改动和代码
[图片]
4. Split out节点筛选出部分参数changes和patch，以文件维度
[图片]
5. Parese Last Diff Line节点通过JavaScript脚本从patch中整理出差异代码的起始，结束行数，当前positionLine直接取了changes，作为PR评论所在行
[图片]
6. Code节点从patch中通过JavaScript脚本获取到originalCode和newCode，用来提交给chatGPT
[图片]
7. Basic LLM Chain可以设置不同的prompt，并在【OpenAI Chat Model】设置不同的版本，不同版本的收费不同
[图片]
[图片]
8. Post Discussions节点请求Git的PR comments接口，写入chatGPT给出的评审结果
[图片]
