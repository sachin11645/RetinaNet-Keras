# Retinanet-Keras
Implementation of Keras-Retinanet in Poultry Behavior Dataset
In Linux Interface

# Getting Tools
pip install --user --upgrade tensorflow-gpu
pip install keras
sudo pip install keras
mkdir ~/RetinanetTutorial
git clone https://github.com/jaspereb/Retinanet-Tutorial.git
git clone https://github.com/fizyr/keras-retinanet.git

# Creating Dataset
mkdir ~/RetinanetTutorial/Data (Put all images in this dataset folder)
cd ~/RetinanetTutorial/Data
mkdir -p ~/RetinanetTutorial/PlumsVOC/JPEGImages
mkdir ~/RetinanetTutorial/PlumsVOC/Annotations
mkdir -p ~/RetinanetTutorial/PlumsVOC/ImageSets/Main
cp ~/RetinanetTutorial/Data/* ~/RetinanetTutorial/PlumsVOC/JPEGImages

# Labelling
Using Makesence.AI to label the images
Renumberring the images
Editing the xml files to replace "Unspecified" with "0"

# Making Imagesets
cd ~/RetinanetTutorial/PlumsVOC/ImageSets/Main
ls ../../JPEGImages/ > trainval.txt
sed -i 's/.jpg//g' trainval.txt
touch val.txt
cp trainval.txt train.txt (Split and edit train and val text files with 60:40 ratio)

#  Training
mkdir -p ~/RetinanetTutorial/TrainingOutput/snapshots
cd ~/RetinanetTutorial/keras-retinanet/
edit pascal_voc.py- voc classes
voc_classes = {
    'Feeding'   : 0,
    'Drinking'     : 1,
    'Perching'        : 2,
    'Pecking'        : 3,
    'Dust Bathing'      : 4,
    'Preening'         : 5,
    'Foraging'         : 6
}

cd ~/RetinanetTutorial/keras-retinanet/
pip install numpy --user
pip install . --user
python setup.py build_ext --inplace
keras_retinanet/bin/train.py --tensorboard-dir ~/RetinanetTutorial/TrainingOutput --snapshot-path ~/RetinanetTutorial/TrainingOutput/snapshots --random-transform --steps 100 pascal ~/RetinanetTutorial/PlumsVOC (Edit steps per pascal and epochs if necessary)

# Deploying
mkdir ~/RetinanetTutorial/RetinanetModels
cp ~/RetinanetTutorial/TrainingOutput/snapshots/resnet50_pascal_06.h5 ~/RetinanetTutorial/RetinanetModels/PlumsTraining.h5
cd ~/RetinanetTutorial/keras-retinanet/
keras_retinanet/bin/convert_model.py ~/RetinanetTutorial/RetinanetModels/PlumsTraining.h5 ~/RetinanetTutorial/RetinanetModels/PlumsInference.h5
cd ~/RetinanetTutorial/
Edit testDetector.py to give paths for input and output

# Detection
cd /home/datascience/RetinanetTutorial/Retinanet-Tutorial
!python testDetector.py
