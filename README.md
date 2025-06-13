This is a [failed] experiment to compile postgres docker image to wasm (13.06.2025). See artifact here: https://github.com/vrslev/postgres-container2wasm/actions/runs/15632751383.

Download [artifact from CI](https://github.com/vrslev/postgres-container2wasm/actions/runs/15632751383), run unzip:

```
unzip ~/Downloads/artifact.zip
```

Compiled in CI because it failed on my Mac.

### Wasmtime

Running with [Wasmtime](https://wasmtime.dev) fails:

```
mise x wasmtime@33.0.0 -- wasmtime --env POSTGRES_PASSWORD=pass ~/Downloads/postgres.wasm
```

Output:

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

Setting flags like `dynamic_shared_memory_type=posix` didn't help.

```
mise x wasmtime -- wasmtime --env POSTGRES_PASSWORD=pass ~/Downloads/postgres.wasm postgres -c dynamic_shared_memory_type=posix
```

### WasmEdge

Running with [WasmEdge](https://wasmedge.org):

```
export VERSION=0.14.1
curl -sSf https://raw.githubusercontent.com/WasmEdge/WasmEdge/master/utils/install.sh | bash
~/.wasmedge/bin/wasmedge compile ~/Downloads/postgres.wasm ~/Downloads/postgres_aot.wasm
~/.wasmedge/bin/wasmedge --env POSTGRES_PASSWORD=pass --enable-all ~/Downloads/postgres_aot.wasm
```

Output:

```
[2025-06-13 15:24:31.662] [warning] GC proposal is enabled, this is experimental.
[2025-06-13 15:24:31.664] [warning] component model is enabled, this is experimental.
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
running bootstrap script ... 2025-06-13 12:25:15.308 UTC [80] FATAL:  could not create shared memory segment: Function not implemented
2025-06-13 12:25:15.308 UTC [80] DETAIL:  Failed system call was shmget(key=22, size=56, 03600).
child process exited with exit code 1
initdb: removing contents of data directory "/var/lib/postgresql/data"
```

# Why

To speed up Python tests by using in-memory Postgres. Although I now see that it wouldn't work since WASM runtime would still use disk.

I like [PGLite](https://pglite.dev). There's now [python-pglite](https://github.com/wey-gu/py-pglite) which actually runs Node. This won't work if Python app is run in Docker (I don't want to install Node just to run tests). Hopefully
[pglite-bindings](https://github.com/electric-sql/pglite-bindings) will make my usecase possible :)
