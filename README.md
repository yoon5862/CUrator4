
# CUrator
  CUrator is an efficient LLM execution engine with optimized integration of CUDA libraries(cuBLAS, and CUTLASS).

# Prerequisite Packages and Supported Models

## **Prerequisite Packages**
- LLVM v10.0.0
- CUDA v11.4.4 (V100, RTX3090) or v12.0.0 (A6000, RTX4090, A100)
- CUDNN v8.9.7 (V100, RTX3090) or v8.9.5 (A6000, RTX4090, A100)
- gcc v11.4.0 and above
- CMake v3.18 and above
- Anaconda

__all the prerequiest packages need to be installed in a default directory.__


__cuDNN library must be included in the CUDA library.__

# **Setup Curator**

**Step 1**: Clone the Curator repository in github:
```bash
git clone https://github.com/yoon5862/CUrator.git curator
```

**Step 2**: Install Conda virtual environment, users can fllow command in your terminal or comment prompt:
```bash
cd curator
conda env create -f conda.yml --name curator
conda activate curator
pip install torch==2.1.2 torchvision==0.16.2 torchaudio==2.1.2 --index-url https://download.pytorch.org/whl/cu118
```

**Step 3**: Config Curator before build:
```bash
cd ./tvm
mkdir build
cp ./cmake/config.cmake ./build
```
and modify **./build/config.cmake** file
```bash
set(USE_LLVM <path/to/llvm/llvm-config>)
set(USE_CUDA </path/to/cuda>) # examples /usr/lib/cuda-12.0
set(USE_CUTLASS ON) # OFF->ON
set(USE_CUBLAS ON) # OFF->ON
set(USE_CUDNN ON) # OFF
```

**Step 4**: Build Curator:
```bash
cd ./build
cmake ..
make -j $(nproc)
```
When the build is successful, please set the PYTHONPATH on .bashrc
```bash
export PYTHONPATH=path/to/curator/tvm/python:$PYTHONPATH
```

## **Evaluated HuggingFace Models**
* BERT
  * gaunernst/bert-tiny-uncased
  * gaunernst/bert-mini-uncased
  * gaunernst/bert-small-uncased
  * gaunernst/bert-medium-uncased
  * bert-base-uncased
  * bert-large-uncased
* GPT2
  * gpt2
  * gpt2-medium
* Llama
  * openlm-research/open_llama_3b
  * meta-llama/Meta-Llama-3-8B-Instruct

## **Evaluated GPU Architecture**
  - 70: Tesla V100-DGXS-32GB
  - 80: NVIDIA A100-SXM4-80GB
  - 86: NVIDIA GeForce RTX 3090, NVIDIA RTX A6000
  - 89: NVIDIA GeForce RTX 4090

# **Convert HuggingFace Model to ONNX model**

CUrator need to convert the HuggingFace models to ONNX models:
or run shell script to create evaluated models
```bash
cd <path/to/curator>/script
./create_model.sh > create_model.log
```

# **Inference LLM with CUrator**
Inference end-to-end Evaluated LLM TTFT and measure time running shell script
```bash
cd <path/to/curator>/script

# Single Precision Evaluation
./profile_single.sh 89 ./cutlass_rtx4090 > profile_single.log
./curator_single.sh 89 ./cutlass_rtx4090 > curator_single.log

# Half Precision Evaluation
./profile_half.sh 89 ./cutlass_rtx4090 > profile_half.log
./curator_half.sh 89 ./cutlass_rtx4090 > curator_half.log
```