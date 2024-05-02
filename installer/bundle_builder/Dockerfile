# Copyright 2021 VMware, Inc. All Rights Reserved.
# SPDX-License-Identifier: Apache-2.0

# Build and push (opt-in) a BYOH bundle to repository
#
# 1. Download bundle ingredients. See ingredients/deb/download.sh
# 2. Mount the bundle ingredients under /ingredients
# 3. Optional. Mount output bundle directory under /bundle
# 3. Optional. Mount additional configuration under /config
#	-v config/ubuntu/20_04/k8s/1_22:/config
#	Defaults to config/ubuntu/20_04/k8s/1_22
# Example
# // Build and push a BYOH bundle to repository
# docker run --rm -v <INGREDIENTS_HOST_ABS_PATH>:/ingredients --env BUILD_ONLY=0 <THIS_IMAGE> <REPO>/<BUNDLE IMAGE>
#
# // Build and store a BYOH bundle
# docker run --rm -v <INGREDIENTS_HOST_ABS_PATH>:/ingredients  -v <BUNDLE_OUTPUT_ABS_PATH>:/bundle --env <THIS_IMAGE>

# https://github.com/carvel-dev/docker-image/blob/develop/Dockerfile
FROM ubuntu
# perl is needed for shasum
RUN apt-get -y update && apt-get install -y wget perl ca-certificates && update-ca-certificates && rm -rf /var/lib/apt/lists/*
RUN bash -c "set -eo pipefail; wget -O- https://carvel.dev/install.sh | bash"
RUN ytt version && kapp version && kbld version && kwt version && imgpkg version && vendir version

# If set to 1 bundle is built and available as bundle/bundle.tar
# If set to 0 bundle is build and pushed to repo
ENV BUILD_ONLY=1

WORKDIR /bundle-builder
COPY *.sh ./
RUN chmod a+x *.sh
#Default config
COPY config/ubuntu/20_04/k8s/1_22 /config/

RUN mkdir /ingredients && mkdir /bundle
ENV PATH="/bundle-builder:${PATH}"

WORKDIR /tmp/bundle
ENTRYPOINT ["build-push-bundle.sh", "/ingredients", "/config", "rpm"]
