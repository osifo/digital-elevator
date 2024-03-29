#
# Copyright (c) 2023 Intel
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#      http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
#

ARG BASE=golang:1.21-alpine3.18
FROM ${BASE} AS builder

ARG MAKE='make build'

WORKDIR /signals-service

LABEL copyright='Copyright (c) 2023: Outside Labs'

RUN apk add --update --no-cache make git

COPY go.mod vendor* ./
RUN [ ! -d "vendor" ] && go mod download all || echo "skipping..."

COPY . .

#refer to the Makefile for what gets run here
RUN ${MAKE}

# Next image - Copy built Go binary into new workspace
FROM alpine:3.18
LABEL copyright='Copyright (c) 2024; Outside Labs'

RUN apk add --update --no-cache

COPY --from=builder /signals-service/Attribution.txt /Attribution.txt
COPY --from=builder /signals-service/core/res/ /res/
COPY --from=builder /signals-service/core/signals-service /signals-service

EXPOSE 59990

ENTRYPOINT ["/signals-service"]
CMD ["-cp=consul.http://edgex-core-consul:8500", "--registry"]
