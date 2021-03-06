FROM debian:jessie

ENV DEBIAN_FRONTEND noninteractive

## Add official gcloud apt repos key
#
COPY gcloud_apt.gpg /opt/
RUN cat /opt/gcloud_apt.gpg | apt-key add - && \
    rm -f /opt/gcloud_apt.gpg

## Install base package
#
COPY sources.list /etc/apt/sources.list
COPY apt.conf /etc/apt/apt.conf.d/70debconf
RUN mkdir -p /var/cache/apt/archives && \
    apt-get clean && apt-get update && apt-get install -y \
    # For ruby compile
    debian-keyring \
    sharutils \
    locales \
    zlib1g-dev \
    ca-certificates \
    gzip \
    unzip \
    curl \
    wget \
    openssl \
    # For minimum debug purpose
    telnet \
    redis-tools \
    dnsutils \
    net-tools \
    netcat-openbsd \
    less \
    htop \
    vim \
    # For GCE and GKE access
    google-cloud-sdk \
    kubectl && \
    # Install haproxy from backports
    apt-get install -t jessie-backports -y haproxy && \
    # Tidy up and reduce space
    apt-get clean && \
    rm -rf /usr/share/doc && \
    rm -rf /usr/share/man && \
    rm -rf /var/log/*

## Install dumb-init
#
RUN wget -O /bin/dumb-init https://github.com/Yelp/dumb-init/releases/download/v1.2.0/dumb-init_1.2.0_amd64
RUN chmod +x /bin/dumb-init

## Install tini
#
ENV TINI_VERSION v0.15.0
ADD https://github.com/krallin/tini/releases/download/${TINI_VERSION}/tini /tini
ADD https://github.com/krallin/tini/releases/download/${TINI_VERSION}/tini.asc /tini.asc
RUN chmod +x /tini

## Set LOCALE to UTF8
#
RUN echo "en_US.UTF-8 UTF-8" > /etc/locale.gen && \
    locale-gen en_US.UTF-8 && \
    dpkg-reconfigure locales && \
    /usr/sbin/update-locale LANG=en_US.UTF-8
ENV LC_ALL en_US.UTF-8

## Set timezone
#
RUN echo "America/New_York" > /etc/timezone && \
    dpkg-reconfigure -f noninteractive tzdata

## Allow all user to use ping
#
RUN chmod +s /bin/ping

## Install confd
#
RUN curl -sSL https://github.com/kelseyhightower/confd/releases/download/v0.11.0/confd-0.11.0-linux-amd64 > /usr/bin/confd && \
    chmod +x /usr/bin/confd

## Add kube-beachhead daemon
#
COPY kube-beachhead /

## Force user to redefine CMD
#
CMD false
