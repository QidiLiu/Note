#running 

Prerequisite:
- Windows 11
- Visual Studio 2022
- CMake 3.31.3
- Internet outside the Chinese mainland

Reference: 
- https://github.com/MIC-DKFZ/nnUNet/blob/master/documentation/installation_instructions.md
- https://github.com/MIC-DKFZ/nnUNet/blob/master/documentation/setting_up_paths.md
- https://github.com/MIC-DKFZ/nnUNet/blob/master/documentation/dataset_format.md
- https://blog.csdn.net/qq_40035462/category_12633990.html

## Set up nnUNet v2 step by step

Create a conda environment and install pytorch in it, then clone the repo to `<somewhere>/nnUNet` and install nnUNet.
```bash
cd <somewhere>
git clone https://github.com/MIC-DKFZ/nnUNet/blob/master/documentation/installation_instructions.md
cd nnUNet
conda create -n nnunet python=3.10
conda activate nnunet
conda install pytorch torchvision torchaudio pytorch-cuda=12.4 -c pytorch -c nvidia
pip install -e .
```

## Prepare the dataset in nnUNet format

Create the following 3 folder for nnUNet processing.
```bash
mkdir data && cd data
mkdir nnUNet_raw
mkdir nnUNet_preprocessed
mkdir nnUNet_results
```

Organize datasets in folder `nnUNet_raw`. Name the dataset folders as `DatasetXXX_<Dataset name>`, in which `XXX` is a number between 000 ~ 999 (some one recommanded that the number should be more than 200 to avoid some weird bugs).
```
nnUNet_raw/
├── Dataset001_BrainTumour
├── Dataset002_Heart
├── Dataset003_Liver
├── Dataset004_Hippocampus
├── Dataset005_Prostate
├── ...
```

The following example convert a dataset from [LabelMe JSON](https://roboflow.com/formats/labelme-json) to a format compatible with nnUNetv2. LabelMe provided [a tool to export VOC-format dataset for semantic segmentation](https://github.com/wkentaro/labelme/tree/main/examples/semantic_segmentation).

```
nnUNet_raw/Dataset001_NAME1
├── dataset.json
├── imagesTr
│   ├── ...
├── imagesTs
│   ├── ...
└── labelsTr
    ├── ...
nnUNet_raw/Dataset002_NAME2
├── dataset.json
├── imagesTr
│   ├── ...
├── imagesTs
│   ├── ...
└── labelsTr
    ├── ...
```

