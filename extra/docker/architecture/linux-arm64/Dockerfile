#!/bin/echo docker build . -f
# -*- coding: utf-8 -*-
# SPDX-License-Identifier: MPL-2.0
# Copyright 2018-present Samsung Electronics Co., Ltd. and other contributors
#{
# This Source Code Form is subject to the terms of the Mozilla Public
# License, v. 2.0. If a copy of the MPL was not distributed with this
# file, You can obtain one at http://mozilla.org/MPL/2.0/ .
#}

FROM arm64v8/node:carbon
ADD extra/docker/architecture/linux-arm64/local/qemu-aarch64-static /usr/bin/qemu-aarch64-static 

MAINTAINER Philippe Coval (rzr@users.sf.net)

ENV DEBIAN_FRONTEND noninteractive
ENV LC_ALL en_US.UTF-8
ENV LANG ${LC_ALL}


RUN echo "#log: Configuring locales" \
  && set -x \
  && apt-get update -y \
  && apt-get install -y locales \
  && echo "${LC_ALL} UTF-8" | tee /etc/locale.gen \
  && locale-gen ${LC_ALL} \
  && dpkg-reconfigure locales \
  && sync

ENV project mozilla-iot-generic-sensors-adapter

RUN echo "#log: ${project}: Setup system" \
  && set -x \
  && apt-get update -y \
  && apt-cache search npm \
  && apt-get install -y \
  sudo \
  curl \
  && apt-get clean \
  && sync

ENV install_url https://raw.githubusercontent.com/mozilla-iot/gateway/master/install.sh

RUN echo "#log: ${project}: Setup system" \
  && set -x \
  && curl -o mozilla-iot-gateway-install.sh ${install_url} \
  && bash -x -e mozilla-iot-gateway-install.sh \
  && sync

ADD . /root/.mozilla-iot/addons/generic-sensors-adapter
WORKDIR /root/.mozilla-iot/addons/generic-sensors-adapter
RUN echo "#log: ${project}: Preparing sources" \
  && set -x \
  && which npm || . ~/.bashrc \
  && npm config set unsafe-perm true \
  && which yarn || npm install -g yarn \
  && sync

WORKDIR /root/.mozilla-iot/addons/generic-sensors-adapter
RUN echo "#log: ${project}: Building sources" \
  && set -x \
  && which npm || . ~/.bashrc \
  && ./package.sh \
  && sync

WORKDIR /root/.mozilla-iot/addons/generic-sensors-adapter
RUN echo "#log: ${project}: Installing binaries" \
  && set -x \
  && install -d /usr/local/src/${project}/dist \
  && install generic-sensors-adapter-*.tgz /usr/local/src/${project}/dist \
  && sync
