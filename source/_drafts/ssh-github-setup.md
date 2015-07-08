title: Setup ssh for Github
date: 2015-07-08 01:07:53
tags:
---
$ ssh git@github.com:fsworld009/blog.git
ssh: Could not resolve hostname github.com:fsworld009/blog.git: Name or service not known

https://help.github.com/articles/error-repository-not-found/

$ ssh -T git@github.com
Permission denied (publickey).


https://help.github.com/articles/error-permission-denied-publickey/

$ ssh -vT git@github.com
OpenSSH_6.7p1, OpenSSL 1.0.2a 19 Mar 2015
debug1: Reading configuration data /etc/ssh/ssh_config
debug1: Connecting to github.com [192.30.252.131] port 22.
debug1: Connection established.
debug1: key_load_public: No such file or directory
debug1: identity file /d/dev/home/.ssh/id_rsa type -1
debug1: key_load_public: No such file or directory
debug1: identity file /d/dev/home/.ssh/id_rsa-cert type -1
debug1: key_load_public: No such file or directory
debug1: identity file /d/dev/home/.ssh/id_dsa type -1
debug1: key_load_public: No such file or directory
debug1: identity file /d/dev/home/.ssh/id_dsa-cert type -1
debug1: key_load_public: No such file or directory
debug1: identity file /d/dev/home/.ssh/id_ecdsa type -1
debug1: key_load_public: No such file or directory
debug1: identity file /d/dev/home/.ssh/id_ecdsa-cert type -1
debug1: key_load_public: No such file or directory
debug1: identity file /d/dev/home/.ssh/id_ed25519 type -1
debug1: key_load_public: No such file or directory
debug1: identity file /d/dev/home/.ssh/id_ed25519-cert type -1
debug1: Enabling compatibility mode for protocol 2.0
debug1: Local version string SSH-2.0-OpenSSH_6.7
debug1: Remote protocol version 2.0, remote software version libssh-0.7.0
debug1: no match: libssh-0.7.0
debug1: SSH2_MSG_KEXINIT sent
debug1: SSH2_MSG_KEXINIT received
debug1: kex: server->client aes128-ctr hmac-sha2-256 none
debug1: kex: client->server aes128-ctr hmac-sha2-256 none
debug1: sending SSH2_MSG_KEX_ECDH_INIT
debug1: expecting SSH2_MSG_KEX_ECDH_REPLY
debug1: Server host key: RSA 16:27:ac:a5:76:28:2d:36:63:1b:56:4d:eb:df:a6:48
debug1: Host 'github.com' is known and matches the RSA host key.
debug1: Found key in /d/dev/home/.ssh/known_hosts:1
Warning: Permanently added the RSA host key for IP address '192.30.252.131' to the list of known hosts.
debug1: SSH2_MSG_NEWKEYS sent
debug1: expecting SSH2_MSG_NEWKEYS
debug1: SSH2_MSG_NEWKEYS received
debug1: Roaming not allowed by server
debug1: SSH2_MSG_SERVICE_REQUEST sent
debug1: SSH2_MSG_SERVICE_ACCEPT received
debug1: Authentications that can continue: publickey
debug1: Next authentication method: publickey
debug1: Trying private key: /d/dev/home/.ssh/id_rsa
debug1: Trying private key: /d/dev/home/.ssh/id_dsa
debug1: Trying private key: /d/dev/home/.ssh/id_ecdsa
debug1: Trying private key: /d/dev/home/.ssh/id_ed25519
debug1: No more authentication methods to try.
Permission denied (publickey).





/d/dev/home  (master)
$ ssh-agent -s
SSH_AUTH_SOCK=/tmp/ssh-DfJYGF8SYcAB/agent.8624; export SSH_AUTH_SOCK;
SSH_AGENT_PID=6204; export SSH_AGENT_PID;
echo Agent pid 6204;
/d/dev/home  (master)
$ ssh-add -l
Could not open a connection to your authentication agent.

http://stackoverflow.com/questions/17846529/could-not-open-a-connection-to-your-authentication-agent

$ eval $(ssh-agent -s)
Agent pid 7688
/d/dev/home  (master)
$ ssh-add -l
The agent has no identities.

http://stackoverflow.com/questions/26505980/github-permission-denied-ssh-add-agent-has-no-identities


$ ssh-keygen -t rsa
Generating public/private rsa key pair.
(...)

/d/Program/blog  (master)
$ ssh-add
Identity added: /d/dev/home/.ssh/id_rsa (rsa w/o comment)


https://help.github.com/articles/generating-ssh-keys/#step-4-add-your-ssh-key-to-your-account
$ ssh -vT git@github.com
....
Hi fsworld009! You've successfully authenticated, but GitHub does not provide shell access.
debug1: channel 0: free: client-session, nchannels 1
Transferred: sent 3232, received 1840 bytes, in 0.1 seconds
Bytes per second: sent 45518.6, received 25914.1
debug1: Exit status 1


