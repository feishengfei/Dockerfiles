# Run 'docker image build -t trusty:v0 .'

FROM ubuntu:14.04

# Standard SSH port
EXPOSE 22

CMD ["/usr/sbin/sshd", "-D"]

COPY source.list /etc/apt/sources.list

RUN \
# update && upgrade
    apt-get update && \
    DEBIAN_FRONTEND="noninteractive" apt-get -y upgrade && \
# update tz
    DEBIAN_FRONTEND="noninteractive" TZ="Asia/Shanghai" apt-get -y install tzdata && \
# Install basic tools
    apt-get install -y coreutils git wget rsync zip unzip locales sudo openssh-server \
# build essential
    cmake gcc g++ build-essential autotools-dev automake autoconf \
# ptyhon
    python python3 python3-pip python-pip  \
# Install a basic SSH server(depends on openssh-server)
    && sed -i 's|session    required     pam_loginuid.so|session    optional     pam_loginuid.so|g' /etc/pam.d/sshd && \
    mkdir -p /var/run/sshd && \
# install jdk(only for trusty, for 18.04 bionic, run `apt-get install -qy openjdk-8-jdk` instead)
    mkdir -p /usr/local/java /usr/java && \
    wget -qO - https://repo.huaweicloud.com/openjdk/11.0.2/openjdk-11.0.2_linux-x64_bin.tar.gz | tar zxf - -C /usr/local/java/ && \
    ln -sf /usr/local/java/jdk-11.0.2 /usr/java/latest && \
# Cleanup old packages
    apt-get -y autoremove && apt-get clean \
# Add user jenkins to the image
#    adduser --quiet jenkins && \
    && useradd -b /home/jenkins -d /home/jenkins -m -s /bin/bash -G sudo jenkins && \
# Set password for the jenkins user 
    echo "jenkins:123456" | chpasswd && \
# add jenkins to sudo password free
    echo "jenkins  ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/jenkins && \
# add use bash by default /bin/sh
    ln -sf /bin/bash /bin/sh && \
# update locales
    locale-gen en_US.UTF-8 && \
    update-locale LANG=en_US.UTF-8



# Copy authorized keys
COPY .gitconfig  /home/jenkins/.gitconfig
COPY .ssh/*  /home/jenkins/.ssh/

# copy repo
COPY repo /usr/bin/

# chown
RUN chown -R jenkins:jenkins /home/jenkins/.ssh && \
    chmod 0600 /home/jenkins/.ssh/id_rsa
