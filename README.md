# SemesterProjectJulesPochon

The goal of this repo/post is to allow a robot serve beverages. This repo focus on the computer vision part of the project. The code has been written to work for an intel realsense D415 camera.

The hand detector model is built using data from the Egohands Dataset dataset. It contains 4800 images. All images are captured from an egocentric view (Google glass) across 48 different environments (indoor, outdoor) and activities (playing cards, chess, jenga, solving puzzles etc).
The Egohands dataset (zip file with labelled data) contains 48 folders of locations where video data was collected (100 images per folder).

    -- LOCATION_X
      -- frame_1.jpg
      -- frame_2.jpg
      ...
      -- frame_100.jpg
      -- polygons.mat  // contains annotations for all 100 images in current folder
    -- LOCATION_Y
      -- frame_1.jpg
      -- frame_2.jpg
      ...
      -- frame_100.jpg
      -- polygons.mat  // contains annotations for all 100 images in current folder

If it has not been done yet, donwload the "Tensorflow and the Tensorflow object detection api".

Some initial work needs to be done to the Egohands dataset to transform it into the format (tfrecord) which Tensorflow needs to train a model. This repo contains egohands_dataset_clean.py a script that is here to help generate the csv files.

    - Downloads the egohands datasets
    - Renames all files to include their directory names to ensure each filename is unique
    - Splits the dataset into train (80%), test (10%) and eval (10%) folders.
    - Reads in polygons.mat for each folder, generates bounding boxes and visualizes them to ensure correctness.
    - Once the script is done running, one should have an images folder containing three folders - train, test and eval. Each of these folders should also contain a csv label document each - train_labels.csv, test_labels.csv that can be used to generate tfrecords


First, some libraries needs to be downloaded for the script to work properly :

    cv2
    tensorflow
    datetime
    argparse
    matplotlib.pyplot         
    pyrealsense2
    numpy 
    time

In order to use the detector to detect hands, Load the frozen_inference_graph.pb trained on the hands dataset as well as the corresponding label map. In this repo, this is done in the utils/detector_utils.py script by the load_inference_graph method. 

The tensorflow object detection repo has a python file for exporting a checkpoint to frozen graph here. You can copy it to the current directory and use it as follows :

    python3 export_inference_graph.py \
        --input_type image_tensor \
        --model-checkpoint/ssd_mobilenet_v1_pets.config \
        --model-checkpoint/model.ckpt-200002 \ 
        --output_directory hand_inference_graph


Once all these steps have been done, the notebook related to the hand detection is called : "detect_single_threaded.ipynb". When running the notebook, a variable allows the user to determine the number of hands that need to be detected.

The method called "CanRobotPour(dist,dist_roi,color)" is here to check if the hand and the bottle are located in the same plan and to send a message to the robot in order to let it know that it can start pouring te beverage.

The method called "PourringOrNot(image)" aims at detecting the presence of the beverage while pouring, meaning that the action of pouring is being done properly.

At last, the method "ChangeReferential(Tx,Ty,Tz,angle,coordinates)" has been created to allow the robot to know the exact coordinates of the hands in its own referential. The argument of this method have to be entered manually by the user before running the notebook.


