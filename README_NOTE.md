
#### 训练大模型

- 创建训练完成输出目录 mkdit -p model/export/qwen1.5-0.5b/20240301/
- 获取主服务  git clone -b <tag-name> --depth=1 https://github.com/hiyouga/LLaMA-Factory.git
- 创建虚拟环境 conda create -n llama_factory python=3.10 && conda activate llama_factory
- 进入目录 cd LLaMA-Factory
- 安装依赖 pip install modelscope -U && pip install -r requirements.txt

```shell
#!/bin/bash

# Function to handle errors
handle_error() {
    echo "Error occurred in script at line: $1"
    exit 1
}

# Update package list and install jq
sudo apt-get update

# Trap errors
trap 'handle_error $LINENO' ERR

# Clone repository  git clone -b v0.9.0 --depth=1 https://github.com/hiyouga/LLaMA-Factory.git
git clone https://github.com/hiyouga/LLaMA-Factory.git
cd LLaMA-Factory
git checkout tags/v0.9.0
git checkout -b my-branch-v0.9.0  # 可选：如果需要在此标签上继续开发

# Update modelscope and install requirements
pip install -e ".[torch,metrics]"  # pip install modelscope -U && pip install -r requirements.txt

# Set environment variables
export USE_MODELSCOPE_HUB=1
export CUDA_VISIBLE_DEVICES=0

# Run Python script
llamafactory-cli webui # python src/webui.py

# Exit cleanly
exit 0
```







### FastChat运行自有模型[官方文档](https://github.com/lm-sys/FastChat)
```shell
# 安装Fastchat
# git clone https://github.com/lm-sys/FastChat.git # wget https://github.com/lm-sys/FastChat/archive/refs/tags/v0.2.36.tar.gz -o fastchat.tar.gz
pip install --upgrade pip
pip3 install "fschat[model_worker,webui]"
# CLI访问方式启动模型
python3 -m fastchat.serve.cli --model-path /root/qwen/202403131240 --device cpu
# web页面访问启动模型
    # 控制器 默认端口21001
python3 -m fastchat.serve.controller
    # 模型实例(分布式) 默认端口21002
python3 -m fastchat.serve.model_worker --model-path /root/qwen/202403131240 --device cpu
    # 模型实例高级功能、可扩展性、第三方 UI：worker 0
CUDA_VISIBLE_DEVICES=0 python3 -m fastchat.serve.model_worker --model-path /root/qwen/202403131240 --controller http://localhost:21001 --port 21002 --worker http://localhost:21002 --device cpu
    # 测试
python3 -m fastchat.serve.test_message --model-name 202403131240
    # web页面 默认端口7860
python3 -m fastchat.serve.gradio_web_server
# API 访问
    # 控制器 默认端口21001
python3 -m fastchat.serve.controller
    # 模型实例(分布式) 默认端口21002
        # 设置 OpenAI 基本 url
export OPENAI_API_BASE=http://localhost:8000/v1
        # 设置 OpenAI API 密钥
export OPENAI_API_KEY=EMPTY
        # 启动实例
python3 -m fastchat.serve.model_worker --model-names "gpt-3.5-turbo,text-davinci-003,text-embedding-ada-002" --model-path lmsys/vicuna-7b-v1.5
    # 启动 RESTful API 服务器
python3 -m fastchat.serve.openai_api_server --host localhost --port 8000
```