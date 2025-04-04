# Whisper

## Prerequisites

Please note the package versions needed for using Whisper in the `requirements.txt` file that fits your scenario.
- `requirements-cpu.txt`
  - For running Whisper on CPU
- `requirements-cuda.txt`
  - For running Whisper on CUDA
  - Note that `torch` with CUDA enabled is not installed automatically. This is because `torch` should be installed with the CUDA version used on your machine. Please visit [the PyTorch website](https://pytorch.org/get-started/locally/) to download the `torch` version that is used with the CUDA version installed on your machine and satisfies the requirement listed in the file.
- `requirements.txt`
  - Package versions needed in each of the above files

In addition to the above packages, you will need to install `ffmpeg` on your machine. Visit the [FFmpeg website](https://ffmpeg.org/) for details. You can also install it natively using package managers.

- Linux: `sudo apt-get install ffmpeg`
- MacOS: `sudo brew install ffmpeg`
- Windows: Download from website

**FFMPEG includes numerous codecs, many of which are likely not used by your product/service. Microsoft engineering teams using FFMPEG must build FFMPEG to remove all the unneeded and unused codecs. Including codecs in your product/service, even if not used, can create patent risk for Microsoft. You are responsible for building FFMPEG in a way that follows this codec guidance.**

## Exporting Whisper

It is recommended to export Whisper for ONNX Runtime GenAI as you will get much more granular control over the generation loop and you can produce word-level timestamps. The alternative option is to export Whisper with the beam search op in the ONNX model, which does not provide these extra benefits and may have additional limitations.

To see all available options:
```
# From source:
$ python3 -m models.whisper.convert_to_onnx --help

# From wheel:
$ python3 -m onnxruntime.transformers.models.whisper.convert_to_onnx --help
```

## Exporting Whisper for [ONNX Runtime GenAI](https://github.com/microsoft/onnxruntime-genai)

To export Whisper for ONNX Runtime GenAI, you can use the `convert_to_onnx.py` script.

```
# From source
$ git clone https://github.com/microsoft/onnxruntime
$ cd onnxruntime/onnxruntime/python/tools/transformers/
$ python3 -m models.whisper.convert_to_onnx -m openai/whisper-large-v3-turbo --output whisper-turbo --use_external_data_format --no_beam_search_op --output_cross_qk

# From wheel
$ python3 -m onnxruntime.transformers.models.whisper.convert_to_onnx -m openai/whisper-large-v3-turbo --output whisper-turbo --use_external_data_format --no_beam_search_op --output_cross_qk
```

Here are some additional examples for exporting Whisper for ONNX Runtime GenAI.

Export + Optimize for FP32 CPU
```
# From source:
$ python3 -m models.whisper.convert_to_onnx -m openai/whisper-large-v3-turbo --output whisper-turbo --precision fp32 --provider cpu --use_external_data_format --optimize_onnx --no_beam_search_op --output_cross_qk

# From wheel:
$ python3 -m onnxruntime.transformers.models.whisper.convert_to_onnx -m openai/whisper-large-v3-turbo --output whisper-turbo --precision fp32 --provider cpu --use_external_data_format --optimize_onnx --no_beam_search_op --output_cross_qk
```

Export + Optimize for FP32 CUDA
```
# From source:
$ python3 -m models.whisper.convert_to_onnx -m openai/whisper-large-v3-turbo --output whisper-turbo --precision fp32 --provider cuda --use_gpu --use_external_data_format --optimize_onnx --no_beam_search_op --output_cross_qk

# From wheel:
$ python3 -m onnxruntime.transformers.models.whisper.convert_to_onnx -m openai/whisper-large-v3-turbo --output whisper-turbo --precision fp32 --provider cuda --use_gpu --use_external_data_format --optimize_onnx --no_beam_search_op --output_cross_qk
```

Export + Optimize for FP16 CUDA
```
# From source:
$ python3 -m models.whisper.convert_to_onnx -m openai/whisper-large-v3-turbo --output whisper-turbo --precision fp16 --provider cuda --use_gpu --use_external_data_format --optimize_onnx --no_beam_search_op --output_cross_qk

# From wheel:
$ python3 -m onnxruntime.transformers.models.whisper.convert_to_onnx -m openai/whisper-large-v3-turbo --output whisper-turbo --precision fp16 --provider cuda --use_gpu --use_external_data_format --optimize_onnx --no_beam_search_op --output_cross_qk
```

## Exporting Whisper with Beam Search

There are several ways to export Whisper with beam search.

### Option 1: from convert_to_onnx

```
# From source
$ git clone https://github.com/microsoft/onnxruntime
$ cd onnxruntime/onnxruntime/python/tools/transformers/
$ python3 -m models.whisper.convert_to_onnx -m openai/whisper-large-v3-turbo --output whisper-turbo --use_external_data_format

# From wheel
$ python3 -m onnxruntime.transformers.models.whisper.convert_to_onnx -m openai/whisper-large-v3-turbo --output whisper-turbo --use_external_data_format
```

### Option 2: end-to-end model from [Olive](https://github.com/microsoft/Olive/tree/main/examples/whisper)

Please follow the [README instructions](https://github.com/microsoft/Olive/tree/main/examples/whisper#prerequisites) in Olive.

### Option 3: from [Hugging Face Optimum](https://github.com/huggingface/optimum)

Run the following Python code to export:

```
from optimum.onnxruntime import ORTModelForSpeechSeq2Seq

model_name = "openai/whisper-large-v3-turbo"
model = ORTModelForSpeechSeq2Seq.from_pretrained(
    model_name,
    export=True,
)
model.save_pretrained(model_name.split("/")[-1] + "-onnx")
```

## Exporting + Optimizing + Quantizing Whisper with Beam Search

Here are some additional examples for exporting Whisper with beam search.

Export with Forced Decoder Input Ids
```
# From source:
$ python3 -m models.whisper.convert_to_onnx -m openai/whisper-large-v3-turbo --output whisper-turbo --use_external_data_format --use_forced_decoder_ids

# From wheel:
$ python3 -m onnxruntime.transformers.models.whisper.convert_to_onnx -m openai/whisper-large-v3-turbo --output whisper-turbo --use_external_data_format --use_forced_decoder_ids
```

Export + Optimize for FP32
```
# From source:
$ python3 -m models.whisper.convert_to_onnx -m openai/whisper-large-v3-turbo --output whisper-turbo --use_external_data_format --optimize_onnx --precision fp32

# From wheel:
$ python3 -m onnxruntime.transformers.models.whisper.convert_to_onnx -m openai/whisper-large-v3-turbo --output whisper-turbo --use_external_data_format --optimize_onnx --precision fp32
```

Note: FP32 CPU is not compatible with `--output_cross_qk`.

Export + Optimize for FP16 GPU
```
# From source:
$ python3 -m models.whisper.convert_to_onnx -m openai/whisper-large-v3-turbo --output whisper-turbo --use_external_data_format --optimize_onnx --precision fp16 --use_gpu --provider cuda

# From wheel:
$ python3 -m onnxruntime.transformers.models.whisper.convert_to_onnx -m openai/whisper-large-v3-turbo --output whisper-turbo --use_external_data_format --optimize_onnx --precision fp16 --use_gpu --provider cuda
```

Export + Quantize for INT8
```
# From source:
$ python3 -m models.whisper.convert_to_onnx -m openai/whisper-large-v3-turbo --output whisper-turbo --use_external_data_format --precision int8 --quantize_embedding_layer

# From wheel:
$ python3 -m onnxruntime.transformers.models.whisper.convert_to_onnx -m openai/whisper-large-v3-turbo --output whisper-turbo --use_external_data_format --precision int8 --quantize_embedding_layer
```

Note: INT8 CPU is not compatible with `--output_cross_qk`.

## Benchmark Whisper

Here are some examples of how you can benchmark Whisper across various end-to-end (E2E) implementations.

### Variants

1. PyTorch without `torch.compile`, FP32
```
python3 -m models.whisper.benchmark \
    --benchmark-type hf-pt-eager \
    --audio-path 1272-141231-0002.mp3 \
    --model-name openai/whisper-large-v3-turbo \
    --precision fp32 \
    --device cpu
```

2. PyTorch with `torch.compile`, FP16
```
python3 -m models.whisper.benchmark \
    --benchmark-type hf-pt-compile \
    --audio-path 1272-141231-0002.mp3 \
    --model-name openai/whisper-large-v3-turbo \
    --precision fp16 \
    --device cuda
```

3. Optimum + ONNX Runtime, FP32, export via Optimum
```
python3 -m models.whisper.benchmark \
    --benchmark-type hf-ort \
    --audio-path 1272-141231-0002.mp3 \
    --model-name openai/whisper-large-v3-turbo \
    --hf-ort-dir-path ./whisper-large-v3-turbo-onnx/ \
    --precision fp32 \
    --device cpu
```

4. ONNX Runtime, FP32, export via Olive or convert_to_onnx
```
python3 -m models.whisper.benchmark \
    --benchmark-type ort \
    --audio-path 1272-141231-0002.mp3 \
    --model-name openai/whisper-large-v3-turbo \
    --ort-model-path ./wlarge-fp32/whisper-large-v3-turbo_beamsearch.onnx \
    --precision fp32 \
    --device cpu
```

5. ONNX Runtime, FP16, export via Olive or convert_to_onnx
```
python3 -m models.whisper.benchmark \
    --benchmark-type ort \
    --audio-path 1272-141231-0002.mp3 \
    --model-name openai/whisper-large-v3-turbo \
    --ort-model-path ./wlarge-fp32/whisper-large_all.onnx \
    --precision fp16 \
    --device cuda
```

6. ONNX Runtime, INT8, export via Olive or convert_to_onnx
```
python3 -m models.whisper.benchmark \
    --benchmark-type ort \
    --audio-path 1272-141231-0002.mp3 \
    --model-name openai/whisper-large-v3-turbo \
    --ort-model-path ./wlarge-fp32/whisper-large-v3-turbo_all.onnx \
    --precision fp32 \
    --device cpu
```

You can profile a variant by adding the `--profile` flag.

### Benchmark All

You can use `benchmark_all.py` to benchmark across various platforms and automatically store the results in a CSV file. Here is an example.

```
python3 -m models.whisper.benchmark_all \
    --audio-path ./whisper-test-audios/ \
    --hf-pt-eager \
    --hf-pt-compile \
    --hf-ort-dir-path ./whisper-large-v3-turbo-onnx/ \
    --ort-model-path ./wlarge-fp32/whisper-large-v3-turbo_all.onnx \
    --model-name openai/whisper-large-v3-turbo \
    --precision fp32 \
    --device cpu
```

### Benchmarking on NVIDIA A100

Here is a benchmark for an MP3 file with 20.7s of audio.

#### FP16

| Engine          | Size     | Per-Token Latency | Real-Time Factor |
| --------------- | -------- | ----------------- | ---------------- |
| PyTorch eager   | Tiny     | 4.697 ms/token    | 0.004697         |
| PyTorch compile | Tiny     | 3.406 ms/token    | 0.003406         |
| ONNX Runtime    | Tiny     | 0.746 ms/token    | 0.000746         |
| PyTorch eager   | Medium   | 17.837 ms/token   | 0.017387         |
| PyTorch compile | Medium   | 18.124 ms/token   | 0.018124         |
| ONNX Runtime    | Medium   | 3.894 ms/token    | 0.003894         |
| PyTorch eager   | Large v2 | 23.470 ms/token   | 0.023470         |
| PyTorch compile | Large v2 | 23.146 ms/token   | 0.023146         |
| ONNX Runtime    | Large v2 | 6.262 ms/token    | 0.006262         |

#### FP32

| Engine          | Size     | Per-Token Latency | Real-Time Factor |
| --------------- | -------- | ----------------- | ---------------- |
| PyTorch eager   | Tiny     | 6.220 ms/token    | 0.006220         |
| PyTorch compile | Tiny     | 3.944 ms/token    | 0.003944         |
| ONNX Runtime    | Tiny     | 1.545 ms/token    | 0.001545         |
| PyTorch eager   | Medium   | 19.093 ms/token   | 0.019093         |
| PyTorch compile | Medium   | 20.459 ms/token   | 0.020459         |
| ONNX Runtime    | Medium   | 9.440 ms/token    | 0.009440         |
| PyTorch eager   | Large v2 | 25.844 ms/token   | 0.025844         |
| PyTorch compile | Large v2 | 26.397 ms/token   | 0.026397         |
| ONNX Runtime    | Large v2 | 7.492 ms/token    | 0.007492         |
