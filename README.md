# Quick start guide for running CESM 1.2.2 on Burgundy

## 1)	Deciding your working directory
First, you need to decide on the working directory. You are suggested to install and run CESM in the directory /scratch/Course/see6212/**_username_**, where **_username_** is your assigned username on Burgundy.

Enter the following commands to set the location to an environment variable and move to the directory:
(Remember to replace **_username_** with your own username)
```
export CESMHOME=/scratch/Courses/see6212/username
cd $CESMHOME
mkdir cesm_case
```
In the last line, we create an empty directory for holding new simulation case, which will be used in the latter step.


## 2)	Copying model files
Since users are not allowed to download files to Burgundy directly from the CESM server, we prepared the required files of CESM. You can copy it to your working directory by entering the following command:

```
cp -r /scratch/Courses/see6212/see6212_common/cesm1_2_2/ $CESMHOME
```
The total file size of this directory is about 577 MB, so it will not take long for copying.


## 3)	Preparing input data for simulation
Initial condition is required to solve differential equations, so we need to provide the CESM some input data to start a simulation. Again, downloading of data from CESM server is prohibited on the cluster, so we can make a copy of the prepared data. However, the size of the input data is huge (1.9 GB). Instead of making duplicate, we can use the data in a pre-defined location, and extra step is not needed here.


## 4)	Creating a new simulation case
To create a case, we need to specify the component set and resolution of the components. In this practical session, we choose the component set “FRM” and resolution “f45_g37”.
Enter the following commands:
```
cd cesm1_2_2/script
./create_newcase -case $CESMHOME/cesm_case/FRM_f45_g37 -compset FRM -res f45_g37 -mach burgundy
```
A new directory specified in the option “-case” is created, and necessary files are copied to this directory automatically. You can choose any name for this directory. You should see “Successfully created the case for burgundy” as the last line (and something similar to the following) if the creation of new case is successful.

![Picture1](https://user-images.githubusercontent.com/107025929/227130130-31abf9cb-d305-4369-86b0-9639899e4293.png)


## 5)	Setting up the case
At this stage, we can make some changes to model setting (e.g. path for input/output) if desired. For convenient, we will not make further changes for this part in this practical session. The simulation is set to run for 100 years, with one month in each time step.

Start the setup by the following commands:
```
cd ../../cesm_case/FRM_f45_g37
./cesm_setup
```

If the setup is successful, you should be able to see the following:
![Picture2](https://user-images.githubusercontent.com/107025929/227130782-b58e6a78-3577-41de-b22b-7d745b2657f7.png)


## 6)	Building the model
We then proceed to creating the executable of the model. For simplicity, no further modification is required.
Enter the following command:
```
./FRM_f45_g37.build
```
It should take about 10–12 minutes to finish building the executables. If the model is successfully built, you can see “CESM BUILDEXE SCRIPT HAS FINISHED SUCCESSFULLY” as the last line (and the following):
![Picture3](https://user-images.githubusercontent.com/107025929/227130927-53b93112-1a5e-4225-a041-28e7cc0270a3.png)


## 7)	Submitting the job
The final step is to run the model. However, running a complex model like CESM is computationally demanding, so it needs a lot of resources (e.g. CPU). Burgundy uses a software called Slurm for job scheduling. It means that the allocation of access to computing resources on the cluster is arranged by Slurm.
Enter the following command:
```
./FRM_f45_g37.submit
```
A job which run the CESM model is submitted to the queue. The job will start when the requested resources become available. You can check the status of the job by entering the following command (replace **_username_** with your own username):
```
squeue -u username
```

The following lines are prompted:
![Picture4](https://user-images.githubusercontent.com/107025929/227131164-7da54bb9-e079-4971-a000-bf1a018b3822.png)
"ST" (first line) denotes status, and "PD" (just below "ST") denotes pending.

If the status changes to "R" from "PD", the job (simulation) is running.
![Picture5](https://user-images.githubusercontent.com/107025929/227131374-bd3afe4a-6d8e-4d81-aaf3-7d37387e6c3b.png)

After the job is completed, you need to check whether the simulation is finished successfully or not. There is a newly created text file named “FRM_f45_g37.run.o******”, where ****** is the jobID.
In the above figure, the jobID is 376003.

Show the content of this file on screen by:
```
cat FRM_f45_g37.run.o******
```

For a successful run, the last line in this file should contain “SUCCESSFUL TERMINATION OF CPL7-CCSM” and the following:
![Picture6](https://user-images.githubusercontent.com/107025929/227132393-294614cf-a45c-4178-96fd-f055ebe43b7d.PNG)

The output data can be found under the directory “run”. The file names are in the format FRM_f45_g37.*ccc*.h0.*yyyy*-*mm*.nc (or FRM_f45_g37.*ccc*.h.*yyyy*-*mm*.nc). *ccc* is component name (cam/cice/clm2/rtm), *yyyy* is year, and *mm* is month.

