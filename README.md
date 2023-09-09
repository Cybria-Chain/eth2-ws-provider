# Eth2 Weak Subjectivity Provider Server

This repo contains a simple Eth2 "Please Read The Docs" [weak subjectivity](https://github.com/ethereum/eth2.0-specs/blob/dev/specs/phase0/weak-subjectivity.md) provider server.

## Prerequisites
- Docker Engine & Docker Compose
- Eth2 beacon node with accessible HTTP API
- Accurate system time

## Launching the server
1. Build the containers using `docker-compose build`
2. Fill in the `eth2_api` and `ws_server_graffiti` parameters in `config.yml`
3. Run the server using: `docker-compose up`.

#### Advanced Configuration:
- The default port is `80`. This may be changed by editing the port mapping for `eth2_ws_server` in `docker-compose.yml`.
- This application uses the `uwsgi` Python server. For advanced settings of `uwsgi`, load the desired configuration (such as number of processes & threads) in the `uwsgi` section of `config.yml`

#### Connecting to Beacon Node running in a Docker container
If your beacon node is running inside a Docker container on the same machine, you will have to connect to it by routing through the Docker network. The `eth2_api` parameter in `config.yml` is fed into the Python application running inside a docker container. You need to ensure that the beacon node's docker container is accessible from this container, and appropriately fill in `eth2_api`.

## User guide

The Eth2 WS server will serve the following data in JSON format:
- `current_epoch`: The current epoch number of the Eth2 network
- `is_safe`: A boolean value representing whether the Eth2 API endpoint used was operating under safe WS conditions, i.e., if the current epoch is within the safe WS period of the WS checkpoint that is provided in this response
- `ws_checkpoint`: The WS checkpoint in `block_root:epoch_number` format
- `ws_period`: The WS period as [calculated](https://github.com/ethereum/eth2.0-specs/blob/dev/specs/phase0/weak-subjectivity.md#calculating-the-weak-subjectivity-period) for the state at the WS checkpoint that is provided in this response
- `graffiti`: Optional graffiti set by the server operator

Example:
```json
{
  "current_epoch":16552,
  "is_safe":true,
  "ws_checkpoint":"0xe8e4b0170c4b9bfb09e477c754db0f5a02756859ccf1e834a39dbafbe9292f3c:15601",
  "ws_period":1188,
  "graffiti": "This is an Eth2 weak subjectivity data server"
}
```
