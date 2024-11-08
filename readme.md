# On Efficient Large Sparse Matrix Chain Multiplication
More details about our work can be found in our publication:

Lin, C., Luo, W., Fang, Y., Ma, C., Liu, X., & Ma, Y. (2024). On Efficient Large Sparse Matrix Chain Multiplication. Proceedings of the ACM on Management of Data, 2(3), 1-27.

-- We kindly ask that any published research that makes use of our work cites the paper above.

## Environment
* All the algorithms in this paper are implemented in C++ and compiled with the gcc 9.4.0 compiler using the -O3 optimization level.

* The experiments are run on a Linux machine running Ubuntu Linux 20.04.5 LTS. The machine is equipped with dual Intel Xeon(R) Gold 6338 2.0GHz processors (64 cores) and 496GB of RAM.

* The version of mpirun (Open MPI) is 4.0.3. You can refer to https://www.open-mpi.org/software/ompi/v4.0/ to install OpenMP.

* The version of Intel® oneAPI Math Kernel Library (MKL) is 2024.0, which can be obtained from https://www.intel.com/content/www/us/en/developer/tools/oneapi/base-toolkit-documentation.html. For installation steps, please see https://www.intel.com/content/www/us/en/docs/onemkl/developer-reference-dpcpp/2024-0/overview.html.

* The version of python is 3.12.1 and matplotlib and numpy also need to be installed.


## Execution
All output figures are drawn in the **./materials/output/fig** folder. 

You need to download datasets from Google drive (https://drive.google.com/drive/folders/1ugn9PY20Xb_W-N9vr44ueZQl_gluyzsR?usp=drive_link) and put **edge.txt, vertex.txt, and graph.txt** in the corresponding folder in **./materials/input/graph**.

The recommended execution order of scripts in **./script**: 
+ **run_all.sh**
+ **draw_all.sh**
+ **run_eff_mkl.sh**
+ **draw_mkl.sh**
### 1. Clean
* When excuting **./script/run_all.sh**, **./script/clean_all.sh** and **./script/clean_metapath.sh** will be triggered first.
  * **./script/clean_all.sh**: clean up files generated by **run_all.sh** and **run_eff_mkl.sh**, except for those in **./materials/output/fig**.
  * **./script/clean_fig.sh**: clean up files in **./materials/output/fig**.
 <!-- and **./script/run_eff_mkl.sh** is not triggered. You need to excute **./script/run_eff_mkl.sh** separately. -->

### 2. Efficiency and accuracy evaluation (run_all.sh)
If you want to get the efficiency of each sparse matrix chain multiplication (SMCM) and the accuracy of all sparsity in the experiments in this paper, you can run **run_all.sh** in **./script**:
```
bash ./run_all.sh
```
After finishing **run_all.sh**, you can run **draw_all.sh** to draw **Figures 8-12**, and **Figure 14** with DBLP, IMDB, and FreeBase datasets in **./output/fig** directly.
```
bash ./draw_all.sh
```

 ### 3. Efficiency of MKL evaluation (run_eff_mkl.sh)
You need to install MKL first if you want to run the experiment in  **Figure 17**. Note that after installing Open MPI, you need to execute the following command:
```
. ${KML_PATH}/env/vars.sh
```
before you run the script **run_eff_mkl.sh** in **./script**. ${KML_PATH} is the path to your installed MKL. For example, in my environment, **\$\{KML\_PATH\}=/home/chunxu/intel/oneapi/mkl/latest**:
```
. /home/chunxu/intel/oneapi/mkl/latest/env/vars.sh
```

And then,  you can execute **run_eff_mkl.sh** in **./script** with the following command:
```
bash ./run_eff_mkl.sh -mkl_root=${KML_PATH}
```
For example, in my environment, this command could be 
```
bash ./run_eff_mkl.sh -mkl_root=/home/chunxu/intel/oneapi/mkl/latest
```

After finishing **run_all.sh** and **run_eff_mkl.sh**, you can run **draw_mkl.sh** to draw **Figure 17** in **./output/fig** directly.
```
bash ./draw_all.sh
```
Please note that if you want to draw **Figure 17** by **draw_mkl.sh**, you must first execute **run_all.sh** and **run_eff_mkl.sh** in sequence.

### 4. Others
Input parameters are explained in the table below:
| Parameter | Description |
| --- | --- |
| **-mkl_root:** | the path to your installed MKL.|

All statistics are written in the **./materials/output/** folder
In the **./materials/output/efficient** folder, all the statistics are from the code in **accuracy**, the 5 columns of the file in this folder are explained in the table below:
| Column | Description |
| --- | --- |
| **1-st** | Meta-path.|
| **2-nd** | Number of non-zero elements in the result matrix.|
| **3-rd** | Time cost of the algorithm.|
| **4-th** | Time cost of the dynamic process in the algorithm.|
| **5-th** | Time cost of the estimation in the algorithm.|
Note that in the file, whose name invloves **mkl**, it only contains the first 3 columns.
In the **./materials/output/efficient** folder, all the statistics is from the code in **efficient**

In the **./materials/output/accuracy** folder, all the statistics are from the code in **accuracy**, the 2 columns of the file in this folder are explained in the table below:
| Column | Description |
| --- | --- |
| **1-st** | Meta-path.|
| **2-nd** | Number of **estiamted** non-zero elements in the result matrix.|


## The codes of Estimator Accuracy Evaluation
* **\accuracy\res.cpp:** the row-wise sparsity estimator which is proposed by us. 
* **\accuracy\meta.cpp:** a statistically unbiased matrix sparsity estimation algorithm, MetaAC, which is very efficient.
* **\accuracy\mnc.cpp:** the state-of-the-art matrix sparsity estimation algorithm, MNC;
* **\accuracy\dm.cpp:** a statistically unbiased sparsity estimator with block cells;
* **\accuracy\lg.cpp:** a graph-based sparsity estimator (with r=32).

## The codes of Efficiency Evaluation
* **\efficient\resmcm.cpp:** our proposed parallel SMCM algorithm.
* **\efficient\rescsr.cpp:** same as RESMCM, except for using CSR data structure.
* **\efficient\mkl_csr.cpp:** same estimator RESMCM, but using the sparse matrix multiplication algorithms from MKL with the CSR data structure.
* **\efficient\mkl_csc.cpp:** same estimator RESMCM, but using the sparse matrix multiplication algorithms from MKL with the CSC data structure.
* **\efficient\mkl_bsr.cpp:** same estimator RESMCM, but using the sparse matrix multiplication algorithms from MKL with the BSR data structure.
* **\efficient\l2r.cpp:** same as RESMCM except for using the left-to-right chain order.
* **\efficient\naive.cpp:** same as RESMCM except for regarding all the input matrices as dense matrices, and then using the classic dynamic programming to determine an optimal orde.
* **\efficient\meta.cpp:** same as RESMCM except for using MetaAC estimator.
* **\efficient\mnc.cpp:** same as RESMCM except for using MNC estimator.
* **\efficient\dm.cpp:** same as RESMCM except for using DMap estimator.
* **\efficient\lg.cpp:** same as RESMCM except for using LGraph estimator.

## Others
* The five datasets used in the paper are available from the following sources:

        DBLP: http://dblp.uni-trier.de/xml/
        DBpedia: https://wiki.dbpedia.org/Datasets
        FourSquare: https://sites.google.com/site/yangdingqi/home/foursquare-dataset
        FreeBase: http://freebase-easy.cs.uni-freiburg.de/dump/
        IMDB: https://www.imdb.com/interfaces/

* We uploaded the DBLP, IMDB, and FreeBase datasets to Google Drive:  https://drive.google.com/drive/folders/1ugn9PY20Xb_W-N9vr44ueZQl_gluyzsR?usp=drive_link.
* There are some slight differences from the experiment shown in the paper. The meta-path here are all possible meta-paths, so the figure will be slightly different from that shown in the paper.

