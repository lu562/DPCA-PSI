
## How to use DPCA-PSI

### Requirements

- Linux
- CMake (>=3.15)
- GNU G++ (>=5.5) or Clang++ (>= 5.0)
- Python 3

### Building DPCA-PSI

DPCA-PSI depends on [Intel Paillier Cryptosystem Library (IPCL)](https://github.com/intel/pailliercryptolib).
We choose IPCL because it is certified for ISO compliance.
Unfortunately, IPCL introduces a number of [issues](#issues-with-intel-pailllier-cryptosystem-library).
Here we provide the following scripts to help users install IPCL (without `sudo`).

First, install NASM with `apt install nasm`, or build and install NASM from source code downloaded from [NASM's official page](https://www.nasm.us/). Assume that you are working in the root directory of NASM source code.
```shell
./configure
make -j
make install
```

Second, build and install IPCL using the following scripts.
Assume that IPCL is cloned into the directory `${IPCL}` and will be installed to the directory `${IPCL_INSTALL_DIR}`.
```shell
cmake -B ${IPCL}/build -S ${IPCL} -DCMAKE_INSTALL_PREFIX=${IPCL_INSTALL_DIR} -DCMAKE_BUILD_TYPE=Release -DIPCL_TEST=OFF -DIPCL_BENCHMARK=OFF
cmake --build ${IPCL}/build -j
cmake --build ${IPCL}/build --target install
```

Third, build [JSON for Modern C++ (JSON)](https://github.com/nlohmann/json) using the following scripts.
Assume that JSON is cloned into the directory `${JSON}`.
```shell
cmake -B ${JSON}/build -S ${JSON}
cmake --build ${JSON}/build -j
```

At last, build DPCA-PSI using the following scripts.
Assume that DPCA-PSI is cloned into the directory `${DPCA-PSI}`.
```shell
cmake -B ${DPCA-PSI}/build -S ${DPCA-PSI} -DCMAKE_BUILD_TYPE=Release -DIPCL_DIR=${IPCL_INSTALL_DIR}/lib/cmake/ipcl-2.0.0  -Dnlohmann_json_DIR=${JSON}/build
cmake --build ${DPCA-PSI}/build -j
```

Output binaries can be found in `${DPCA-PSI}/build/lib/` and `${DPCA-PSI}/build/bin/` directories.

| Compile Options          | Values       | Default | Description                         |
|--------------------------|--------------|---------|-------------------------------------|
|`CMAKE_BUILD_TYPE`        | Release/Debug| Release | The build type.                     |
| `DPCA_PSI_BUILD_SHARED_LIBS` | ON/OFF        | OFF     | Build a shared library if set to ON.          |
| `DPCA_PSI_BUILD_EXAMPLE`        | ON/OFF        | ON      | Build C++ example if set to ON.                 |
| `DPCA_PSI_BUILD_TEST`                | ON/OFF        | ON      | Build C++ test if set to ON.                        |
| `DPCA_PSI_BUILD_DEPS`               | ON/OFF        | ON      | Download and build unmet dependencies if set to ON. |

DPCA-PSI further depends on [OpenSSL](https://github.com/openssl/openssl), [gflags](https://github.com/gflags/gflags), [Google Logging](https://github.com/google/glog), and [Google Test](https://github.com/google/googletest).
The build system will try to find these dependencies if they exist or will otherwise automatically download and build them.

### Running the DPCA-PSI protocol

Here we give a simple example to run our protocol. Please refer to [Example running DPCA-PSI](example/README.md) for more details.

To run Party A
```shell
cd ${DPCA-PSI}/build/example/scripts
bash sender_test.sh
```

To run Party B
```shell
cd ${DPCA-PSI}/build/example/scripts
bash receiver_test.sh
```

### Issues with Intel Pailllier Cryptosystem Library

- A user has to install [Netwide Assembler (NASM)](https://www.nasm.us/) and export the environment variable `nasm`. [(issue #70)](https://github.com/intel/pailliercryptolib/issues/70).
- IPCL does not support GNU G++ 11.0 and above. [(issue #27)](https://github.com/intel/pailliercryptolib/issues/27#issuecomment-1320753239).
- `IPCLConfig.cmake` resides in a wrong directory without installing IPCL. [(issue #69)](https://github.com/intel/pailliercryptolib/issues/69).

## Reference
[1] Meadows, Catherine. "A more efficient cryptographic matchmaking protocol for use in the absence of a continuously available third party." 1986 IEEE Symposium on Security and Privacy. IEEE, 1986.

[2] Ion, Mihaela, et al. "On deploying secure computing: Private intersection-sum-with-cardinality." 2020 IEEE European Symposium on Security and Privacy (EuroS&P). IEEE, 2020.

[3] Buddhavarapu, Prasad, et al. "Private matching for compute." Cryptology ePrint Archive (2020)

[4] Buddhavarapu, Prasad, et al. "Multi-key private matching for compute." Cryptology ePrint Archive (2021).

[5] Guo, Xiaojie, et al. "Birds of a Feather Flock Together: How Set Bias Helps to Deanonymize You via Revealed Intersection Sizes." 31st USENIX Security Symposium (USENIX Security 22). 2022.

[6] https://github.com/nshomron/covidpred

[7] Powar, Jovan, and Alastair R. Beresford. "SoK: Managing risks of linkage attacks on data privacy." Proceedings on Privacy Enhancing Technologies 2 (2023): 97-116.
