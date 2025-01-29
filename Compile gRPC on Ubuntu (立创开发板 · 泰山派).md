#done

Prerequisite:
- Ubuntu 20.04.6 LTS (Notice: the processor of 泰山派 "RK3566" is **ARM architecture**)

Reference:
- https://grpc.io/docs/languages/cpp/quickstart/
## Compile gRPC step by step

Install essential packages.
```bash
sudo apt update
sudo apt upgrade
sudo apt install -y build-essential cmake autoconf libtool pkg-config
```

Download source from GitHub.
```bash
cd /userdata/Lib
git clone --recurse-submodules -b v1.69.0 --depth 1 --shallow-submodules https://github.com/grpc/grpc
```
```

Create build and install dir in opencv source dir.
```bash
cd grpc && mkdir build && cd build && mkdir install
```

Configure
```bash
cmake -DgRPC_INSTALL=ON -DgRPC_BUILD_TESTS=OFF -DCMAKE_CXX_STANDARD=17 -DCMAKE_INSTALL_PREFIX=/userdata/Lib/grpc/build/install ..
```

Build and install
```bash
sudo make
sudo make install
```

## Test (editing...)

Set up the project directory.
```bash
mkdir grpc-greeter && cd grpc-greeter 
mkdir src && mkdir build
vim CMakeLists.txt
```

Create the CMake build file.
```cmake
cmake_minimum_required(VERSION 3.16)

set(MAIN grpc-greeter)
project(${MAIN} CXX)
set(CMAKE_CXX_STANDARD 17)

# Third party libraries
set(absl_DIR /userdata/Lib/grpc/build/install/lib/cmake/absl)
set(utf8_range_DIR /userdata/Lib/grpc/build/install/lib/cmake/utf8_range)
set(protobuf_DIR /userdata/Lib/grpc/build/install/lib/cmake/protobuf)
set(re2_DIR /userdata/Lib/grpc/build/install/lib/cmake/re2)
set(c-ares_DIR /userdata/Lib/grpc/build/install/lib/cmake/c-ares)
set(gRPC_DIR /userdata/Lib/grpc/build/install/lib/cmake/grpc)
find_package(absl CONFIG REQUIRED)
find_package(utf8_range CONFIG REQUIRED)
find_package(protobuf CONFIG REQUIRED)
find_package(re2 CONFIG REQUIRED)
find_package(c-ares CONFIG REQUIRED)
find_package(gRPC CONFIG REQUIRED)
message(STATUS "Using absl-${absl_VERSION}")
message(STATUS "Using gRPC-${gRPC_VERSION}")
set(PROTOBUF_LIBPROTOBUF protobuf::libprotobuf)
set(REFLECTION gRPC::grpc++_reflection)
set(PROTOBUF_PROTOC /userdata/Lib/grpc/build/install/bin/protoc)
set(GRPC_GRPCPP gRPC::grpc++)
set(GRPC_CPP_PLUGIN_EXECUTABLE /userdata/Lib/grpc/build/install/bin/grpc_cpp_plugin)

# Proto file
set(GRPC_INTERFACE_NAME greeting_interface)
get_filename_component(proto_filepath ${CMAKE_CURRENT_SOURCE_DIR}/meta/${GRPC_INTERFACE_NAME}.proto ABSOLUTE)
get_filename_component(proto_folder ${proto_filepath} PATH)

# Generate sources
set(GREETER_PROTOCOL_DIR ${CMAKE_CURRENT_SOURCE_DIR}/src/GreeterProtocol)
set(greeting_proto_srcs ${GREETER_PROTOCOL_DIR}/${GRPC_INTERFACE_NAME}.pb.cc)
set(greeting_proto_hdrs ${GREETER_PROTOCOL_DIR}/${GRPC_INTERFACE_NAME}.pb.h)
set(greeting_grpc_srcs ${GREETER_PROTOCOL_DIR}/${GRPC_INTERFACE_NAME}.grpc.pb.cc)
set(greeting_grpc_hdrs ${GREETER_PROTOCOL_DIR}/${GRPC_INTERFACE_NAME}.grpc.pb.h)
add_custom_command(
      OUTPUT "${greeting_proto_srcs}" "${greeting_proto_hdrs}" "${greeting_grpc_srcs}" "${greeting_grpc_hdrs}"
      COMMAND ${PROTOBUF_PROTOC}
      ARGS --grpc_out "${GREETER_PROTOCOL_DIR}"
        --cpp_out "${GREETER_PROTOCOL_DIR}"
        -I "${proto_folder}"
        --plugin=protoc-gen-grpc="${GRPC_CPP_PLUGIN_EXECUTABLE}"
        "${proto_filepath}"
      DEPENDS "${proto_filepath}"
)
include_directories(${GREETER_PROTOCOL_DIR})

# Define library of gRPC interface
add_library(greeting-grpc-interface ${greeting_proto_srcs} ${greeting_proto_hdrs} ${greeting_grpc_srcs} ${greeting_grpc_hdrs})
target_link_libraries(greeting-grpc-interface
    absl::check
    ${REFLECTION}
    ${GRPC_GRPCPP}
    ${PROTOBUF_LIBPROTOBUF})

# Build targets
add_executable(greeter-server ${CMAKE_CURRENT_SOURCE_DIR}/src/GreeterServer/GreeterServer.cc)
target_link_libraries(greeter-server
    greeting-grpc-interface
    absl::check
    absl::flags
    absl::flags_parse
    absl::log
    ${REFLECTION}
    ${GRPC_GRPCPP}
    ${PROTOBUF_LIBPROTOBUF})

add_executable(greeter-client ${CMAKE_CURRENT_SOURCE_DIR}/src/GreeterClient/GreeterClient.cc)
target_link_libraries(greeter-client
    greeting-grpc-interface
    absl::check
    absl::flags
    absl::flags_parse
    absl::log
    ${REFLECTION}
    ${GRPC_GRPCPP}
    ${PROTOBUF_LIBPROTOBUF})
```

Create the protobuffer file.
```proto
syntax = "proto3";
package greeting;

// The greeting service definition.
service Greeter {
  // Sends a greeting
  rpc SayHello (HelloRequest) returns (HelloReply) {}

  rpc SayHelloStreamReply (HelloRequest) returns (stream HelloReply) {}

  rpc SayHelloBidiStream (stream HelloRequest) returns (stream HelloReply) {}
}

// The request message containing the user's name.
message HelloRequest {
  string name = 1;
}

// The response message containing the greetings
message HelloReply {
  string message = 1;
}
```

Write the source code of server.
```cpp
#include <grpcpp/ext/proto_server_reflection_plugin.h>
#include <grpcpp/grpcpp.h>
#include <grpcpp/health_check_service_interface.h>

#include <iostream>
#include <memory>
#include <string>

#include "absl/flags/flag.h"
#include "absl/flags/parse.h"
#include "absl/strings/str_format.h"

#include "greeting_interface.grpc.pb.h"

using grpc::Server;
using grpc::ServerBuilder;
using grpc::ServerContext;
using grpc::Status;
using greeting::Greeter;
using greeting::HelloReply;
using greeting::HelloRequest;

ABSL_FLAG(uint16_t, port, 50051, "Server port for the service");

// Logic and data behind the server's behavior.
class GreeterServiceImpl final : public Greeter::Service {
  Status SayHello(ServerContext* context, const HelloRequest* request,
                  HelloReply* reply) override {
    std::string prefix("Hello ");
    reply->set_message(prefix + request->name());
    return Status::OK;
  }
};

void RunServer(uint16_t port) {
  std::string server_address = absl::StrFormat("0.0.0.0:%d", port);
  GreeterServiceImpl service;

  grpc::EnableDefaultHealthCheckService(true);
  grpc::reflection::InitProtoReflectionServerBuilderPlugin();
  ServerBuilder builder;
  // Listen on the given address without any authentication mechanism.
  builder.AddListeningPort(server_address, grpc::InsecureServerCredentials());
  // Register "service" as the instance through which we'll communicate with
  // clients. In this case it corresponds to an *synchronous* service.
  builder.RegisterService(&service);
  // Finally assemble the server.
  std::unique_ptr<Server> server(builder.BuildAndStart());
  std::cout << "Server listening on " << server_address << std::endl;

  // Wait for the server to shutdown. Note that some other thread must be
  // responsible for shutting down the server for this call to ever return.
  server->Wait();
}

int main(int argc, char** argv) {
  absl::ParseCommandLine(argc, argv);
  RunServer(absl::GetFlag(FLAGS_port));
  return 0;
}
```

Write the source code of client.
```cpp
#include <grpcpp/grpcpp.h>

#include <iostream>
#include <memory>
#include <string>

#include "absl/flags/flag.h"
#include "absl/flags/parse.h"

#include "greeting_interface.grpc.pb.h"

ABSL_FLAG(std::string, target, "localhost:50051", "Server address");

using grpc::Channel;
using grpc::ClientContext;
using grpc::Status;
using greeting::Greeter;
using greeting::HelloReply;
using greeting::HelloRequest;

class GreeterClient {
 public:
  GreeterClient(std::shared_ptr<Channel> channel)
      : stub_(Greeter::NewStub(channel)) {}

  // Assembles the client's payload, sends it and presents the response back
  // from the server.
  std::string SayHello(const std::string& user) {
    // Data we are sending to the server.
    HelloRequest request;
    request.set_name(user);

    // Container for the data we expect from the server.
    HelloReply reply;

    // Context for the client. It could be used to convey extra information to
    // the server and/or tweak certain RPC behaviors.
    ClientContext context;

    // The actual RPC.
    Status status = stub_->SayHello(&context, request, &reply);

    // Act upon its status.
    if (status.ok()) {
      return reply.message();
    } else {
      std::cout << status.error_code() << ": " << status.error_message()
                << std::endl;
      return "RPC failed";
    }
  }

 private:
  std::unique_ptr<Greeter::Stub> stub_;
};

int main(int argc, char** argv) {
  absl::ParseCommandLine(argc, argv);
  // Instantiate the client. It requires a channel, out of which the actual RPCs
  // are created. This channel models a connection to an endpoint specified by
  // the argument "--target=" which is the only expected argument.
  std::string target_str = absl::GetFlag(FLAGS_target);
  // We indicate that the channel isn't authenticated (use of
  // InsecureChannelCredentials()).
  GreeterClient greeter(
      grpc::CreateChannel(target_str, grpc::InsecureChannelCredentials()));
  std::string user("world");
  std::string reply = greeter.SayHello(user);
  std::cout << "Greeter received: " << reply << std::endl;

  return 0;
}
```

Build and run the test program.
```bash
mkdir build && cd build
cmake ..
make
nohup ./build/greeter-server > server.log &
./build/greeter-client
kill -9 <pid_of_greeter-server>
```
