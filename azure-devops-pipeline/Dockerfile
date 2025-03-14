FROM openjdk:14-jdk-alpine

# see https://github.com/docker-library/openjdk/issues/73
ENV LC_CTYPE en_US.UTF-8

RUN addgroup -S dtcgroup && adduser -S dtcuser -G dtcgroup

RUN apk update && apk add --no-cache build-base

RUN	echo "add needed tools" && \
    apk add --no-cache curl wget zip unzip git bash --virtual build-dependencies build-base\
    git \
    graphviz \
    python \
    ruby-dev \
    py-pygments \
    libc6-compat \
    ttf-dejavu \
    nodejs \ 
    bash sudo shadow
RUN gem update --system  --no-rdoc --no-ri
RUN gem install rdoc --no-document 
RUN gem install pygments.rb


# Add pandoc
# https://github.com/advancedtelematic/dockerfiles/blob/master/doctools/Dockerfile
#RUN apk add --no-cache cmark --repository http://nl.alpinelinux.org/alpine/edge/testing && \
#    apk add --no-cache --allow-untrusted pandoc --repository https://conoria.gitlab.io/alpine-pandoc/

SHELL ["/bin/bash", "-c"]

#USER dtcuser
WORKDIR /home/dtcuser
ENV HOME=/home/dtcuser

ENV GRADLE_USER_HOME=/home/dtcuser/.gradle

ARG DTC_VERSION
RUN     git clone --branch ng https://github.com/docToolchain/docToolchain.git  && \
        cd docToolchain && \
        git fetch --tags && \
        git checkout ${DTC_VERSION} && \
        git submodule update -i && \
        # remove .git folders
        rm -rf `find -type d -name .git` && \
        umask g+w && \
        ./gradlew downloadDependencies && \
        chmod -R o=u $GRADLE_USER_HOME && \
        chmod -R g=u $GRADLE_USER_HOME && \
        rm -r $GRADLE_USER_HOME/daemon && \
        chmod -R o=u $HOME

# add reveal.js
RUN     cd /home/dtcuser/docToolchain/resources/. && \ 
        ./clone.sh && \
        cd - 

ENV PATH="/home/dtcuser/docToolchain/bin:${PATH}"
ENV DTC_HEADLESS=1

# Required by Azure DevOps
LABEL "com.azure.dev.pipelines.agent.handler.node.path"="/usr/bin/node"

# USER dtcuser

WORKDIR /project

VOLUME /project

