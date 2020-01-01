# litecoind

Docker hub: [depach/litecoind](https://hub.docker.com/r/depach/litecoind)

Docker image of Litecoin core litecoin built from sources on ubuntu

forked from [jamesob/docker-bitcoind](https://github.com/jamesob/docker-bitcoind) - many thanks!

## Quick start

Requires that [Docker be installed](https://docs.docker.com/install/) on the host machine.

```bash
# Create some directory where your bitcoin data will be stored.
$ mkdir /home/youruser/litecoin_data

$ docker run --name litecoind -d \
   --env 'LTC_RPCUSER=foo' \
   --env 'LTC_RPCPASSWORD=password' \
   --env 'LTC_TXINDEX=1' \
   --volume /home/youruser/litecoin_data:/root/.litecoin \
   -p 127.0.0.1:9432:9432 \
   --publish 9432:9432 \
   depach/litecoind

$ docker logs -f litecoind
[ ... ]
```


## Configuration

A custom `litecoin.conf` file can be placed in the mounted data directory.
Otherwise, a default `litecoin.conf` file will be automatically generated based
on environment variables passed to the container:

| name | default |
| ---- | ------- |
| LTC_RPCUSER | btc |
| LTC_RPCPASSWORD | changemeplz |
| LTC_RPCPORT | 9432 |
| LTC_RPCALLOWIP | 172.0.0.0/8 |
| LTC_RPCCLIENTTIMEOUT | 30 |
| LTC_DISABLEWALLET | 1 |
| LTC_TXINDEX | 0 |
| LTC_TESTNET | 0 |
| LTC_DBCACHE | 0 |
| LTC_ZMQPUBHASHTX | tcp://0.0.0.0:28333 |
| LTC_ZMQPUBHASHBLOCK | tcp://0.0.0.0:28333 |
| LTC_ZMQPUBRAWTX | tcp://0.0.0.0:28333 |
| LTC_ZMQPUBRAWBLOCK | tcp://0.0.0.0:28333 |


## Daemonizing

The smart thing to do if you're daemonizing is to use Docker's [builtin
restart
policies](https://docs.docker.com/config/containers/start-containers-automatically/#use-a-restart-policy),
but if you're insistent on using systemd, you could do something like

```bash
$ cat /etc/systemd/system/litecoind.service

# litecoind.service #######################################################################
[Unit]
Description=Litecoind
After=docker.service
Requires=docker.service

[Service]
ExecStartPre=-/usr/bin/docker kill litecoind
ExecStartPre=-/usr/bin/docker rm litecoind
ExecStartPre=/usr/bin/docker pull depach/litecoind
ExecStart=/usr/bin/docker run \
    --name litecoind \
    -p 9432:9432 \
    -p 127.0.0.1:9432:9432 \
    -v /data/litecoind:/root/.litecoin \
    depach/litecoind
ExecStop=/usr/bin/docker stop litecoind
```

to ensure that litecoind continues to run.


## Alternatives

- [docker-bitcoind](https://github.com/kylemanna/docker-bitcoind): sort of the
  basis for this repo, but configuration is a bit more confusing.
