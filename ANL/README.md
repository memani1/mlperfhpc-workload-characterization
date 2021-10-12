# Instructions to obtain workload characteristics on ThetaGPU system at ANL.

(1) Memory bandwidth:

We used Nvidia Nsight Tools to capture the related metrics 

``` ncu --metrics dram__bytes.sum.per_second  --kernel-id ::regex:'^(?!Eigen)':4 python3 train.py <args> ```

(2) Network bandwidth:

This was measured using Horovod Timeline

https://horovod.readthedocs.io/en/stable/timeline_include.html

``` mpirun -x HOROVOD_TIMELINE=/path/to/log/file python train.py ``` 

Execute the application with HOROVOD_TIMELINE enviroment variable
  ``` export HOROVOD_TIMELINE=<output horovod timeline json path> ```

Analyze horovod timeline json file by using python script
  ``` python3 analyze_hvd.py <horovod timeline json path> ```

(3) I/O bandwidth:

Darshan tool was used to measure the peak I/O bandwidth (https://www.mcs.anl.gov/research/projects/darshan/). 

After installation, 

``` export DARSHAN_PRELOAD=<path-to-darshan-installation>/lib/libdarshan.so ``` 
 
``` mpirun -x LD_PRELOAD=$DARSHAN_PRELOAD  -np 8 python3 train.py <args> ```
( -x for OpenMPI, --env for MPICH-based implementations)

This step will generate log files (.darshan) format. Then use the following step to analyze the data

``` darshan-parser --perf <path-to-darshan-logs>/.darshan.gz ```
