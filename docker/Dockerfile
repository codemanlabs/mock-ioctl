# ---------------------------------------------------------------------------
# How to build
# ---------------------------------------------------------------------------
# docker build \
#     --build-arg UID=$(id -u) \
#     --build-arg GID=$(id -g) \
#     -t mock-ioctl ./docker

# ---------------------------------------------------------------------------
# How to run
# ---------------------------------------------------------------------------
# docker run --rm -it \
#     -v "$(pwd)":/workspace \
#     -w /workspace \
#     mock-ioctl


FROM ubuntu:24.04

ENV DEBIAN_FRONTEND=noninteractive

# ---------------------------------------------------------------------------
# 1. System packages
# ---------------------------------------------------------------------------
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    cmake \
    git \
    python3 \
    python3-venv \
    python3-dev \
    pkg-config \
    ca-certificates \
    gdb \
    vim \
    && rm -rf /var/lib/apt/lists/*

# ---------------------------------------------------------------------------
# 2. Python virtual environment for Conan
# ---------------------------------------------------------------------------
RUN python3 -m venv /opt/conan
RUN /opt/conan/bin/pip install --no-cache-dir conan
ENV PATH="/opt/conan/bin:${PATH}"

RUN conan profile detect

# ---------------------------------------------------------------------------
# 3. Create a *static* user named "dev" but inject host UID/GID
# ---------------------------------------------------------------------------
ARG UID=1000
ARG GID=1000

# Create group
RUN groupadd -g ${GID} dev || true

# Create user with the host UID/GID
RUN useradd -m -u ${UID} -g ${GID} -s /bin/bash dev || true

USER dev
WORKDIR /workspace
ENV HOME=/home/dev

CMD [ "bash" ]
