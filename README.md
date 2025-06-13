Built a postgres.wasm from docker container. See artifact here: https://github.com/vrslev/postgres-container2wasm/actions/runs/15632751383.

Run it with [wasmtime](https://wasmtime.dev). If you have [mise](https://mise.jdx.dev):

```
mise x wasmtime -- wasmtime run postgres.wasm
```
