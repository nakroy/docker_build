FROM ubuntu:22.04

WORKDIR /tmp
COPY . ./

# 设置阿里源
RUN sed -i 's/archive.ubuntu.com/mirrors.aliyun.com/g' /etc/apt/sources.list

# 安装系统依赖
RUN apt update && \
    apt install --no-install-recommends ca-certificates -y && \
    apt install --no-install-recommends bc wget -y && \
    apt install --no-install-recommends curl gcc make g++ pkg-config unzip -y && \
    apt install --no-install-recommends libblas3 liblapack3 gfortran vim -y && \
    apt install --no-install-recommends liblapack-dev libblas-dev libhdf5-dev libffi-dev -y && \
    apt install --no-install-recommends libssl-dev zlib1g-dev xz-utils cython3 python3-h5py -y && \
    apt install --no-install-recommends libopenblas-dev libgmpxx4ldbl liblzma-dev -y && \
    apt install --no-install-recommends libxml2 pciutils libgl1-mesa-glx libbz2-dev -y && \
    apt install --no-install-recommends git -y && \
    apt clean && rm -rf /var/lib/apt/lists/*

# 设定系统python环境变量
ENV LD_LIBRARY_PATH=/usr/local/python3.9.18/lib: \
    PATH=/usr/local/python3.9.18/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
    
# 安装Python
RUN umask 0022 && \
    wget https://repo.huaweicloud.com/python/3.9.18/Python-3.9.18.tar.xz && \
    tar -xf Python-3.9.18.tar.xz && cd Python-3.9.18 && ./configure --prefix=/usr/local/python3.9.18 --enable-shared && \
    make && make install && \
    ln -sf /usr/local/python3.9.18/bin/python3 /usr/bin/python3 && \
    ln -sf /usr/local/python3.9.18/bin/python3 /usr/bin/python && \
    ln -sf /usr/local/python3.9.18/bin/pip3 /usr/bin/pip3 && \
    ln -sf /usr/local/python3.9.18/bin/pip3 /usr/bin/pip && \
    cd .. && \
    rm -rf Python* && \
    mkdir -p ~/.pip  && \
    echo "[global]\n\
    index-url = https://mirrors.huaweicloud.com/repository/pypi/simple\n\
    extra-index-url = http://mirrors.aliyun.com/pypi/simple/\n\
        https://pypi.tuna.tsinghua.edu.cn/simple/\n\
        http://pypi.mirrors.ustc.edu.cn/simple/\n\
    \n\
    [install]\n\
    trusted-host=mirrors.huaweicloud.com\n\
        mirrors.aliyun.com\n\
        pypi.tuna.tsinghua.edu.cn\n\
        pypi.mirrors.ustc.edu.cn" >> ~/.pip/pip.conf && \
    pip3 install pip -U

# 安装python基础依赖
RUN pip3 install numpy && \
    pip3 install decorator && \
    pip3 install sympy && \
    pip3 install cffi && \
    pip3 install pyyaml && \
    pip3 install pathlib2 && \
    pip3 install protobuf attrs attr && \
    pip3 install scipy && \
    pip3 install requests psutil absl-py && \
    rm -rf /root/.cache/pip

# 设置环境变量
ENV LD_LIBRARY_PATH=/usr/lib/aarch64-linux-gnu/hdf5/serial:$LD_LIBRARY_PATH

# 创建lib64下的linker，避免在euler宿主机上运行driver工具出错
RUN if [ ! -d "/lib64" ]; \
    then \
        mkdir /lib64 && ln -sf /lib/ld-linux-aarch64.so.1 /lib64/ld-linux-aarch64.so.1; \
    fi

# 清理临时目录
RUN rm -rf ./*
