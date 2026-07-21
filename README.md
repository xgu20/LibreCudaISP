# LibreCudaISP

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
![CUDA 11+](https://img.shields.io/badge/CUDA-11%2B-76B900?logo=nvidia&logoColor=white)
![C++17](https://img.shields.io/badge/C%2B%2B-17-00599C?logo=cplusplus)

**LibreCudaISP is an open-source camera image signal processor (ISP) written
in CUDA C++ for NVIDIA GPUs.** It converts Bayer RAW sensor captures—including
MIPI RAW10 and unpacked RAW—into RGB images with a GPU-accelerated pipeline.

The project implements common camera ISP stages such as black-level
correction, dead-pixel correction, lens shading correction, automatic white
balance (AWB), Bayer demosaicing, color correction matrices (CCM), tone
mapping, gamma correction, YUV denoising, and edge enhancement. It is designed
for RAW image processing experiments, camera algorithm development, CUDA
optimization, and per-stage performance benchmarking.

> [!NOTE]
> This is an experimental ISP, not a production pipeline calibrated for every
> sensor. Color accuracy depends on sensor-specific tuning.

![LibreCudaISP pipeline](docs/pipeline.svg)

## Features

- MIPI RAW10, unpacked 8-bit, and unpacked 16-bit input
- RGGB, BGGR, GRBG, and GBRG Bayer patterns
- Black-level correction, OECF, DPC, LSC, white balance, and demosaic
- CCM, tone mapping, sRGB gamma, YUV denoise, and edge enhancement
- Per-stage CUDA timing and reusable pipeline buffers
- GoogleTest correctness and 4K performance tests

See [TODO.md](TODO.md) for planned work.

## Requirements

- NVIDIA GPU with CUDA support
- CUDA Toolkit 11 or newer
- CMake 3.25 or newer
- C++17 host compiler
- Network access during the first CMake configure

## Quick start

```bash
git clone https://github.com/xgu20/LibreCudaISP.git
cd LibreCudaISP

python3 -m pip install PyYAML
# Download sample RAW files and generate their JSON sidecars.
python3 tools/download_data.py infinite

cmake -S . -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j

./build/libreisp \
  data/infinite/Indoor1_2592x1536_10bit_GRBG.raw \
  output.png
```

The result is written to `output.png`. The downloader creates the matching JSON
sidecar automatically.

## Sample output

Outputs produced from the five tuned 10-bit Infinite-ISP samples using Gray
World AWB and their matching upstream CCMs (click an image to view it at full
resolution):

<p align="center">
  <a href="docs/assets/infinite/Indoor1.png"><img src="docs/assets/infinite/Indoor1.png" alt="Indoor1 output" width="49%"></a>
  <a href="docs/assets/infinite/Outdoor1.png"><img src="docs/assets/infinite/Outdoor1.png" alt="Outdoor1 output" width="49%"></a>
  <a href="docs/assets/infinite/Outdoor2.png"><img src="docs/assets/infinite/Outdoor2.png" alt="Outdoor2 output" width="49%"></a>
  <a href="docs/assets/infinite/Outdoor3.png"><img src="docs/assets/infinite/Outdoor3.png" alt="Outdoor3 output" width="49%"></a>
  <a href="docs/assets/infinite/Outdoor4.png"><img src="docs/assets/infinite/Outdoor4.png" alt="Outdoor4 output" width="49%"></a>
</p>

## Download data

RAW datasets are not committed to this repository. Download the Infinite-ISP
samples with:

```bash
python3 -m pip install PyYAML
python3 tools/download_data.py infinite
```

To download from Kaggle, install the optional dependencies, configure Kaggle
authentication, and choose `kaggle` or `all`:

```bash
python3 -m pip install -r requirements-data.txt
python3 tools/download_data.py kaggle
# Or download both Infinite-ISP and Kaggle:
python3 tools/download_data.py all
```

The Infinite-ISP command also downloads the matching upstream tuning YAML files
and generates JSON sidecars. Kaggle files may require dataset-specific format
conversion and sidecar generation.

For dataset sources, authentication, tuning conversion, and directory layout,
see [data/README.md](data/README.md).

## Custom RAW files

Each RAW file requires a JSON sidecar with the same stem:

```text
data/example.raw
data/example.json
```

```bash
./build/libreisp data/example.raw output.png
```

Generic defaults are stored in
[config/golden_tuning.json](config/golden_tuning.json). Sensor and scene tuning
belongs in the sidecar and overrides the generic profile. See
[data/README.md](data/README.md) for the sidecar schema and conversion tools.

## Documentation

- [Data download and sidecars](data/README.md)
- [Testing and benchmarking](docs/testing.md)
- [Pipeline diagram source](docs/pipeline.mmd)
- [YUV denoise algorithm](docs/algorithms/yuv_denoise.md)
- [Chroma-noise troubleshooting](docs/troubleshooting/chroma_noise_on_green_screen.md)
- [Roadmap](TODO.md)

## Contributing

Issues and pull requests are welcome. Please build the project and follow the
[testing guide](docs/testing.md) before submitting changes. Formatting follows
the repository's `.clang-format` file.

## Third-party components

- [nlohmann/json](https://github.com/nlohmann/json)
- [GoogleTest](https://github.com/google/googletest)
- [stb_image_write](https://github.com/nothings/stb)

Each component remains subject to its own license terms.

CUDA is a trademark of NVIDIA Corporation. LibreCudaISP is an independent project
and is not affiliated with or endorsed by NVIDIA.

## License

This project is licensed under the [MIT License](LICENSE).
