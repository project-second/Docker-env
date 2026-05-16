FROM ubuntu:24.04

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        ca-certificates \
        curl \
        less \
        pkg-config \
        build-essential \
        cmake \
        git \
        vim \
        nano \
        openssl \
        libssl-dev \
        gsoap \
        libgsoap-dev \
        nginx \
        postgresql \
        postgresql-contrib \
        libpq-dev \
        gstreamer1.0-tools \
        gstreamer1.0-plugins-base \
        gstreamer1.0-plugins-good \
        gstreamer1.0-plugins-bad \
        gstreamer1.0-plugins-ugly \
        gstreamer1.0-libav \
        gstreamer1.0-rtsp \
        libgstreamer1.0-dev \
        libgstreamer-plugins-base1.0-dev \
        libgstrtspserver-1.0-0 \
        libgstrtspserver-1.0-dev \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

EXPOSE 80 5432 8554

CMD ["/bin/bash"]
