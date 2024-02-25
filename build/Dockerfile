# From root of repo:
# docker build -t apache-ant -f build/Dockerfile .
# docker run --rm -v $(pwd):/workspace apache-ant ant generated

# Or, to run multiple ant commands,
# docker run -it --rm -v $(pwd):/workspace apache-ant
# [your commands]
# exit

# Contents below adapted from a ChatGPT response. Proceed with caution. :)

# Use a base image with Java 8 already installed
FROM openjdk:8-jdk

# Set environment variables
ENV ANT_VERSION 1.10.14
ENV ANT_HOME /opt/apache-ant-$ANT_VERSION
ENV PATH $PATH:$ANT_HOME/bin

# Install required packages
RUN apt-get update && \
    apt-get install -y wget && \
    rm -rf /var/lib/apt/lists/*

# Download and install Apache Ant
RUN wget -qO- https://downloads.apache.org/ant/binaries/apache-ant-$ANT_VERSION-bin.tar.gz | tar xvz -C /opt

# Set working directory
WORKDIR /workspace
