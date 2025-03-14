# Copyright 2021 syzkaller project authors. All rights reserved.
# Use of this source code is governed by Apache 2 LICENSE that can be found in the LICENSE file.

# syzbot container is used to run syz-ci on syzbot, use as:
# docker build -t gcr.io/syzkaller/syzbot tools/docker/syzbot
# docker push gcr.io/syzkaller/syzbot
# docker run -it gcr.io/syzkaller/syzbot

FROM debian:bullseye

RUN apt-get update --allow-releaseinfo-change
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y -q \
	# Build essentials:
	gcc g++ binutils make ccache golang-go \
	clang clang-format clang-tidy \
	# Some common utilities:
	unzip curl sudo procps psmisc nano vim git bzip2 dh-autoreconf software-properties-common \
	# These are needed to build Linux kernel:
	flex bison bc gawk dwarves cpio texinfo texi2html lzop lbzip2 \
	zlib1g-dev libelf-dev libncurses-dev libmpc-dev libssl-dev \
	# This is required to run alien arch binaries in pkg/cover tests:
	qemu-user \
	# These are required to run foreign arch kernels:
	qemu-utils qemu-system-misc qemu-system-x86 qemu-system-arm qemu-system-aarch64  \
	qemu-system-s390x qemu-system-mips qemu-system-ppc \
	# Cross-compilation:
	g++-arm-linux-gnueabi g++-aarch64-linux-gnu g++-powerpc64le-linux-gnu \
	g++-mips64el-linux-gnuabi64 g++-s390x-linux-gnu g++-riscv64-linux-gnu \
	libc6-dev-i386 libc6-dev-i386-amd64-cross lib32gcc-10-dev lib32stdc++-10-dev \
	apt-transport-https curl gnupg python-is-python3

# Not really GRTE, but it's enough to run some scripts that hardcode the path.
RUN mkdir -p /usr/grte/v5/bin && ln -s /usr/bin/python3 /usr/grte/v5/bin/python2.7

# Install bazel
RUN echo "deb [arch=amd64] http://storage.googleapis.com/bazel-apt stable jdk1.8" | \
	sudo tee /etc/apt/sources.list.d/bazel.list && \
	curl https://bazel.build/bazel-release.pub.gpg | sudo apt-key add -
RUN apt-get update --allow-releaseinfo-change && DEBIAN_FRONTEND=noninteractive apt-get install -y -q bazel

# pkg/osutil uses syzkaller user for sandboxing.
RUN useradd syzkaller
RUN echo "export PS1='\n\W🤖 '" >> /root/.bashrc
