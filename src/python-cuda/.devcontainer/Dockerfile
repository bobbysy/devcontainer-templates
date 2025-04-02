FROM nvcr.io/nvidia/cuda:12.4.1-cudnn-devel-ubuntu22.04

ARG USERNAME=user-name-goes-here
ARG USER_UID=1000
ARG USER_GID=$USER_UID
ARG PYTHON_VERSION=3.11

ENV \
    PYTHON_VERSION=$PYTHON_VERSION \
    TZ=Asia/Singapore \
    PATH=$PATH:/root/.local/bin:/usr/local/lib/python3.11/dist-packages:/home/user-name-goes-here/.local/bin

# Create the user
RUN groupadd --gid $USER_GID $USERNAME \
    && useradd --uid $USER_UID --gid $USER_GID -m $USERNAME \
    #
    # [Optional] Add sudo support. Omit if you don't need to install software after connecting.
    && apt-get update \
    && apt-get install -y sudo \
    && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME \
    && chmod 0440 /etc/sudoers.d/$USERNAME

RUN --mount=target=/var/lib/apt/lists,type=cache,sharing=locked \
    --mount=target=/var/cache/apt,type=cache,sharing=locked \
    rm -f /etc/apt/apt.conf.d/docker-clean && \
    apt-get update && export DEBIAN_FRONTEND=noninteractive && \
    apt-get install -y --no-install-recommends \
    software-properties-common && \
    add-apt-repository -y ppa:deadsnakes/ppa && \
    apt-get update && \
    INSTALL_PKGS="python${PYTHON_VERSION} python${PYTHON_VERSION}-venv git" && \ 
    apt-get install -y --no-install-recommends \
        $INSTALL_PKGS && \
    python3.11 -m ensurepip && \
    ln -sf /usr/bin/python${PYTHON_VERSION} /usr/bin/python && \
    ln -sf /usr/bin/python${PYTHON_VERSION} /usr/bin/python3 && \
    ln -sf /usr/bin/pip${PYTHON_VERSION} /usr/bin/pip

USER $USERNAME

RUN python3 -m pip install pipx && \
    pipx ensurepath && \
    pipx --global ensurepath

RUN --mount=type=cache,target=/root/.cache/pip \
    --mount=type=cache,target=/root/.local/pipx \
    pipx install ruff pytest black isort poetry pylint pre-commit

USER $USERNAME
