# HPC-Optimized VP-Trees & Parallel KNN Search

## Description
The project benchmarks four distinct architectural approaches:

* **Sequential (Baseline)**: A native C implementation using the Quickselect algorithm for median calculation and Euclidean distance for metric partitioning.
* **Simple OpenMP**: Introduces shared-memory parallelism by offloading distance calculations and recursive subtree construction to multiple threads.
* **Optimized OpenMP (Threshold-Limited)**: A sophisticated version that prevents thread explosion. It switches to sequential execution when the workload size ($N \times D$) falls below a specific threshold and caps parallel recursion levels to ensure optimal CPU utilization.
* **Hybrid MPI + OpenMP**: A distributed-memory extension where the workload is split between compute nodes. The top-level partitioning is handled via MPI, while lower-level construction utilizes optimized multi-threading.

The repository includes a highly efficient KNN search algorithm that traverses the VP-Tree using a **Priority Queue**. It minimizes distance calculations by pruning branches that fall outside the search radius, and it is further parallelized via a **Dynamic Scheduler** to maintain load balancing across points.

## How to build and run
In order to build folow the instructions
1. Inside the root of the repo create and go to a build folder: ``mkdir -p build && cd build``
2. Configure the project ``cmake ..``
3. Compile the project ``make``
4. Run: <br>
i. Sequential, Simple OpenMP, Limited OpenMP: ``./main_program <data> <mode> <limit of threads>`` <br>
ii. MPI with OpenMP version ``mpirun -np 2 main_program <data> 3 <limit of threads>``,
<br> where ``<data>`` is the path the the dataset of points, ``<mode>`` is the 0 for sequential, 1: for simple OpenMP, 2: for optimized OpenMP/Sequential and ``<limit of threads>`` is used in 2 and 3 mode and its actually a limit to prallel levels.
5. (Optional) You can configure the project with Google Tests running ``cmake -DBUILD_TESTING=ON ..`` , then ``make`` to compile and ``make test`` to run unit tests on basic utilites

Alternativle to build and run some tests you can see the ``demo_script.sh`` inside the scripts folder. If you also have download the sample scripts inside the data folder,as mentioned below (after unzip you should move all the .dat inside the data folder), you can also execute the demo script.

## Datasets
This project needs some nDimnensional points as input. The points should be stored in binary file, with points stacked sequential in float32 format. The first 2 floats are the number and the dimension of points. You can create several datasets like this format with scripts ``create_data.py`` and ``datasets.py`` in scripts folder. This scripts basicly create these points randomly using different  distributions like normal, uniform, expodential or using known datasets like MNIST. 

Alternativly you can download some samples from the README.md inside the data folder.

## Testing
An important aspect of this project were testing. We implement the VP creation in python following similar architecture like Matlab script provided by professor of the subject (only difference we pick the first and not the last element ad vp point). We also use skelarn's implementation of KNN and so we can compare the preorder form of C and the KNN elements with pythons. All the parallel implemenations are compared with the simple sequential. 

As mentioned for the basic utilities there are Google Tests and the utilities were written in Test Driven philosophy. Ofcourse they are not proof of correctness but they helped us develop easier and faster the whole project.

## Instructions for code reviewers
For easing and deep understanding of this project it is recommended to follow the steps with that order:
1. first read the paper
2. read the README.md and follow build instructions
3. start code reviewing with main program, the base of all project.
4. Check the correctness of each implemntation by opening both the .c and .h. In header there are comments in fomral format and you can read each function's description in VS code by puting the cursor upon the function.
5. To run the project, follow the How to build & run section.
