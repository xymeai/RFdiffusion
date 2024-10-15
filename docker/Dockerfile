# Usage: 
# git clone https://github.com/RosettaCommons/RFdiffusion.git
# cd RFdiffusion
# docker build -f docker/Dockerfile -t rfdiffusion .
# mkdir $HOME/inputs $HOME/outputs $HOME/models
# bash scripts/download_models.sh $HOME/models
# wget -P $HOME/inputs https://files.rcsb.org/view/5TPN.pdb

# docker run -it --rm --gpus all \
#   -v $HOME/models:$HOME/models \
#   -v $HOME/inputs:$HOME/inputs \
#   -v $HOME/outputs:$HOME/outputs \
#   rfdiffusion \
#   inference.output_prefix=$HOME/outputs/motifscaffolding \
#   inference.model_directory_path=$HOME/models \
#   inference.input_pdb=$HOME/inputs/5TPN.pdb \
#   inference.num_designs=3 \
#   'contigmap.contigs=[10-40/A163-181/10-40]'

FROM nvidia/cuda:11.8.0-runtime-ubuntu22.04

COPY . /app/RFdiffusion/

RUN apt-get -q update \
  && DEBIAN_FRONTEND=noninteractive apt-get install --no-install-recommends -y \
  libgomp1 \
  git \
  python3.10 \
  python3-pip \
  && python3.10 -m pip install -q -U --no-cache-dir pip \
  && rm -rf /var/lib/apt/lists/* \
  && apt-get autoremove -y \
  && apt-get clean \
  && pip install  \
    -q --no-cache-dir \
    -f https://data.dgl.ai/wheels/torch-2.1/cu118/repo.html \
    torch==2.1 \
    dgl \
    e3nn==0.3.3 \
    wandb==0.12.0 \
    pynvml==11.0.0 \
    git+https://github.com/NVIDIA/dllogger#egg=dllogger \
    decorator==5.1.0 \
    hydra-core==1.3.2 \
    pyrsistent==0.19.3 \
    pandas \
    pydantic \
    numpy==1.26.4 \
    /app/RFdiffusion/env/SE3Transformer \
  && pip install --no-cache-dir /app/RFdiffusion --no-deps
  
WORKDIR /app/RFdiffusion

ENV DGLBACKEND="pytorch"

ENTRYPOINT ["python3.10", "scripts/run_inference.py"]
