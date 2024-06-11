ARG VARIANT=bookworm-slim
FROM debian:${VARIANT}
ENV DEBIAN_FRONTEND=noninteractive
ENV LC_ALL=C.UTF-8
ENV LANG=C.UTF-8

# Arguments
ARG CONTAINER_USER=stm32
ARG CONTAINER_GROUP=stm32
ARG GITHUB_TOKEN

# Install dependencies
RUN apt-get update \
    && apt-get install -y pkg-config curl gcc clang libudev-dev unzip xz-utils \
    git wget flex bison gperf python3 python3-pip python3-venv pipx cmake ninja-build ccache libffi-dev libssl-dev dfu-util libusb-1.0-0 \
    gdb-multiarch openocd libusb-dev  \
    && apt-get clean -y && rm -rf /var/lib/apt/lists/* /tmp/library-scripts

# Set users
RUN adduser --disabled-password --gecos "" ${CONTAINER_USER}
USER ${CONTAINER_USER}
WORKDIR /home/${CONTAINER_USER}

# Install rustup
RUN curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- \
    --profile complete -y \
    --target thumbv6m-none-eabi thumbv7m-none-eabi thumbv7em-none-eabi thumbv7em-none-eabihf

# Update envs
ENV PATH=${PATH}:/home/${CONTAINER_USER}/.cargo/bin

RUN ARCH=$($HOME/.cargo/bin/rustup show | grep "Default host" | sed -e 's/.* //') && \
    cargo install cargo-binutils && \
    rustup component add llvm-tools-preview && \
    cargo install cargo-generate && \
    cargo install probe-rs-tools

RUN pipx install gdbgui

CMD [ "/bin/bash" ]
