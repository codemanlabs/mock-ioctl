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
# 3. Create a static user "dev" matching host UID/GID
# ---------------------------------------------------------------------------
ARG UID=1000
ARG GID=1000

# Ensure UID/GID are valid non-zero integers
RUN if [ "$UID" -eq 0 ] || [ "$GID" -eq 0 ]; then \
        echo "ERROR: UID/GID cannot be 0" && exit 1; \
    fi

# Create or reuse group
RUN if getent group $GID >/dev/null; then \
        groupname=$(getent group $GID | cut -d: -f1); \
        if [ "$groupname" != "dev" ]; then groupmod -n dev "$groupname"; fi; \
    else \
        groupadd -g $GID dev; \
    fi

# Create or reuse user
RUN if id -u $UID >/dev/null 2>&1; then \
        username=$(getent passwd $UID | cut -d: -f1); \
        if [ "$username" != "dev" ]; then usermod -l dev "$username"; fi; \
        usermod -d /home/dev -m dev; \
    else \
        useradd -m -u $UID -g $GID -s /bin/bash dev; \
    fi

USER dev
WORKDIR /workspace
ENV HOME=/home/dev

CMD [ "bash" ]
