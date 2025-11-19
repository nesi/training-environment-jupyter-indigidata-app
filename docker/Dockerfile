FROM ubuntu:22.04

# install system dependencies
# TODO: move ldap-utils, libnss-ldapd, libpam-ldapd, nscd, nslcd to base image??
RUN apt-get update && DEBIAN_FRONTEND=noninteractive apt-get install -y \
        curl \
        git \
        dnsutils \
        ldap-utils \
        libnss-ldapd \
        libpam-ldapd \
        less \
        locales \
        nano \
        nodejs \
        nscd \
        nslcd \
        rsync \
        unzip \
        vim \
        wget \
        zip \
        python-is-python3 \
        python3 \
        python3-pip \
        libglib2.0-0 \
        libnss3 \
        libnspr4 \
        libnssutil3 \
        libdbus-1-3 \
        libxcb1 \
        libx11-xcb1 \
    && rm -rf /var/lib/apt/lists/* \
    && localedef -i en_NZ -c -f UTF-8 -A /usr/share/locale/locale.alias en_NZ.UTF-8

ENV LANG en_NZ.utf8

# install kubectl, required for running on the k8s cluster
ARG KUBECTL_VERSION=v1.28.5
RUN curl -LO https://dl.k8s.io/release/$KUBECTL_VERSION/bin/linux/amd64/kubectl \
    && mv kubectl /usr/local/bin/kubectl \
    && chmod +x /usr/local/bin/kubectl

# copy in ood k8s utils
ARG UTILS_HASH=6298fb01f7a7c66a8454e3f0fd74437a32491423
RUN git clone https://github.com/nesi/training-environment-k8s-utils.git /opt/ood-k8s-utils \
    && cd /opt/ood-k8s-utils \
    && git checkout $UTILS_HASH \
    && chmod +x /opt/ood-k8s-utils/files/* \
    && mv /opt/ood-k8s-utils/files/* /bin/ \
    && rm -rf /opt/ood-k8s-utils

# Install ML101
#ARG UTILS_HASH=6298fb01f7a7c66a8454e3f0fd74437a32491423
RUN git clone https://github.com/nesi/ml101_workshop.git /opt/sklearn_tutorial \
    && cd /opt/sklearn_tutorial \
#    && git checkout $ML101_HASH \
    && rm -rf /opt/sklearn_tutorial/.git

# Install IndigiData NLP Training repo
RUN git clone https://github.com/nicholson-consulting/indigidata_nlp_training.git /opt/indigi_data \
    && cd /opt/indigi_data \
    && rm -rf /opt/indigi_data/.git

# install miniconda
RUN wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O /miniconda.sh \
    && bash /miniconda.sh -b -p /opt/miniconda3 \
    && rm -f /miniconda.sh

RUN . /opt/miniconda3/etc/profile.d/conda.sh \
    && conda config --set auto_activate_base false \
    && conda tos accept --override-channels --channel https://repo.anaconda.com/pkgs/main \
    && conda tos accept --override-channels --channel https://repo.anaconda.com/pkgs/r \
    && conda env create -f /opt/sklearn_tutorial/environment.yml -p /var/lib/skl_tut \
    && conda run -p /var/lib/skl_tut pip3 install --no-cache-dir nltk \
    && echo ". /opt/miniconda3/etc/profile.d/conda.sh" > /etc/profile.d/conda.sh \
    && echo "conda activate /var/lib/skl_tut" >> /etc/profile.d/conda.sh \
    && conda clean -afy

    
    