# STEMDL-Benchmark

## Software Requirements:

### Classification Task

- Python >= 3.6
- PyTorch >= 1.2 
- Horovod >= 0.19 
- opencv-python 
- h5py 

### Reconstruction Task 

- Python >= 3.6
- TensorFlow == 1.14
- Horovod >= 0.16

## Data  

Download the classificatoin dataset from [10.13139/OLCF/1510313](https://doi.ccs.ornl.gov/ui/doi/70) via Globus. 
If you have access to OLCF machines, the preprocessed data are available at 
```bash
classification: /gpfs/alpine/world-shared/gen011/sajal/dc20/spacegroup/mxdata
reconstruction: /gpfs/alpine/world-shared/stf011/junqi/stemdl/data
```

## Quickstart

- Run the space-group classification: 

__classification/source/run.sh__ is the working job script on Summit, and the training is launched via   

```bash
jsrun -n<NODES> -a1 -g1 -c42 -r1 -b none python sgcl_runner.py --epochs 10 
                                                         --batch-size 32 
                                                         --train-dir <path-to-train-dataset> 
                                                         --val-dir <path-to-val_dataset>  
```

- Run the potential reconstruction: 

__reconstruction/job.lsf__ is the working job script on Summit, and the training is launched via   

```bash
CMD="python -u stemdl_run.py --hvd_fp16 
                             --hvd_group 1 
                             --nvme 
                             --filetype "lmdb" 
                             --data_dir <path-to-train-data>  
                             --fp16  
                             --mode "train" 
                             --batch_size 1  
                             --network_config "json_files/network_FCDenseNet_pool_avg.json""  
                             --ilr 1.e-6 
                             --bn_decay 0.1 
                             --scaling 1.0 
                             --input_flags "json_files/input_flags.json" 
                             --hyper_params "json_files/hyper_params.json" "

jsrun -n<NODES> -a 6 -c 42 -g 6 -r 1 --bind=proportional-packed:7 --launch_distribution=packed stdbuf -o0 utils/launch.sh "${CMD}"
```

