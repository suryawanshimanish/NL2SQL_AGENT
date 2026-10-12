# NL2SQL Agent

A C++17 command-line natural-language-to-SQL agent with ONNX Runtime inference, SQLite execution, explicit schema context, tabulated output, and benchmark tooling.

## Features

- **<50ms target inference path** through ONNX Runtime in C++
- **Text-to-SQL routing model** loaded from `onnx_model/model.onnx`
- **SQLite3 execution** against `sample.db`
- **Schema-aware prompting** that passes table/column names into the NL2SQL pipeline
- **Latency metrics** in microseconds for full pipeline and ONNX inference
- **Beautiful table output** using `tabulate`
- **Benchmark mode** via `benchmark.py` comparing C++ CLI execution with a Python baseline
- **Graceful errors** with suggested next queries

## Build

```bash
cmake -B build
cmake --build build --config Release
```

The executable is usually `build/Release/sql_agent.exe` on Windows and `build/sql_agent` on Linux/macOS. ONNX Runtime is fetched by CMake and copied beside the executable on Windows.

## Model Export

A tiny `onnx_model/model.onnx` is present for smoke tests. To export a Hugging Face model with `optimum-cli`:

```bash
python -m pip install "optimum[onnxruntime]" transformers
python download_model.py --model mrm8488/t5-base-finetuned-wikiSQL --output onnx_model
```

Large seq2seq exports may produce multiple ONNX files and tokenizer assets. The current C++ integration loads `onnx_model/model.onnx`; replace or adapt that path when promoting to a full decoder model.

## Usage

Interactive mode:

```bash
build/Release/sql_agent.exe
```

Single query with formatted output:

```bash
build/Release/sql_agent.exe --query "total revenue"
```

Single query as JSON for automation:

```bash
build/Release/sql_agent.exe --query-json "sales by category"
```

Example JSON fields include `sql`, `schema`, `source`, `latency_us`, `onnx_latency_us`, `model_status`, `data`, and `suggestion` when relevant.

## Benchmark

```bash
python benchmark.py --exe build/Release/sql_agent.exe --iterations 5
```

This prints and writes `benchmark_results.md` automatically:

| Query | C++ (ms) | Python (ms) | Speedup |
|-------|----------|-------------|---------|
| total revenue | generated | generated | generated |
| sales by category | generated | generated | generated |
| top products | generated | generated | generated |
