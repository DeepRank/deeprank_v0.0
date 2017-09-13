# DeepRank Machinery Version 0.0

These files allows to :

   * assemble data from different sources (PDB,PSSM,Scores,....) in a comprehensible data base where each conformation has its own folder. In each folder are stored the conformation, features and targets.

   * Map several features to a grid. The type of features as well as the grid parameters can be freely chosen. New features can be mapped without havng to recompute old ones.

   * Use a 3d or 2d CNN to predict possible targets (binary class, haddock-score ...) from the data set

## Pre-requisite

Several packages are required to run the code. Most are pretty standard.

  * [Numpy](http://www.numpy.org)

  * [Scipy](https://www.scipy.org/)

  * [PyTorch](http://pytorch.org)

The deep learning was implemented with PyTorch 2. (pytorch.org)
To install pytorch with anaconda 

```
conda install pytorch torchvision cuda80 -c soumith
```

  * [tensorboard](https://github.com/lanpa/tensorboard-pytorch)

To install pytorch-tensorboard with pip

```
pip install tensorboard-pytorch
```

This package depends on tensorflow-tensorboard. If you don't have tensorflow installed you can get it with

```
pip install tensorflow-tensorboard
```

## Overview 

The (manual) workflow contains three main stages 

1 Assemble the dataset from a collection of sources

2 Map the features of each conformation of the dataset on a grid

3 Use DeepLearning to teach the CNN how to predict a pre-defined target

The code for each stage are contained in the own folder : **_assemble/ map/ learn/_**

### Download the data set

The data set BM4 is located on alcazar at 

```
BM4=/home/deep/HADDOCK-decoys/BM4_dimers
```

All the files needed in the following are there, i.e. 
decoys pdb : $BM4/decoys_pdbFLs
native pdb : $BM4/BM4_dimers_bound/pdbFLs_ori (or refined ...)
features   : $BM4/PSSM (only PSSM so far)
targets    : $BM4/model_qualities/XXX/water   (XXX=haddockscore, i-rmsd, Fnat, ....)
classID    : $BM4/training_set_IDS/classIDs.lst

We can add later on more features, and more targets.
The classIDs.lst contains the IDs of 228 complexes (114 natives 114 decoys) preselected for training. The decoys were selected for theur very low i-rmsd, i.e. they are very bad decoys.

Dowload the $BM4 folder (maybe zip it before as it is pretty big !)

### Assemble the data set

The file assemble/assemble_data.py allows to collect data and to create a clean data base. The data can contain natives pdbs, decoy pdbs, different features, different targets. In the output directory, each conformation will have its own subfolder containing its pdb, features files and target data.

To test the routine, download the BM4 data file from the cluster and decompress it
In assemble_data.py change the path to the BM4 folder on your computer.

```
python assemble_data.py
```

should create a new folder training_set containg all the info

### Map the features to the grid

The files used for mapping the features are in the map/ folder. The gridtool.py is the contains the main function computing the feature on the grid. You can test the routine on a single conformation with

```
python gridtool.py
````

This will compute the feature of the complex contained in the ./test/ subfolder. Once done go to the subfolder. Cube files can be generated using

```
python generate_cube_files.py ./test/

```

Once the cuve generated it easy to visualzie them with VMD and a few files automatically generated by the generate_cube_files.py.

```
cd ./test/
cp 1CLV_1w.pdb data_viz/complex.pdb
cd ./data_viz/
vmd -e AtomicDensities.vmd
```

To map the features of all the complexes contained in the data set, change the folder name in gendatatool.py to the training_set/ folder on your machine. Then type

```
python gendatatool.py
```

This will loop over all the folder and map all the features. The cuve files are not generated automatically as it will take to much time. The generate_cube_files.py is however automatically copied to the training_set/ folder. You can use it to visualize the data of a particular complex as before.

```
python generate_cube_files.py molfolder
cp moldfolder/viz_data
vmd -e AtomicDensities.vmd
```

### Deep Learning

The main file for the deeplearning phase is DeepRankConvNet.py. A lot of options are available here. You can in particular to use a 2d or 3d conv net and to do either a regression or a classification. The header of the file explains all the options. To start the learning loop simply type 

```
python DeepRankConvNet.py
````

