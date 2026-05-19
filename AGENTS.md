# Agents

## Cursor Cloud specific instructions

### Overview

Rotel is a high-performance OpenTelemetry collector written in Rust. See `DEVELOPING.md` for build/test/run instructions and `README.md` for configuration options.

### Build gotcha: CC/CXX must be gcc/g++

The default `c++` on this VM is clang-18 which cannot find the libstdc++ headers/libs needed by the rdkafka (librdkafka) C++ build. Always build with:

```shell
CC=gcc CXX=g++ cargo build
```

These are already exported in `~/.bashrc`. If you open a new shell without sourcing bashrc, set them manually.

The `libstdc++.so` linker symlink at `/usr/lib/x86_64-linux-gnu/libstdc++.so` was created during setup; if missing, recreate it:

```shell
sudo ln -sf /usr/lib/x86_64-linux-gnu/libstdc++.so.6 /usr/lib/x86_64-linux-gnu/libstdc++.so
```

### Quick reference

- **Build**: `cargo build` (with CC/CXX set)
- **Lint**: `cargo fmt --check` and `cargo clippy`
- **Test**: `cargo nextest run` (preferred) or `cargo test`
- **Run**: `cargo run -- start --debug-log traces --exporter blackhole` (starts with blackhole exporter for local dev)
- **Send test data**: `cargo run --bin generate-otlp -- traces --http-endpoint localhost:4318`

### Notes

- Rust toolchain version is pinned in `rust-toolchain.toml` (currently 1.91.1).
- Default features include `rdkafka`, `aws_iam`, `file_receiver`, and `rust_processor`. Kafka integration tests require Docker (see `KAFKA_INTEGRATION_TESTS.md`).
- The `pyo3` feature (Python processor SDK) requires Python 3.13+ and is not enabled by default.
