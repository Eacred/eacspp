wsrpc
=====

Module `github.com/jrick/wsrpc` provides a partial implementation of a JSON-RPC
2.0 websocket client.  Inspired by net/rpc, clients call methods by their name
with arguments and return values marshaled by encoding/json.  The client may be
used to create convenience calls with types specific to an application.

Receiving notifications is supported but it is up to the caller to unmarshal the
JSON-RPC parameters into meaningful data.

This module currently does not implement JSON-RPC 2.0 request batching or keyed
request parameters when performing calls.

## CLI

A command line tool is provided to perform individual websocket JSON-RPCs
against a server.

A JSON array must be used to pass parameters in a method call.

```
$ wsrpc -h
usage: wsrpc address [flags] method [arg]
  -c string
        Root certificate PEM file
  -p string
        Password
  -u string
        User
$ wsrpc wss://ecrd0.i.zettaport.com:9109/ws -c ecrd0.pem -u jrick -p sekrit getinfo
{
  "version": 1050000,
  "protocolversion": 6,
  "blocks": 324795,
  "timeoffset": 0,
  "connections": 65,
  "proxy": "",
  "difficulty": 19920803496.64989,
  "testnet": false,
  "relayfee": 0.0001,
  "errors": ""
}
$ wsrpc wss://ecrd0.i.zettaport.com:9109/ws -c ecrd0.pem -u jrick -p sekrit getblockhash '[324795]'
"0000000000000000235b1210221d412c428237175dbb0aef202277d1706b9312"
```


The `wsrpc-agent` tool can be optionally used to manage persistent connections.
Usage of the agent is similar to `ssh-agent`.  The agent can exec a command,
running only so long as the command continues to run, or by daemonizing and
using eval to set the environment of the Bourne shell:

```
$ wsrpc-agent -h
usage (exec):   wsrpc-agent cmd [args...]
usage (daemon): eval $(wsrpc-agent)
```

Once running, `wsrpc` will use the `WSRPCAGENT_SOCK` and `WSRPCAGENT_AUTH`
environment variables to perform IPC with the agent.  TLS and RPC authentication
flags only apply to the initial connection.

```
$ eval `wsrpc-agent`
Agent listening on /tmp/wsrpc732266934/agent.19981
$ wsrpc wss://ecrd0.i.zettaport.com:9109/ws -c ecrd0.pem -u jrick -p sekrit getblockhash '[324795]'
"0000000000000000235b1210221d412c428237175dbb0aef202277d1706b9312"
$ wsrpc wss://ecrd0.i.zettaport.com:9109/ws getblockhash '[324795]'
"0000000000000000235b1210221d412c428237175dbb0aef202277d1706b9312"
```

## License

wsrpc is licensed under the permissive
[Blue Oak Model License 1.0.0](https://blueoakcouncil.org/license/1.0.0).
