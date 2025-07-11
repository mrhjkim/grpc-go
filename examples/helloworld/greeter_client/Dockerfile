# Copyright 2024 gRPC authors.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#    http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

# Dockerfile for building the example server. To build the image, run the
# following command from grpc-go/ directory:
# docker build -t <TAG> -f xxx/xxx/Dockerfile .

FROM golang:1.23-alpine as build
RUN apk --no-cache add curl

# 설정: 원하는 아키텍처
ARG GOARCH=amd64
ARG GOOS=linux

# Make a grpc-go directory and copy the repo into it.
WORKDIR /go/src/grpc-go
COPY . .

# Build a static binary without cgo so that we can copy just the binary in the
# final image, and can get rid of the Go compiler and gRPC-Go dependencies.
RUN cd examples/helloworld/greeter_client && GOARCH=$GOARCH GOOS=$GOOS go build -tags osusergo,netgo .

FROM alpine
RUN apk --no-cache add curl
COPY --from=build /go/src/grpc-go/examples/helloworld/greeter_client/greeter_client .
ENV GRPC_GO_LOG_VERBOSITY_LEVEL=99
ENV GRPC_GO_LOG_SEVERITY_LEVEL="info"
ENTRYPOINT ["./greeter_client"]
