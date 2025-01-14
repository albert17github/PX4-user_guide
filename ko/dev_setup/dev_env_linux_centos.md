# CentOS 개발 환경

:::warning
이 개발 환경은 [지원되지 않습니다](../advanced/dev_env_unsupported.md). 지원되는 환경과 도구에 대한 정보는 [도구 모음 설치](../dev_setup/dev_env.md)를 참고하십시오!
:::

빌드에는 Python 2.7.5가 필요합니다. 따라서, 이 글을 쓰는 시점에서 Centos 7을 사용하여야 합니다. (이전 Centos 릴리스의 경우 Python v2.7.5를 설치할 수 있습니다. 하지만 yum을 깨뜨릴 수 있으므로 권장하지 않습니다.)

## 공통 종속성

EPEL 저장소는 openocd libftdi-devel libftdi-python에 필요합니다.

```sh
wget https://dl.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-5.noarch.rpm
sudo yum install epel-release-7-5.noarch.rpm
yum update
yum groupinstall “Development Tools”
yum install python-setuptools python-numpy
easy_install pyserial
easy_install pexpect
easy_install toml
easy_install pyyaml
easy_install cerberus
yum install openocd libftdi-devel libftdi-python python-argparse flex bison-devel ncurses-devel ncurses-libs autoconf texinfo libtool zlib-devel cmake vim-common
```

:::note
`python-pip`와 `screen`을 설치할 수 있습니다.
:::

## GCC 툴체인 설치
<!-- GCC toolchain documentation used for all Linux platforms to build NuttX -->

아래 스크립트로 GCC 7-2017-q4를 설치합니다.

:::warning
이 GCC 버전은 최신 버전이 아닙니다. Ubuntu에서 현재 버전을 작성하는 시점은 `9-2020-q2-update`입니다([focal nuttx docker 파일](https://github.com/PX4/PX4-containers/blob/master/docker/Dockerfile_nuttx-focal#L28) 참조).
:::

```sh
pushd .
cd ~
wget https://armkeil.blob.core.windows.net/developer/Files/downloads/gnu-rm/7-2017q4/gcc-arm-none-eabi-7-2017-q4-major-linux.tar.bz2
tar -jxf gcc-arm-none-eabi-7-2017-q4-major-linux.tar.bz2
exportline="export PATH=$HOME/gcc-arm-none-eabi-7-2017-q4-major/bin:\$PATH"
if grep -Fxq "$exportline" ~/.profile; then echo nothing to do ; else echo $exportline >> ~/.profile; fi
popd
```

이제 시스템을 다시 시작하십시오.


**문제 해결**

다음 명령을 입력하여 버전을 확인하십시오.

```sh
arm-none-eabi-gcc --version
```

출력 결과는 다음과 비슷하여야 합니다.

```sh
arm-none-eabi-gcc (GNU Tools for Arm Embedded Processors 7-2017-q4-major) 7.2.1 20170904 (release) [ARM/embedded-7-branch revision 255204]
Copyright (C) 2017 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
```

<!-- import docs ninja build system -->
## Ninja 빌드 시스템

[Ninja](https://ninja-build.org/)는 *Make*보다 빠른 빌드 시스템이며, PX4 *CMake* 생성기에서 지원합니다.

Ubuntu Linux에서는 일반 저장소에서 자동으로 설치할 수 있습니다.

```sh
sudo apt-get install ninja-build -y
```

다른 시스템은 패키지 관리자에 Ninja를 포함하지 않을 수 있습니다. 이 경우 대안은 바이너리를 다운로드하여 경로에 추가하는 것입니다.

```sh
mkdir -p $HOME/ninja
cd $HOME/ninja
wget https://github.com/martine/ninja/releases/download/v1.6.0/ninja-linux.zip
unzip ninja-linux.zip
rm ninja-linux.zip
exportline="export PATH=$HOME/ninja:\$PATH"
if grep -Fxq "$exportline" ~/.profile; then echo nothing to do ; else echo $exportline >> ~/.profile; fi
. ~/.profile
```

