# Juno

[![Build Status](https://travis-ci.org/fissionlabsio/juno.svg?branch=master)](https://travis-ci.org/fissionlabsio/juno)
[![Go Report Card](https://goreportcard.com/badge/github.com/fissionlabsio/juno)](https://goreportcard.com/report/github.com/fissionlabsio/juno)

> Juno is a Cosmos Hub blockchain data aggregator and exporter that provides the
> ability for developers and clients to query for indexed chain data.

## Table of Contents

- [Juno](#juno)
  - [Table of Contents](#table-of-contents)
  - [Background](#background)
  - [Versions](#versions)
  - [Install](#install)
  - [Usage](#usage)
  - [Schemas](#schemas)
  - [Future Improvements](#future-improvements)
  - [Contributing](#contributing)
  - [License](#license)

## Background

Juno is a Cosmos Hub data aggregator and exporter. In other words, it can be seen
as an ETL layer atop of the Cosmos Hub. It improves the Hub's data accessibility
by providing an indexed PostgreSQL database exposing aggregated resources and
models such as blocks, validators, pre-commits, transactions, and various aspects
of the governance module. Juno is meant to run with a GraphQL layer on top so that
it even further eases the ability for developers and downstream clients to answer
queries such as "what is the average gas cost of a block?" while also allowing
them to compose more aggregate and complex queries.

> 🎉 Fission Labs publishes a public GraphQL API for the Cosmos Hub that
> can be found [here](https://gaiaql.fissionlabs.io/) 🎉

## Versions

- v0.0.5 - v0.0.6
  - Network: cosmoshub-3
- v0.0.4
  - Network: cosmoshub-2
- v0.0.1 - v0.0.3:
  - Network: cosmoshub-1

## Install

Juno takes a simple configuration. It needs to only know about a PostgreSQL
database instance and a Tendermint RPC node.

Example:

```toml
rpc_node = "<rpc-ip/host>:<rpc-port>"
client_node = "<client-ip/host>:<client-port>"

[database]
host = "<db-host>"
port = <db-port>
name = "<db-name>"
user = "<db-user>"
password = "<db-password>"
ssl_mode = "<ssl-mode>"
```

To install the binary run `make install`.

**Note**: Requires [Go 1.13+](https://golang.org/dl/)

## Usage

Juno internally runs a single worker that consumes from a single queue. The
queue contains block heights to aggregate and export to a PostgreSQL database.
Juno will start a new block even listener where for each new block, it will
enqueue the height. A worker listens for new heights and queries for various data
related to the block height to persist. For each block height, juno will persist
the block, the validators that committed/signed the block, all the pre-commits
for the block and the transactions in the block.

In addition, it will also sync missing blocks from `--start-height` to the latest
known height.

```shell
$ juno /path/to/config.toml [flags]
```

## Schemas

The schema definitions are contained in the `schema/` directory. Note, these
schemas are not necessarily optimal and are subject to change! However, feel
free to fork this tool and expand upon the schemas as you see fit. Any tweaks
will most likely require adjustments to the `database` wrapper.

## Future Improvements

- Unit and integration tests
- Persist governance proposals and tallies
- Improve the db migration process
- Implement better retry logic on failed queries
- Implement a docker-compose setup that allows for complete bootstrapping
- Improve modularity and remove any assumptions about the origin chain so Juno
can sync with any Tendermint-based chain

## Contributing

Contributions are welcome! Please open an Issues or Pull Request for any changes.

## License

[MPL 2.0](https://www.mozilla.org/en-US/MPL/2.0/FAQ/)
