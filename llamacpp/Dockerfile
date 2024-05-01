# First Stage - Building the Go application
FROM nvidia/cuda:12.4.1-devel-ubuntu22.04 as builder

# Install necessary packages
RUN apt-get update && apt-get install -y build-essential git cmake && \
    rm -rf /var/lib/apt/lists/*

WORKDIR /compiler

# Clone and build the llama.cpp project
RUN git clone https://github.com/ggerganov/llama.cpp.git && \
    mkdir llama.cpp/build && \
    cd llama.cpp/build && \
    cmake -DLLAMA_CUDA=1 .. && \
    cmake --build . --config Release


# Second Stage - Setting up Python and requirements
FROM ubuntu:22.04

# Install required packages
RUN apt-get update && apt-get install -y curl ca-certificates python3-pip git make && \
    rm -rf /var/lib/apt/lists/*

# Clone the Python application repository
WORKDIR /files
RUN git clone https://github.com/ggerganov/llama.cpp.git 
# Set up Python dependencies
WORKDIR /files/llama.cpp
# Uncomment line below to skip python dependencies
# RUN pip install -r requirements.txt


COPY --from=builder /compiler/llama.cpp/build/bin /files/llama.cpp

# RUN pip cache purge
# Set environment variables for NVIDIA compatibility
ENV PATH=/usr/local/nvidia/bin:/usr/local/cuda/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
ENV LD_LIBRARY_PATH=/usr/local/nvidia/lib:/usr/local/nvidia/lib64
ENV NVIDIA_DRIVER_CAPABILITIES=compute,utility

# Set the entry point to run.sh
ENTRYPOINT /bin/bash
