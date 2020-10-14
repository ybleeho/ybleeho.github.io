---
title: A brief introduction of gRPC in Typescript
date: 2020-10-05 10:17:41
tags:
---

### Introduction to gRPC

In gRPC, a client application can directly call a method on a server application on a different machine as if it were a local object, making it easier for you to create distributed applications and services. As in many RPC systems, gRPC is based around the idea of defining a service, specifying the methods that can be called remotely with their parameters and return types. On the server side, the server implements this interface and runs a gRPC server to handle client calls. On the client side, the client has a stub (referred to as just a client in some languages) that provides the same methods as the server.

<img src="/images/gPRC.svg" style="zoom:67%;" />



By default, gRPC uses HTTP2 for transporting data streams between remote computers. Protocal buffers developed by Google are used for processing the data. Protocol buffers are saved as simple text files with the extension `.proto`.

gRPC lets you define four kinds of service method:

* Unary RPCs where the client sends a single request to the server and gets a single response back, just like a normal function call.

  ```
  rpc SayHello(HelloRequest) returns (HelloResponse);
  ```

* Server streaming RPCs where the client sends a request to the server and gets a stream to read a sequence of messages back. The client reads from the returned stream until there are no more messages. gRPC guarantees message ordering within an individual RPC call.

  ```
  rpc LotsOfReplies(HelloRequest) returns (stream HelloResponse);
  ```

* Client streaming RPCs where the client writes a sequence of messages and sends them to the server, again using a provided stream. Once the client has finished writing the messages, it waits for the server to read them and return its response. Again gRPC guarantees message ordering within an individual RPC call.

  ```
  rpc LotsOfGreetings(stream HelloRequest) returns (HelloResponse);
  ```

* Bidirectional streaming RPCs where both sides send a sequence of messages using a read-write stream. The two streams operate independently, so clients and servers can read and write in whatever order they like: for example, the server could wait to receive all the client messages before writing its responses, or it could alternately read a message then write a message, or some other combination of reads and writes. The order of messages in each stream is preserved.

  ```
  rpc BidiHello(stream HelloRequest) returns (stream HelloResponse);
  ```

### Use with Typescript

#### Init

```
yarn init
yarn add grpc grpc-tools grpc_tools_node_protoc_ts ts-node-dev typescript
tsc --init
```

#### Create proto file

create user.proto file 

```protobuf
syntax = "proto3";
package userservice;

message User {
    string name = 1;
    int32 age = 2;
    string email = 3;
}

message GetUserByAgeRequest {
    int32 age = 1;
}

message GetUserByAgeResponse {
    repeated User users = 1;
}

service UserService {
    rpc GetUserByAge(GetUserByAgeRequest) returns (GetUserByAgeResponse) {}
}
```

#### Generate code from the proto file

Create gen.sh file

```
npx grpc_tools_node_protoc \
--js_out=import_style=commonjs,binary:./ \
--grpc_out=./ \
--ts_out=./ \
user.proto
```

Make sure this file is executable 

```
chmod +x gen.sh
./gen.sh
```

The script will generate two file for js and two file for ts.The grpc one define our service and the other one contain our object.

Create server.ts file and create our gRPC server

 ```typescript
import grpc from 'grpc';
import { IUserServiceServer, UserServiceService } from './user_grpc_pb';
import { User, GetUserByAgeResponse, GetUserByAgeRequest } from './user_pb';
const server = new grpc.Server();
server.addService<IUserServiceServer>(UserServiceService, new UserService());
server.bind('0.0.0.0:3302', grpc.ServerCredentials.createInsecure());
server.start()
 ```

Then write the UserService class implements IUserServiceServer

```typescript
import grpc from 'grpc';
import { IUserServiceServer, UserServiceService } from './user_grpc_pb';
import { User, GetUserByAgeResponse, GetUserByAgeRequest } from './user_pb';
const server = new grpc.Server();
const allUsers: User.AsObject[] = [
    {
        name: 'leeho',
        age: 15,
        email: 'ybleeho@gmail.com'
    },
    {
        name: 'leeho',
        age: 20,
        email: 'ybleeho2@gmail.com'
    },
    {
        name: 'leeho',
        age: 30,
        email: 'ybleeho3@gmail.com'
    },
]
class UserService implements IUserServiceServer {
    public getUserByAge(
        call: grpc.ServerUnaryCall<GetUserByAgeRequest>,
        callback: grpc.sendUnaryData<GetUserByAgeResponse>
    ): void {
        const {request} = call;
        const age = request.getAge();
        const users = allUsers.filter(user => user.age >= age);
        const response = new GetUserByAgeResponse();
        const result: User[] = [];
        for(const user of users) {
            const userGrpc: User = new User();
            userGrpc.setName(user.name);
            userGrpc.setAge(user.age);
            userGrpc.setEmail(user.email);
            result.push(userGrpc);
        }
        response.setUsersList(result);
        callback(null, response);
    }
}

server.addService<IUserServiceServer>(UserServiceService, new UserService());
server.bind('0.0.0.0:3302', grpc.ServerCredentials.createInsecure());
server.start()
```

Last edit the `package.json` as below:

```json
{
  "name": "grpc",
  "version": "1.0.0",
  "main": "index.js",
  "license": "MIT",
  "scripts": {
    "start": "ts-node-dev ./server.ts"
  },
  "dependencies": {
    "grpc": "^1.24.3",
    "grpc-tools": "^1.9.1",
    "grpc_tools_node_protoc_ts": "^4.1.5",
    "ts-node-dev": "^1.0.0-pre.63",
    "typescript": "^4.0.3"
  }
}
```

Than start the server with `yarn start` command.

We can test the gRPC server with [BloomRPC](https://github.com/uw-labs/bloomrpc).

You can see the results as blow.

![gPRC-test-result](/images/gPRC-test-result.png)

