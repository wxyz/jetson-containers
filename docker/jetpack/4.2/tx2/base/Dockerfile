ARG DEPENDENCIES_IMAGE
ARG IMAGE_NAME
FROM ${DEPENDENCIES_IMAGE} as dependencies

ARG IMAGE_NAME
FROM ${IMAGE_NAME}:32.1-tx2

ENV JETPACK_VERSION "4.2"

LABEL com.nvidia.jetpack.version="${JETPACK_VERSION}"

ENV CUDA_VERSION 10.0.166

ENV CUDA_PKG_VERSION=${CUDA_VERSION}_1.0-1

LABEL com.nvidia.cuda.version="${CUDA_VERSION}"

# Prereqs

RUN apt-get update && \
    apt-get install -y --no-install-recommends gnupg \
    && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# CUDA Runtime for L4T

ARG CUDA_TOOLKIT_PKG="cuda-repo-l4t-10-0-local-${CUDA_PKG_VERSION}_arm64.deb"

COPY --from=dependencies /data/${CUDA_TOOLKIT_PKG} ${CUDA_TOOLKIT_PKG}
RUN echo "5e3eedc3707305f9022d41754d6becde ${CUDA_TOOLKIT_PKG}" | md5sum -c - && \
    dpkg --force-all -i ${CUDA_TOOLKIT_PKG} && \
    rm ${CUDA_TOOLKIT_PKG} && \
    apt-key add /var/cuda-repo-*-local*/*.pub && \
    apt-get update && \
    apt-get install -y --no-install-recommends --allow-downgrades cuda-cudart-10-0 cuda-nvtx-10-0 && \
    ln -s cuda-10.0 /usr/local/cuda && \
    dpkg --purge cuda-repo-l4t-10-0-local-10.0.166 \
    && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

RUN export LD_LIBRARY_PATH=/usr/local/cuda/lib64:${LD_LIBRARY_PATH} && \
    export LIBRARY_PATH=/usr/local/cuda/lib64/stubs && \
    grep -q "export PATH=.*/usr/local/cuda/bin" ~/.bashrc || echo "export PATH=/usr/local/cuda/bin:$PATH">>~/.bashrc && \
    grep -q "export LD_LIBRARY_PATH=/usr/local/cuda/lib64" ~/.bashrc || echo "export LD_LIBRARY_PATH=/usr/local/cuda/lib64:$LD_LIBRARY_PATH" >> ~/.bashrc && \
    grep -q "export PATH=.*/usr/local/cuda/bin" /etc/skel/.bashrc || echo "export PATH=/usr/local/cuda/bin:$PATH">>/etc/skel/.bashrc && \
    grep -q "export LD_LIBRARY_PATH=/usr/local/cuda/lib64" /etc/skel/.bashrc || echo "export LD_LIBRARY_PATH=/usr/local/cuda/lib64:$LD_LIBRARY_PATH" >> /etc/skel/.bashrc

ENV NVIDIA_VISIBLE_DEVICES all
ENV NVIDIA_DRIVER_CAPABILITIES all
