
# Train a Tensorflow object detection model using Google Colab

# Prerequisites
1. A folder for storing training chekpoints(You should have reasonably sufficient Google Drive storage space to store at least a few training checkpoints (around 3-5 GB))

2. A folder for storing the `train.record` file. Please note that this repository does not contain information regarding the generation of the `train.record` file. It is assumed that you have the file in your computer.

# Steps involved

1. Run the TF_OD.ipynb notebook present in the repository on Google Colab with the hardware accelerator set to GPU. To do this, select `Runtime`, click on `Change Runtime type` and change the `hardware accelerator` to GPU.

2. Open the following link to see the list of available pretrained models on various datasets like MSCOCO, Kitti, Open Images, etc. Right click on the model that you wish to use and copy the link address.

3. Paste this link in the first cell of the jupyter notebook to download the model to Google Colab. Anything which you download will be stored in the `/content/` directory of Google Colab.

4. Run the second cell to unzip its contents. After unzipping, you can delete the `frozen_inference_graph.pb` file if you wish since you won't be needing it as you are going to train a model.

5. Run the next two cells as such since they contain the necessary files and directories needed by the Tensorflow Object Detection API

6. The next cell is needed to mount your Google Drive to upload files needed for traning and save training checkpoints
Note: Tensorflow keeps saving training checkpoints every 1000 steps or so. It is important to regularly check the folder containing the training checkpoints and moving tho older ones to trash and emptying it regularly. Otherwise, your drive space may become full and the training will stop.

7. Before running the final cell, there are a couple of things we still need. The first is the configuration file of the model and the second is the training record(train.record) file.

You can get the config file of the model by simply typing the name of the model in google and it is probably the first or second search result obtained. A config file for `faster_rcnn_inception_v2` looks like [this][1]

For the sake of simplicity, I am going to be using this config file as an example to explain things from now on for the sake of uniformity. Nevertheless, the same procedure can be followed with any other config file.

Copy its contents to a file and name it with the file extension `.config`. We now need to change the following lines in the config file
* Change the `num_classes` in line 10 to the number of classes present in your dataset.

* Change the `min_dimension` and `max_dimension` to the height and width of your images in lines 13 and 14.

* I have changed the optimizer from `momentum_optimizer` to `adam` since it showed greater convergence speed. It is however not necessary to do so. You can learn more about changing the optimizer and other parameters by referring to this [link] if you wish.

* In line 107, you need to give the path from where the model should restore parameters during training. If you are training for the first time, then you can give the path of the mode.ckpt file which was downloaded using Google Colab. The path is something like '/content/faster_rcnn_inception_v2_coco_2018_01_28/model.ckpt'. If you already have trained for a certain number of steps(let's say 5023) and wish to restore training from there, you can replace the path with this `/content/drive/My Drive/folder_containing_checkpoints/model.ckpt-5023`. (The space between 'My' and 'Drive' in the path is intentionally kept and will not cause any error)

* You can choose whether to keep data augmentation or not in line 114. If you don't need it, you can just remove lines 114-117.

* In line 122, you need to provide the path to your `train.record file`. It may look like `content/drive/My\ Drive/folder_name/train.record`. It's a good idea to upload this to your drive and read it from there as it may sometimes be quite large in size and it may take a long time to upload directly in google colab.

* Line 124 contains the path to your `label_map.pbtxt` file. You can upload this file to the `/content/` directory in Google Colab. The path would then be like `/content/label_map.pbtxt`

* That is all we need to change in the config file. In case you are wondering why we are not changing anything in the lines after, it is because the eval config is not read by the `train.py` script as it is only going to train the model and not perform any evaluation. You can now save this file(asuume name is `frcnn_inception.config`) and upload this to the `/content/` directory in Google Colab

8. We are now ready to run the final cell of the notebook in Google Colab. Before you run this cell, change the `train_dir` to point to the folder where you want your checkpoints to be saved and the `pipeline_config_path` to point to the path of the `config file`. In my case, it was `/content/frcnn_inception.config`

9. You can now run the cell and it everything should work properly. It may take some time for the training to actually start and sometimes may take longer than usual. One minor setback once training begins is that you need to keep clearning the ouptut of the cell every 30 minutes or so because the page crashes becasue of the amount of HTML content which is constantly being printed to the console. You may sometimes get an error saying that ` A Google Drive Timeout has recently occured` a few seconds after running the `train.py` cell. To overcome this, stop the cell from executing, close the tab in your browser and reopen it. All the files that you have uploaded will still be saved and the error should not repeat. If it does, try doing this once more. Inspite of this, if the error persists, you may need to clear out some space from your Google Drive. You can refer to this [link] here for more ways to fix this issue.



[1]: https://github.com/tensorflow/models/blob/master/research/object_detection/samples/configs/faster_rcnn_inception_v2_coco.config
[2]: https://towardsdatascience.com/3-steps-to-update-parameters-of-faster-r-cnn-ssd-models-in-tensorflow-object-detection-api-7eddb11273ed
[3]:  https://research.google.com/colaboratory/faq.html
