# SCARR CHES 2024 Artifact

This repository is only for the archival version of our CHES 2024 artifact related to our paper: "High-Performance Design Patterns and File Formats for Side-Channel Analysis".

If you want to use the current version of our software, please use the linked repository instead:

**SCARR active development repository [click here](https://github.com/decryptofy/scarr)**

# Note on Artifact and Benchmark Results

The official result of the CHES artifact review process is "IACR CHES Artifacts Functional" which is consistent with the badge we applied for. During the review process, we had a reviewer ask for full reproducibility of all benchmark results to grant us this badge. This reviewer performed the same comparison of all tested frameworks as in the paper using the following benchmark environment:

* AMD Ryzen Threadripper 3990X 64-Core with 256 GB RAM
* Ubuntu 22.04 with Python 3.10

This reviewer concludes: "_The results are consistent with the ones in the paper (taking into account the hardware differences)._"

However, IACR is unable to host large data sets on their server. In addition, external hosting by the library of Oregon State University (with DOI) is not acceptable to them for the purpose of hosting the artifact. Consequently, we point out the limitations of the official CHES artifact which is only for the functionality of SCARR itself. However, as mentioned beforehand, a reviewer reproduced the benchmark results in the paper and we thank this reviewer for taking on this additional effort.

If researchers want to reproduce the benchmarks results, please see [here](https://github.com/hsrlab/scarr-comparison).

Additional note on benchmark results: after the camera ready paper deadline, a firmware update (UEFI: 1.26 / ECP: 1.18) was installed on the Lenovo X1 Extreme Gen 4 (i7-11800H with RTX 3060) that we primarily used for benchmarking. This update substantially improved thermal management and all tested frameworks consistently perform better (mainly for Profile 2). Their relative performance to each other is still accurately reflected by the results in our paper though.

# SCARR

SCARR is a Side-Channel Analysis (SCA) framework written in Python that is optimized for performance over compressed data sets to minimize storage needs. At this early stage, SCARR should be considered experimental, i.e., API changes may happen at any time and without prior notice. In addition, only a limited set of analysis algorithms and pre-processing options is currently implemented, but this will change in the future as we hope to continue SCARR as an active open-source project.

SCARR is mainly intended for educational and research purposes. If as an individual you find SCARR useful, please contribute, give us a shout-out, or consider buying us coffee (this project currently runs on coffee only). If you are an organization and you benefit from this development, please consider making an unrestricted gift to the Hardware Security Research Lab at Oregon State University (led by Vincent Immler) to promote SCARR's continued development.

# SCARR Features

SCARR is designed to support the following:

* Fast out-of-core computations (processed data can be larger than available memory)
* Processed data can be int or float (raw oscilloscope data or digitally pre-processed)
* Multiple tiles from EM-measurements are stored in the same data set to identify Regions-of-Interest (ROIs)
* Advanced indexing for fast Trace-of-Interest (TOI) and Point-of-Interest (POI) selections
* Analysis algorithms currently include: SNR, TVLA, CPA, MIA (more to come)

SCARR also aims at maximizing I/O efficiency, including the asynchronous prefetch of (compressed) data sets.

# Installing CHES 2024 Artifact from GitHub

If you truly need the artifact version (instead of the current version of SCARR), please clone the repository and install from your local copy using:

```
python3 -m pip install .
```

Please note: the reference OS for this artifact is Ubuntu 22.04 LTS with its default Python 3.10. Additionally, the following dependencies are installed:

* numpy==1.23.5
* numba==0.56.4
* zarr[jupyter]==2.12.0
* ruff==0.1.11
* matplotlib==3.6.3
* scipy==1.10.0

Alternatively, you can run SCARR in a virtual environment. This type of setup is considered for [benchmarking](https://github.com/hsrlab/scarr-comparison) to reproduce the results from our paper.

# Usage Warning

Some computations in SCARR can push your hardware to its limits and beyond. Caution is advised when running sustained compute loads as many consumer-grade hardware is not made for this. Especially for laptops, please take into account the best practices under [USAGE.md](https://github.com/decryptofy/scarr/blob/main/USAGE.md). Additionally, SCARR does not do any memory-checking. When attempting computations that exceed the available memory, then based on OS-level settings, SCARR or other applications may be terminated by the OS, resulting in potential data loss. During heavy computations, it is time for coffee, as you cannot use your computer for anything else but SCARR. See also: [DISCLAIMER.md](https://github.com/decryptofy/scarr/blob/main/DISCLAIMER.md)

# Getting Started with SCARR

After installing SCARR and consideration of the usage warning, please proceed as follows:

* from the 'jupyter' subdirectory, select the first Jupyter notebook
* choose corresponding example data set(s) and download from Box.com: [click here](https://oregonstate.box.com/s/flpkr969do6v1h5a8qwfw5t49c7ivzgl)
* run Jupyter notebook to use SCARR

# SCARR's File Format for Side-Channel Analysis Data

[Zarr](https://zarr.dev/) is a great file format and we use its DirectoryStore as SCARR's native file format. Each data set is represented by a directory that contains the following basic structure:

* traces:
  * directory.zarr/X/Y/traces
* metadata:
  * directory.zarr/X/Y/ciphertext
  * directory.zarr/X/Y/plaintext
  * (*optional*) directory.zarr/X/Y/key

Traces can be left uncompressed or compressed. A chunking of (5000,1000) is recommended. All metadata is left uncompressed and chunked as (5000,16) for AES128. X and Y are the logical coordinates of EM side-channel measurements. Power measurements use the same structure only with /0/0/ as coordinates for /X/Y/.

We are actively supporting the "Zarr-Python Benchmarking & Performance" group to further speed-up Zarr.

# Working with Other File Formats

SCARR only works with its native format and we have no plans to support other file formats. Should you have previously recorded data in other file formats, then you need to convert these data sets to Zarr. We collect example scripts for this format conversion [here](https://oregonstate.box.com/s/flpkr969do6v1h5a8qwfw5t49c7ivzgl), e.g., to convert separate .npy files to a combined .zarr. These scripts are not actively maintained.

# Platform Compatibility

SCARR is developed with High-Performance Computing (HPC) considerations in mind. Optimum performance can rely on many aspects of its configuration and the underlying platform. The default batch size (the number of traces processed in parallel at a given point in time) is 5000. Depending on the platform and chosen analysis, other values between 1000 and 10000 may give better results. Please also take into account the following:

* We recommend CPUs with 8 or more physical (performance) cores, preferably with AVX512
* SCARR is optimized for CPUs with SMT (Hyper-Threading); otherwise, mp.pool parameters are not optimal
* A combination of performance and efficiency cores is not specifically considered in mp.pool either
* Fast, low-latency memory should be used (e.g., DDR5-6400 and CL < 32)
* SCARR should *not* be used on NUMA platforms as this degrades performance in unexpected ways
* SCARR is designed to run on Linux/Unix; Windows may work but is *not* supported
* *ulimits need to be adjusted when processing many tiles/byte-positions at the same time*

# Contributing (inbound=outbound)

This section does not apply, as we do not accept pull requests for this artifact. Instead, please check:

**SCARR active development repository [click here](https://github.com/decryptofy/scarr)**

# License

This Source Code Form is subject to the terms of the Mozilla Public License, v. 2.0. If a copy of the MPL was not distributed with this file, You can obtain one at https://mozilla.org/MPL/2.0/.
This Source Code Form is "Incompatible With Secondary Licenses", as defined by the Mozilla Public License, v. 2.0.

# Authors

SCARR was initiated and designed by Vincent Immler out of a necessity to support his teaching and research at Oregon State University. Under his guidance, two undergraduate students at Oregon State University, Jonah Bosland and Stefan Ene, developed the majority of the initial implementation during the summer 2023. Peter Baumgartner helped us with the testing and analysis on NUMA platforms.

Additional contributions by (new authors, add yourself here):
* Matt Ruff

# Copyright

Copyright for SCARR (2023-2024) by Vincent Immler.

# Citation

If you use SCARR in your research, please cite our paper:
"High-Performance Design Patterns and File Formats for Side-Channel Analysis" by Jonah Bosland, Stefan Ene, Peter Baumgartner, Vincent Immler.
IACR Transactions on Cryptographic Hardware and Embedded Systems, 2024(2), 769–794.

DOI: [click here](https://doi.org/10.46586/tches.v2024.i2.769-794)

# Acknowledgements

Jonah Bosland has been funded through the Office of Naval Research (ONR) Workforce Development grant (N000142112552) during June-November 2023. Stefan Ene has been
funded through the Summer 2023 Research Experience for Undergraduates (REU) program by the School of EECS at Oregon State University.
