# 使用 PyTorch 官方的 CPU 镜像
FROM pytorch/pytorch:2.3.0-cpu

# 定义环境变量
ENV MAX_JOBS=4
ENV FLASH_ATTENTION_FORCE_BUILD=TRUE
ENV VLLM_WORKER_MULTIPROC_METHOD=spawn

# 定义安装参数
ARG INSTALL_BNB=false
ARG INSTALL_VLLM=false
ARG INSTALL_DEEPSPEED=false
ARG INSTALL_FLASHATTN=false
ARG PIP_INDEX=https://pypi.org/simple

# 设置工作目录
WORKDIR /app

# 安装依赖
COPY requirements.txt /app
RUN pip config set global.index-url "$PIP_INDEX" && \
    pip config set global.extra-index-url "$PIP_INDEX" && \
    python -m pip install --upgrade pip && \
    python -m pip install -r requirements.txt

# 复制应用程序文件
COPY . /app

# 安装 LLaMA Factory
RUN EXTRA_PACKAGES="metrics"; \
    if [ "$INSTALL_BNB" == "true" ]; then \
        EXTRA_PACKAGES="${EXTRA_PACKAGES},bitsandbytes"; \
    fi; \
    if [ "$INSTALL_VLLM" == "true" ]; then \
        EXTRA_PACKAGES="${EXTRA_PACKAGES},vllm"; \
    fi; \
    if [ "$INSTALL_DEEPSPEED" == "true" ]; then \
        EXTRA_PACKAGES="${EXTRA_PACKAGES},deepspeed"; \
    fi; \
    pip install -e ".[$EXTRA_PACKAGES]"

# 重建 flash attention (仅在 GPU 上有效，所以这里可以移除相关部分)
RUN if [ "$INSTALL_FLASHATTN" == "true" ]; then \
        pip uninstall -y flash-attn; \
    fi

# 设置卷
VOLUME [ "/root/.cache/huggingface", "/root/.cache/modelscope", "/app/data", "/app/output" ]

# 暴露 LLaMA Board 的端口
ENV GRADIO_SERVER_PORT 7860
EXPOSE 7860

# 暴露 API 服务的端口
ENV API_PORT 8000
EXPOSE 8000
