FROM ubuntu:latest

ARG APT_MIRROR=mirrors.tuna.tsinghua.edu.cn

RUN --mount=type=tmpfs,destination=/var/cache/apt set -ex; \
    cp /etc/apt/sources.list.d/ubuntu.sources /etc/apt/sources.list.d/ubuntu.sources.backup; \
    sed -i "s@/ports.ubuntu.com/@/${APT_MIRROR}/@g" /etc/apt/sources.list.d/ubuntu.sources; \
    apt-get update && apt install ca-certificates -y; \
    sed -i 's@http://@https://@g' /etc/apt/sources.list.d/ubuntu.sources; \
    apt-get update && apt-get dist-upgrade -y; \
    DEBIAN_FRONTEND=noninteractive apt-get install --no-install-recommends --no-install-suggests -y sudo curl git net-tools wget nano unzip \
        pkg-config cmake build-essential gcc-arm-none-eabi gdb libnewlib-arm-none-eabi libstdc++-arm-none-eabi-newlib \
        automake autoconf texinfo libtool libftdi-dev libusb-1.0-0-dev libhidapi-dev \
        python3 python3-pip python3-cryptography; \
    mv -f /etc/apt/sources.list.d/ubuntu.sources.backup /etc/apt/sources.list.d/ubuntu.sources; \
    apt clean && apt autoclean;

RUN mkdir /opt/pico-sdk && cd /opt/pico-sdk; \
    git clone https://github.com/raspberrypi/pico-sdk --single-branch .; \
    git submodule update --init --remote --checkout --single-branch; \
    git submodule update --init lib/mbedtls; \
    echo "export PICO_SDK_PATH=/opt/pico-sdk" >> /etc/profile.d/01-pico-sdk.sh;
    
RUN export PICO_SDK_PATH=/opt/pico-sdk; \
    mkdir /opt/pico-tool && cd /opt/pico-tool; \
    git clone https://github.com/raspberrypi/picotool --single-branch .; \
    mkdir build && mkdir bin && cd build && cmake .. && \
    make -j4 && make install;

# RUN mkdir /opt/openocd && cd /opt/openocd; \
#     git clone https://github.com/raspberrypi/openocd --single-branch --branch rp2040 .; \
#     git submodule set-url tools/git2cl "https://git.savannah.nongnu.org/git/git2cl.git"; \
#     git submodule update --init --remote --checkout --single-branch; \
#     ./bootstrap && ./configure --enable-cmsis-dap && make -j4 && make install; \
#     rm -rf /opt/openocd; \
#     mkdir -p /opt/openocd/src; \
#     ln -s $(which openocd) /opt/openocd/src/; \
#     ln -s /usr/local/share/openocd/scripts /opt/openocd/tcl; \
#     echo "adapter speed 5000" >> /opt/openocd/tcl/target/rp2040.cfg; \
#     echo "export OPENOCD_ROOT=/opt/openocd" >> /etc/profile.d/02-openocd.sh;
    
# RUN mkdir /opt/pico-lab && cd /opt/pico-lab; \
#     git clone https://github.com/wuxx/pico-lab --single-branch .; \
#     git submodule update --init --remote --checkout --single-branch; \
#     echo "export PATH=\${PATH}:/opt/pico-lab/tools" >> /etc/profile.d/02-pico-lab.sh; \
#     \
#     find /opt -name ".git" -type d -exec rm -rf {} +;

ADD ./waveshare_rp2350_one.h /opt/pico-sdk/src/boards/include/boards/
