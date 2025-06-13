This was a failed (13.06.2025) experiment to build a postgres.wasm from docker container. See artifact here: https://github.com/vrslev/postgres-container2wasm/actions/runs/15632751383.

Download [artifact from CI](https://github.com/vrslev/postgres-container2wasm/actions/runs/15632751383), run unzip:

```
unzip ~/Downloads/artifact.zip
```

## Wasmtime

Running with [Wasmtime](https://wasmtime.dev) fails:

```
mise x wasmtime@33.0.0 -- wasmtime --env POSTGRES_PASSWORD=pass ~/Downloads/postgres.wasm
```

Failed output:

```
The files belonging to this database system will be owned by user "postgres".
This user must also own the server process.

The database cluster will be initialized with locale "en_US.utf8".
The default database encoding has accordingly been set to "UTF8".
The default text search configuration will be set to "english".

Data page checksums are disabled.

fixing permissions on existing directory /var/lib/postgresql/data ... ok
creating subdirectories ... ok
selecting dynamic shared memory implementation ... posix
selecting default "max_connections" ... 25
selecting default "shared_buffers" ... 400kB
selecting default time zone ... Etc/UTC
creating configuration files ... ok
running bootstrap script ... 2025-06-13 12:13:00.286 UTC [80] FATAL:  could not create shared memory segment: Function not implemented
2025-06-13 12:13:00.286 UTC [80] DETAIL:  Failed system call was shmget(key=22, size=56, 03600).
child process exited with exit code 1
initdb: removing contents of data directory "/var/lib/postgresql/data"

```

## WasmEdge

Running with [WasmEdge](https://wasmedge.org):

```
export VERSION=0.14.1
curl -sSf https://raw.githubusercontent.com/WasmEdge/WasmEdge/master/utils/install.sh | bash
~/.wasmedge/bin/wasmedge --env POSTGRES_PASSWORD=pass ~/Downloads/postgres.wasm
```
