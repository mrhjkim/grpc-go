# Examples

The following examples are provided to help users get started with gRPC-Go.
They are arranged as follows:

* `helloworld` - a simple example showing a basic client and server
* `routeguide` - a more complicated example showing different types of streaming RPCs
* `features` - a collection of examples, each focused on a single gRPC feature

`data` is a directory containing data used by the examples, e.g. TLS certificates.

# Docker에서 실행하는 방법
## helloworld
cd .. // grpc-go 디렉토리로 이동
docker build -t <TAG> -f examples/helloworld/Dockerfile .
