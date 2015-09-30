---
name: ENode URL Format
category: 
---

An Ethereum node can be described with a URL scheme "enode".

The hexadecimal node ID is encoded in the username portion of the URL, separated from the host by an @ sign. The hostname can only be given as an IP address, DNS domain names are not allowed. The port
in the host name section is the TCP listening port. If the TCP and UDP (discovery) ports differ, the UDP port is specified as query parameter "discport".

In the following example, the node URL describes a node with IP address `10.3.58.6`, TCP listening port `30303` and UDP discovery port `30301`.

```
enode://6f8a80d14311c39f35f516fa664deaaaa13e85b2f7493f37f6144d86991ec012937307647bd3b9a82abe2974e1407241d54947bbb39763a4cac9f77166ad92a0@10.3.58.6:30303?discport=30301
```

The enode url scheme is used by the Node discovery protocol and can be used in the `bootnodes` command line option of the client or as the argument to `suggestPeer(nodeURL)` function in the JSRE.

See also 
- https://github.com/ethereum/go-ethereum/wiki/Command-Line-Options
- https://github.com/ethereum/go-ethereum/wiki/JavaScript-Console
