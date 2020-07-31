# DeepLabCut for multi-animal projects <img src="https://images.squarespace-cdn.com/content/v1/57f6d51c9f74566f55ecf271/1596197885984-YGB3SLDN795G1MHRL3QO/ke17ZwdGBToddI8pDm48kDeESvmL2CKgnnReSL1AEHUUqsxRUqqbr1mOJYKfIPR7LoDQ9mXPOjoJoqy81S2I8N_N4V1vUb5AoIIIbLZhVYxCRW4BPu10St3TBAUQYVKcEylu32zKYtOLotM52LGWiEW_ujpOfeZDdgBF7lQwn1cawo3Xn5c5CO-uPn_aeMmA/workflow22.png?format=2500w" width="550" title="DLC-live GUI" alt="DLC LIVE! GUI" align="right" vspace = "50">


The standard documents include all changes to the package to run DLC, but here is an extended version compiled for using DeepLabCut 2.2+ with multiple-animals. 

Note, we STRONGLY encourage you to use the [Project Manager GUI](https://github.com/DeepLabCut/DeepLabCut/blob/master/docs/PROJECT_GUI.md) when you first start using multi-animal mode. Each tab is customized for multi-animal when you create or 
load a multi-animal project. As long as you follow the recommendations within the GUI, you should be good to do! 

This document should serve as an **advanced user guide for maDLC,**
and it is here to support the scientific advances presented in the preprint (Mathis et al, 2020).

# Install and test: 

Install DLC as noted elsewhere, then run the test script found here (you will need to git clone first):
https://github.com/DeepLabCut/DeepLabCut/blob/master/examples/testscript_multianimal.py
```python
python testscript_multianimal.py
```

# Get started in the terminal:

Start iPython, or if you are using MacOS, you must use ``pythonw`` vs. typing ``ipython`` or ``python``, but otherwise it's the same.
If you are using DeepLabCut on the cloud, you cannot use the GUIs and you need to first set `DLClight=True`. If you use Windows, please always open the terminal with administrator privileges. Please read more [here](https://github.com/DeepLabCut/Docker4DeepLabCut2.0), and in our Nature Protocols paper [here](https://www.nature.com/articles/s41596-019-0176-0). And, see our [troubleshooting wiki](https://github.com/AlexEMG/DeepLabCut/wiki/Troubleshooting-Tips).

Open an ``ipython`` session and import the package by typing in the terminal:
```python
ipython
import deeplabcut
```
**TIP:** for every function there is a associated help document that can be viewed by adding a **?** after the function name; i.e. ``deeplabcut.create_new_project?``. To exit this help screen, type ``:q``.


### Create a New Project:

```python
deeplabcut.create_new_project('ProjectName','YourName', ['/usr/FullPath/OfVideo1.avi', '/usr/FullPath/OfVideo2.avi', '/usr/FullPath/OfVideo1.avi'],
              copy_videos=True, multianimal=True)
```
Tip: if you want to place the project folder somewhere please pass : ``working_directory = 'FullPathOftheworkingDirectory'``

- Note, if you are a Ubuntu user the path should look like: ``['/home/username/yourFolder/video1.mp4']``; if you are a Windows user, it should look like: ``[r'C:\username\yourFolder\video1.mp4']``
- Note, you can also put ``config_path = `` in front of the above line to create the path to the config.yaml that is used in the next step, i.e. ``config_path=deeplabcut.create_project(...)``)
    - If you do not, we recommend setting a variable so this can be easily used! Once you run this step, the conig_path is printed for you once you run this line, so set a variable for ease of use, i.e. something like:
```python
config_path = '/thefulloutputpath/config.yaml'
```
 - just be mindful of the formatting for Windows vs. Linux, see below.

This set of arguments will create a project directory with the name **Name of the project+name of the experimenter+date of creation of the project** in the **Working directory** and creates the symbolic links to videos in the **videos** directory. The project directory will have subdirectories: **dlc-models**, **labeled-data**, **training-datasets**, and **videos**.  All the outputs generated during the course of a project will be stored in one of these subdirectories, thus allowing each project to be curated in separation from other projects. The purpose of the subdirectories is as follows:

**dlc-models:** This directory contains the subdirectories *test* and *train*, each of which holds the meta information with regard to the parameters of the feature detectors in configuration files. The configuration files are YAML files, a common human-readable data serialization language. These files can be opened and edited with standard text editors. The subdirectory *train* will store checkpoints (called snapshots in TensorFlow) during training of the model. These snapshots allow the user to reload the trained model without re-training it, or to pick-up training from a particular saved checkpoint, in case the training was interrupted.

**labeled-data:** This directory will store the frames used to create the training dataset. Frames from different videos are stored in separate subdirectories. Each frame has a filename related to the temporal index within the corresponding video, which allows the user to trace every frame back to its origin.

**training-datasets:**  This directory will contain the training dataset used to train the network and metadata, which contains information about how the training dataset was created.  

**videos:** Directory of video links or videos. When **copy\_videos** is set to ``False``, this directory contains symbolic links to the videos. If it is set to ``True`` then the videos will be copied to this directory. The default is ``False``. Additionally, if the user wants to add new videos to the project at any stage, the function **add\_new\_videos** can be used. This will update the list of videos in the project's configuration file.

```python
deeplabcut.add_new_videos('Full path of the project configuration file*', ['full path of video 4', 'full path of video 5'], copy_videos=True/False)
```

*Please note, *Full path of the project configuration file* will be referenced as ``config_path`` throughout this protocol.

### Configure the Project:

- open the **config.yaml** file (in a text editor (like atom, gedit, vim etc.)), which can be found in the subfolder created when you set your project name, to change parameters and identify label names! This is a crucial step.

<img src="https://images.squarespace-cdn.com/content/v1/57f6d51c9f74566f55ecf271/1588892210304-EW7WD46PYAU43WWZS4QZ/ke17ZwdGBToddI8pDm48kAXtGtTuS2U1SVcl-tYMBOAUqsxRUqqbr1mOJYKfIPR7LoDQ9mXPOjoJoqy81S2I8PaoYXhp6HxIwZIk7-Mi3Tsic-L2IOPH3Dwrhl-Ne3Z2YjE9w60pqfeJxDohDRZk1jXSVCSSfcEA7WmgMAGpjTehHAH51QaxKq4KdVMVBxpG/1nktc1kdgq2.jpg?format=1000w" width="175" title="colormaps" alt="DLC Utils" align="right" vspace = "50">

Next, open the **config.yaml** file, which was created during  **create\_new\_project**. You can edit this file in any text editor.  Familiarize yourself with the meaning of the parameters (Box 1). You can edit various parameters, in particular you **must add the list of *bodyparts* (or points of interest)** that you want to track. You can also set the *colormap* here that is used for all downstream steps (can also be edited at anytime), like labeling GUIs, videos, etc. Here any [matplotlib colormaps](https://matplotlib.org/tutorials/colors/colormaps.html) will do!

Please DO NOT have spaces in the names of bodyparts, uniquebodyparts, individuals, etc.

**maDeepLabCut:** You need to edit the config.yaml file to **modify the following items** which specify the animal ID, body parts, and any unique labels. You should also define an over-connected 'skeleton' at this time in the config.yaml file. For example, for a "simple" 4 body part animal see the example skeleton. Note, we also highly recommend that you use **more bodypoints** that you might otherwise have, i.e., labeling along the spine/tail for 8 bodypoints would be better than four.

Modifying the `config.yaml` is crucial, especially connecting the skeleton:

```python
individuals:
- mouse1
- mouse2

uniquebodyparts:
- topleftcorner
- toprightcorner

multianimalbodyparts:
- snout
- leftear
- rightear
- tailbase

skeleton:
- - snout
  - tailbase
- - snout
  - leftear
- - snout
  - rightear
- - leftear
  - rightear
- - leftear
  - tailbase
- - rightear
  - tailbase

- - topleftcorner
  - toprightcorner
```
**individuals** are names of "individuals" in the annotation dataset. These can be generic (e.g. mouse1, mouse2, etc.). These individuals are comprised of the same bodyparts defined by `multianimalbodyparts`. For annotation in the GUI and training, it is important that all individuals in each frame are labeled. Thus, keep in mind that you might need to have many individuals, .i.e. if there is (even just one frame) with 17 pinguins then the list should be `- indv1` to `- indv17`. For inference, once trained if you have a video with more or less animals, that is fine - you can change this number before running video analysis.

**multianimalbodyparts:** are the bodyparts of each individual (in the above list). Each bodypart should be connected to all the others by at least one path (E.g. like in a [tree](https://en.wikipedia.org/wiki/Tree_(graph_theory)).). However, we recommend to have multiple paths, in a redundant way. These connections are defined in the skeleton. See below how to define the skeleton.

**Uniquebodyparts** are points that you want to track, but that appear only once within each frame, i.e. they are "unique". Typically these are things like unique objects, landmarks, tools, etc. They are treated similar to pre-2.2 projects; namely, they do not require a skeleton. They can also be animals, e.g. in the case where one German shepherd is attending to many sheep the sheep bodyparts would be multianimalbodyparts, the shepherd parts would be uniquebodyparts and the individuals would be the list of sheep (e.g. Polly, Molly, Dolly, ...).

**Note**, if your does not have any uniquebodyparts please format as: `uniquebodyparts: []`.

**HOW TO CONNECT YOUR SKELETON:** For identifying individuals, it is crucial to define links between bodyparts as a skeleton, and "over-connect" the bodyparts, as shown above. Each bodypart should be connected to all the others by at least one path (E.g. like in a [tree](https://en.wikipedia.org/wiki/Tree_(graph_theory)).). However, we recommend to have multiple paths, in a redundant way, this helps to correctly assemble animals even if they occlude each other.

For example, let's say you have the following bodyparts (left). What you might connect for plotting is in the middle, but what we ask you to do for training is on the far right (note, you can edit this after training to indeed plot just as in the middle!).

You can define the skeleton by creating a list of "edges" in the config.yaml file as shown above, or you can use our helper GUI (far right):

```python
deeplabcut.SkeletonBuilder(config_path)
```
:movie_camera: [How to Connect The Skeleton](https://www.youtube.com/watch?v=fQSJ0S08UyY)


<p align="center">
<img src="https://images.squarespace-cdn.com/content/v1/57f6d51c9f74566f55ecf271/1589256735280-SCN7CROSJNJWCDS6EK5T/ke17ZwdGBToddI8pDm48kB08p9-rNkpPD7A3fw8YFjZZw-zPPgdn4jUwVcJE1ZvWQUxwkmyExglNqGp0IvTJZamWLI2zvYWH8K3-s_4yszcp2ryTI0HqTOaaUohrI8PIno0kSvzOWihTW1zp8-1-7mzYxUQjsVr2n3nmNdVcso4/bodyparts-skeleton.png?format=1000w" width="400">

<img src="https://images.squarespace-cdn.com/content/v1/57f6d51c9f74566f55ecf271/1589410182515-9SJO9MML6CNCXBAWQ6Z6/ke17ZwdGBToddI8pDm48kJ1oJoOIxBAgRD2ClXVCmKFZw-zPPgdn4jUwVcJE1ZvWQUxwkmyExglNqGp0IvTJZUJFbgE-7XRK3dMEBRBhUpxBw7VlGKDQO2xTcc51Yv6DahHgScLwHgvMZoEtbzk_9vMJY_JknNFgVzVQ2g0FD_s/ezgif.com-video-to-gif+%2811%29.gif?format=750w" width="400">
</p>
</p>


### Select Frames to Label:

**CRITICAL:** A good training dataset should consist of a sufficient number of frames that capture the breadth of the behavior. This ideally implies to select the frames from different (behavioral) sessions, different lighting and different animals, if those vary substantially (to train an invariant, robust feature detector). Thus for creating a robust network that you can reuse in the laboratory, a good training dataset should reflect the diversity of the behavior with respect to postures, luminance conditions, background conditions, animal identities,etc. of the data that will be analyzed. For the simple lab behaviors comprising mouse reaching, open-field behavior and fly behavior, 100−200 frames gave good results [Mathis et al, 2018](https://www.nature.com/articles/s41593-018-0209-y). However, depending on the required accuracy, the nature of behavior, the video quality (e.g. motion blur, bad lighting) and the context, more or less frames might be necessary to create a good network. Ultimately, in order to scale up the analysis to large collections of videos with perhaps unexpected conditions, one can also refine the data set in an adaptive way (see refinement below).

The function `extract_frames` extracts frames from all the videos in the project configuration file in order to create a training dataset. The extracted frames from all the videos are stored in a separate subdirectory named after the video file’s name under the ‘labeled-data’. This function also has various parameters that might be useful based on the user’s need.
```python
deeplabcut.extract_frames(config_path, mode='automatic/manual', algo='uniform/kmeans', userfeedback=False, crop=True/False)
```
**CRITICAL POINT:** It is advisable to keep the frame size small, as large frames increase the training and
inference time. The cropping parameters for each video can be provided in the config.yaml file (and see below).
When running the function extract_frames, if the parameter crop=True, then you will be asked to draw a box within the GUI (and this is written to the config.yaml file).

`userfeedback` allows the user to check which videos they wish to extract frames from. In this way, if you added more videos to the config.yaml file it does not, by default, extract frames (again) from every video. If you wish to disable this question, set `userfeedback = True`.

The provided function either selects frames from the videos in a randomly and temporally uniformly distributed
way (uniform), by clustering based on visual appearance (k-means), or by manual selection. Random
selection of frames works best for behaviors where the postures vary across the whole video. However, some behaviors
might be sparse, as in the case of reaching where the reach and pull are very fast and the mouse is not moving much
between trials (thus, we have the default set to True, as this is best for most use-cases we encounter). In such a case, the function that allows selecting frames based on k-means derived quantization would
be useful. If the user chooses to use k-means as a method to cluster the frames, then this function downsamples the
video and clusters the frames using k-means, where each frame is treated as a vector. Frames from different clusters
are then selected. This procedure makes sure that the frames look different. However, on large and long videos, this
code is slow due to computational complexity.

**CRITICAL POINT:** It is advisable to extract frames from a period of the video that contains interesting
behaviors, and not extract the frames across the whole video. This can be achieved by using the start and stop
parameters in the config.yaml file. Also, the user can change the number of frames to extract from each video using
the numframes2extract in the config.yaml file.

However, picking frames is highly dependent on the data and the behavior being studied. Therefore, it is hard to
provide all purpose code that extracts frames to create a good training dataset for every behavior and animal. If the user feels specific frames are lacking, they can extract hand selected frames of interest using the interactive GUI
provided along with the toolbox. This can be launched by using:
```python
deeplabcut.extract_frames(config_path, 'manual')
```
The user can use the *Load Video* button to load one of the videos in the project configuration file, use the scroll
bar to navigate across the video and *Grab a Frame* (or a range of frames, as of version 2.0.5) to extract the frame(s). The user can also look at the extracted frames and e.g. delete frames (from the directory) that are too similar before reloading the set and then manually annotating them.

### Label Frames:

```python
deeplabcut.label_frames(config_path)
```

As of 2.2 there is a new multi-animal labeling GUI (as long as in your `config.yaml` says `multianimalproject: true` at the top, this will automatically launch).

The toolbox provides a function **label_frames** which helps the user to easily label all the extracted frames using
an interactive graphical user interface (GUI). The user should have already named the body parts to label (points of
interest) in the project’s configuration file by providing a list. The following command invokes the labeling toolbox.

The user needs to use the *Load Frames* button to select the directory which stores the extracted frames from one of
the videos. Subsequently, the user can use one of the radio buttons (top right) to select a body part to label. RIGHT click to add the label. Left click to drag the label, if needed. If you label a part accidentally, you can use the middle button on your mouse to delete! If you cannot see a body part in the frame, skip over the label! Please see the ``HELP`` button for more user instructions. This auto-advances once you labeled the first body part. You can also advance to the next frame by clicking on the RIGHT arrow on your keyboard (and go to a previous frame with LEFT arrow).
Each label will be plotted as a dot in a unique color (see Figure 4 for more details).

The user is free to move around the body part and once satisfied with its position, can select another radio button
(in the top right) to switch to the respective body part (it otherwise auto-advances). The user can skip a body part if it is not visible. Once all the visible body parts are labeled, then the user can use ‘Next Frame’ to load the following frame. The user needs to save the labels after all the frames from one of the videos are labeled by clicking the save button at the bottom right. Saving the labels will create a labeled dataset for each video in a hierarchical data file format (HDF) in the
subdirectory corresponding to the particular video in **labeled-data**. You can save at any intermediate step (even without closing the GUI, just hit save) and you return to labeling a dataset by reloading it!

**CRITICAL POINT:** It is advisable to **consistently label similar spots** (e.g., on a wrist that is very large, try
to label the same location). In general, invisible or occluded points should not be labeled by the user. They can
simply be skipped by not applying the label anywhere on the frame.

OPTIONAL: In the event of adding more labels to the existing labeled dataset, the user need to append the new
labels to the bodyparts in the config.yaml file. Thereafter, the user can call the function **label_frames**. As of 2.0.5+: then a box will pop up and ask the user if they wish to display all parts, or only add in the new labels. Saving the labels after all the images are labelled will append the new labels to the existing labeled dataset.

**maDeepLabCut CRITICAL POINT:** For multi-animal labeling, unless you can tell apart the animals, you do not need to worry about the "ID" of each animal. For example: if you have a white and black mouse label the white mouse as animal 1, and black as animal 2 across all frames. If two black mice, then the ID label 1 or 2 can switch between frames - no need for you to try to identify them (but always label consistently within a frame). If you have 2 black mice but one always has an optical fiber (for example), then DO label them consistently as animal1 and animal_fiber (for example). The point of multi-animal DLC is to train models that can first group the correct bodyparts to individuals, then associate those points in a given video to a specific individual, which then also uses temporal information to link across the video frames.

Note, we also highly recommend that you use more bodypoints that you might otherwise have (see the example below).

**Example Labeling with maDeepLabCut:**
- note you should within an animal be consistent, i.e. all bodyparts on mouse1 should be on mouse1, but across frames "mouse1" can be any of the black mice (as here it is nearly impossible to tell them apart visually). IF you can tell them apart, do label consistently!

<p align="center">
<img src="https://images.squarespace-cdn.com/content/v1/57f6d51c9f74566f55ecf271/1588028248844-43RXXUNLE1VKJDKGGVFO/ke17ZwdGBToddI8pDm48kAxoZwLd0g_s-irkR9O2vUhZw-zPPgdn4jUwVcJE1ZvWQUxwkmyExglNqGp0IvTJZUJFbgE-7XRK3dMEBRBhUpxFjgZOWy5voI9x7QCcY8v6pdjAnJRY2VhSKj43SxhWXRPK8F08AQobuqKWFB6l9T0/labelingdemo.gif?format=750w" width="70%">
</p>

**mini-demo:** using the GUI to label

<p align="center">
<img src="https://static1.squarespace.com/static/57f6d51c9f74566f55ecf271/t/5c535f16fa0d60294ac39e4e/1548967721381/GUIdemo1.gif?format=750w" width="60%">
</p>

### Check Annotated Frames:

```python
deeplabcut.check_labels(config_path)
```
**maDeepLabCut**: you can also look at both bodypart labeling (standard) and individual IDs by also passing `visualizeindividuals=True`

Checking if the labels were created and stored correctly is beneficial for training, since labeling
is one of the most critical parts for creating the training dataset. The DeepLabCut toolbox provides a function
‘check_labels’ to do so. It is used as follows:
```python
deeplabcut.check_labels(config_path, visualizeindividuals=True/False)
 ```   
**maDeepLabCut:** you can check and plot colors per individual or per body part, just set the flag `visualizeindividuals=True/False`. Note, you can run this twice in both states to see both images.

<p align="center">
<img src="https://images.squarespace-cdn.com/content/v1/57f6d51c9f74566f55ecf271/1586203062876-D9ZL5Q7NZ464FUQN95NA/ke17ZwdGBToddI8pDm48kKmw982fUOZVIQXHUCR1F55Zw-zPPgdn4jUwVcJE1ZvWQUxwkmyExglNqGp0IvTJZUJFbgE-7XRK3dMEBRBhUpx7krGdD6VO1HGZR3BdeCbrijc_yIxzfnirMo-szZRSL5-VIQGAVcQr6HuuQP1evvE/img1068_individuals.png?format=750w" width="50%">
</p>

For each video directory in labeled-data this function creates a subdirectory with **labeled** as a suffix. Those directories contain the frames plotted with the annotated body parts. The user can double check if the body parts are labeled correctly. If they are not correct, the user can reload the frames (i.e. `deeplabcut.label_frames`), move them around, and click save again.

**CROP+LABEL:** When you are done checking the label quality and adjusting if needed, please then use this new function to crop frames /labels for more efficient training. PLEASE call this before you create a training dataset by:
```python
deeplabcut.cropimagesandlabels(path_config_file, userfeedback=False)
```
#### Reminder: Build your skeleton connections before you create a training set!

If you did not do this already be sure to define a skeleton in the `config.yaml` - See [more here for cruical details](functionDetails.md#b-configure-the-project-).

There is also a graphical way to define your skeleton:
```python
deeplabcut.SkeletonBuilder(config_path)
```
<p align="center">
<img src="https://images.squarespace-cdn.com/content/v1/57f6d51c9f74566f55ecf271/1589410182515-9SJO9MML6CNCXBAWQ6Z6/ke17ZwdGBToddI8pDm48kJ1oJoOIxBAgRD2ClXVCmKFZw-zPPgdn4jUwVcJE1ZvWQUxwkmyExglNqGp0IvTJZUJFbgE-7XRK3dMEBRBhUpxBw7VlGKDQO2xTcc51Yv6DahHgScLwHgvMZoEtbzk_9vMJY_JknNFgVzVQ2g0FD_s/ezgif.com-video-to-gif+%2811%29.gif?format=750w" width="80%">
</p>

### Create Training Dataset:

```python
deeplabcut.create_training_dataset(config_path)
```
or to [compare different neural networks](/wiki/What-neural-network-should-I-use%3F) use:
```python
deeplabcut.create_training_model_comparison(config_path, num_shuffles=1, net_types=['resnet_50'], augmenter_types=['default', 'imgaug'] )
```
:movie_camera:[VIDEO TUTORIAL AVAILABLE!](https://www.youtube.com/watch?v=WXCVr6xAcCA)


**maDeepLabCut**:

For mutli-animal training we use batch processing. This means that we'd like the data to be similarly sized. You can of course have differing size of images you label (and we suggest cropping out useless pixels!). So, we have a new function that can pre-process your data to be compatible with batch training. As noted above, please run this function before you `create_multianmialtraining_dataset`:

```python
deeplabcut.cropimagesandlabels(path_config_file)
```
Then run:
```python
deeplabcut.create_multianimaltraining_dataset(path_config_file)
```
**maDeepLabCut CRITICAL POINT**- you must use this new function if you have a multi-animal project (and the skeleton in the `config.yaml` **must be defined** before you run this step, if not already done). You **should also run** `deeplabcut.cropimagesandlabels(config_path)` before creating a training set, as we use batch processing and many users have smaller GPUs that cannot accommodate larger images + larger batchsizes. This is also a type of data augmentation.

- The set of arguments in the function will shuffle the combined labeled dataset and split it to create train and test
sets. The subdirectory with suffix ``iteration#`` under the directory **training-datasets** stores the dataset and meta
information, where the ``#`` is the value of ``iteration`` variable stored in the project’s configuration file (this number
keeps track of how often the dataset was refined).

- OPTIONAL: If the user wishes to benchmark the performance of the DeepLabCut, they can create multiple
training datasets by specifying an integer value to the `num_shuffles`; see the docstring for more details.

- Each iteration of the creation of a training dataset will create several files, which is used by the feature detectors,
and a ``.pickle`` file that contains the meta information about the training dataset. This also creates two subdirectories
within **dlc-models** called ``test`` and ``train``, and these each have a configuration file called pose_cfg.yaml.
Specifically, the user can edit the **pose_cfg.yaml** within the **train** subdirectory before starting the training. These
configuration files contain meta information with regard to the parameters of the feature detectors. Key parameters
are listed in Box 2.

- At this step, the ImageNet pre-trained networks (i.e. ResNet-50) weights will be downloaded. If they do not download (you will see this downloading in the terminal, then you may not have permission to do so (something we have seen with some Windows users - see the **[WIKI troubleshooting for more help!](https://github.com/AlexEMG/DeepLabCut/wiki/Troubleshooting-Tips)**).

**CRITICAL POINT:** For **create_multianimaltraining_dataset** we already change this such that you will use imgaug, ADAM optimization, and batch training. We suggest these defaults at this time.

**DATA AUGMENTATION:** At this stage you can also decide what type of augmentation to use. The default loaders work well for most all tasks (as shown on www.deeplabcut.org), but there are many options, more data augmentation, intermediate supervision, etc. Please look at the [**pose_cfg.yaml**](https://github.com/AlexEMG/DeepLabCut/blob/master/deeplabcut/pose_cfg.yaml) file for a full list of parameters **you might want to change before running this step.** There are several data loaders that can be used. For example, you can use the default loader (introduced and described in the Nature Protocols paper), [TensorPack](https://github.com/tensorpack/tensorpack) for data augmentation (currently this is easiest on Linux only), or [imgaug](https://imgaug.readthedocs.io/en/latest/). We recommend `imgaug`. You can set this by passing:``` deeplabcut.create_training_dataset(config_path, augmenter_type='imgaug')  ```

The differences of the loaders are as follows:
- `default`: our standard DLC 2.0 introduced in Nature Protocols variant (scaling, auto-crop augmentation) *will be renamed to `crop_scale` in a future release!*
- `imgaug`: a lot of augmentation possibilities, efficient code for target map creation & batch sizes >1 supported. You can set the parameters such as the `batch_size` in the `pose_cfg.yaml` file for the model you are training.
- `tensorpack`: a lot of augmentation possibilities, multi CPU support for fast processing, target maps are created less efficiently than in imgaug, does not allow batch size>1
- `deterministic`: only useful for testing, freezes numpy seed; otherwise like default.

Alternatively, you can set the loader (as well as other training parameters) in the **pose_cfg.yaml** file of the model that you want to train. Note, to get details on the options, look at the default file: [**pose_cfg.yaml**](https://github.com/AlexEMG/DeepLabCut/blob/master/deeplabcut/pose_cfg.yaml).


### Train The Network:

```python
deeplabcut.train_network(config_path, allow_growth=True)
```

The set of arguments in the function starts training the network for the dataset created for one specific shuffle. Note that you can change the loader (imgaug/default/etc) as well as other training parameters in the **pose_cfg.yaml** file of the model that you want to train (before you start training).

Example parameters that one can call:
```python
deeplabcut.train_network(config_path, shuffle=1, trainingsetindex=0, gputouse=None, max_snapshots_to_keep=5, autotune=False, displayiters=100, saveiters=15000, maxiters=30000, allow_growth=True)
```

By default, the pretrained networks are not in the DeepLabCut toolbox (as they are around 100MB each), but they get downloaded before you train. However, if not previously downloaded from the TensorFlow model weights, it will be downloaded and stored in a subdirectory *pre-trained* under the subdirectory *models* in *Pose_Estimation_Tensorflow*.
At user specified iterations during training checkpoints are stored in the subdirectory *train* under the respective iteration directory.

If the user wishes to restart the training at a specific checkpoint they can specify the full path of the checkpoint to
the variable ``init_weights`` in the **pose_cfg.yaml** file under the *train* subdirectory (see Box 2).

**CRITICAL POINT:** It is recommended to train the ResNets or MobileNets for thousands of iterations until the loss plateaus (typically around **200,000**) if you use batch size 1. If you want to batch train, we recommend using Adam, see more here: https://github.com/AlexEMG/DeepLabCut/wiki/Data-Augmentation.

If you use **maDeepLabCut** the recommended training iterations is **50K-100K** (it automatically stops at 200K!), as we use Adam and batch-training.

The variables ``display_iters`` and ``save_iters`` in the **pose_cfg.yaml** file allows the user to alter how often the loss is displayed and how often the weights are stored.

**maDeepLabCut CRITICAL POINT:** For multi-animal projects we are using not only different and new output layers, but also new data augmentation, optimization, learning rates, and batch training defaults. Thus, please use a lower ``save_iters`` and ``maxiters``. I.e. we suggest saving every 10K-15K iterations, and only training until 50K-100K iterations. We recommend you look closely at the loss to not overfit on your data. The bonus, training time is much less!!!

**Parameters:**
```python
config : string
    Full path of the config.yaml file as a string.

shuffle: int, optional
    Integer value specifying the shuffle index to select for training. Default is set to 1

trainingsetindex: int, optional
    Integer specifying which TrainingsetFraction to use. By default the first (note that TrainingFraction is a list in config.yaml).

gputouse: int, optional. Natural number indicating the number of your GPU (see number in nvidia-smi). If you do not have a GPU, put None.
See: https://nvidia.custhelp.com/app/answers/detail/a_id/3751/~/useful-nvidia-smi-queries

max_snapshots_to_keep: int, or None. Sets how many snapshots are kept, i.e. states of the trained network. For every saving interation a snapshot is stored, however, only the last max_snapshots_to_keep many are kept! If you change this to None, then all are kept.
See: https://github.com/AlexEMG/DeepLabCut/issues/8#issuecomment-387404835

autotune: property of TensorFlow, somehow faster if 'false' (as Eldar found out, see https://github.com/tensorflow/tensorflow/issues/13317). Default: False

displayiters: this variable is actually set in pose_config.yaml. However, you can overwrite it with this hack. Don't use this regularly, just if you are too lazy to dig out
the pose_config.yaml file for the corresponding project. If None, the value from there is used, otherwise it is overwritten! Default: None

saveiters: this variable is actually set in pose_config.yaml. However, you can overwrite it with this hack. Don't use this regularly, just if you are too lazy to dig out
the pose_config.yaml file for the corresponding project. If None, the value from there is used, otherwise it is overwritten! Default: None

maxiters: This sets how many iterations to train. This variable is set in pose_config.yaml. However, you can overwrite it with this. If None, the value from there is used, otherwise it is overwritten! Default: None
```    


### Evaluate the Trained Network:

Here, for traditional projects you will get a pixel distance metric and you should inspect the individual frames:
```python
deeplabcut.evaluate_network(config_path, plotting=True)
```
:movie_camera:[VIDEO TUTORIAL AVAILABLE!](https://www.youtube.com/watch?v=bgfnz1wtlpo)

**maDeepLabCut [CRITICAL POINT]:**

You need to **cross validate parameters** before inference. Here, you will run the new function:
```python
deeplabcut.evaluate_multianimal_crossvalidate(config_path, Shuffles=[1], edgewisecondition=True, leastbpts=1, init_points=20, n_iter=50)
```
We highly suggest that you read the docstring for this function to edit inputs appropriately.

([Here is more information](functionDetails.md#cross-validation-of-inference-parameters-a-madeeplabcut-critical-point))


**maDeepLabCut**: (or on normal projects!)

You can also plot the scoremaps, locref layers, and PAFs:
```python
deeplabcut.extract_save_all_maps(path_config_file, shuffle=shuffle, Indices=[0, 5])
```
- you can drop "Indices" to run this on all training/testing images (this is slow!)

(more details [here](functionDetails.md#h-evaluate-the-trained-network))

### Video Analysis:
- Please note that **novel videos DO NOT need to be added to the config.yaml file**. You can simply have a folder elsewhere on your computer and pass the video folder (then it will analyze all videos of the specified type (i.e. ``videotype='.mp4'``), or pass the path to the **folder** or exact video(s) you wish to analyze:

```python
deeplabcut.analyze_videos(config_path,['/fullpath/project/videos/'], videotype='.mp4', save_as_csv = True)
```
**maDeepLabCut**: there is a new step that allows you to plot *all* detections first. This allows you to check the pose-estimation quality before tracking of individuals! We recommend doing this step when you are running quality checks on new videos, etc. Once you have optimized pose-estimation and tracking, this is not required. `scorername` can be gotten from `scorername = deeplabcut.analyze_videos (...)` or just looking at the name of the DLC scorer in the folder name, h5 file, etc.

```python
deeplabcut.create_video_with_all_detections(path_config_file, ['videofile_path'], scorername)
```

### Assemble & Refine Tracklets in maDeepLabCut:

:movie_camera:[VIDEO TUTORIAL AVAILABLE!](https://youtu.be/bEuBKB7eqmk)
- Now that you have detections (which are saved as a pickle file, not h5, btw), we need to assemble and track the animals.

First, you need to convert detections to tracklets. This step has several tracker types (`track_method`), and we recommend testing which one works best on your data.

```python
deeplabcut.convert_detections2tracklets(path_config_file, ['videofile_path'], videotype='mp4',
                                                    shuffle=1, trainingsetindex=0, track_method='')
```
You should **cross-validate** the tracking parameters. ([Here is more information](functionDetails.md#cross-validation-of-inference-parameters-a-madeeplabcut-critical-point)). Namely, you can iteratively change the parameters, run `convert_detections2tracklets` then load them in the GUI (`refine_tracklets`). Note, that in the main Project Manager GUI there is a button for you to launch the inference file to seemlessly edit and rapidly test.

Secondly, you need to **refine the tracklets**. You can fix both "major" ID swaps, i.e. perhaps when animals cross, and you can micro-refine the individual body points. You will load the `...trackertype.pickle` file that was created above, and then you can launch a GUI to interactively refine the data. This also has several options, so please check out the docstring. Upon saving the refined tracks you get an `.h5` file (akin to what you might be used to from standard DLC. You can also load (1) filter this to take care of small jitters, and (2) load this `.h5` this to refine (again) in case you find another issue, etc!

```python
deeplabcut.refine_tracklets(path_config_file, pickle_or_h5_file, videofile_path, min_swap_len=2, min_tracklet_len=2, trail_len=50)
```
[Read more here!](functionDetails.md#madeeplabcut-critical-point---assemble--refine-tracklets)

Short demo:  
 <p align="center">
<img src="https://images.squarespace-cdn.com/content/v1/57f6d51c9f74566f55ecf271/1588690928000-90ZMRIM8SN6QE20ZOMNX/ke17ZwdGBToddI8pDm48kJ1oJoOIxBAgRD2ClXVCmKFZw-zPPgdn4jUwVcJE1ZvWQUxwkmyExglNqGp0IvTJZUJFbgE-7XRK3dMEBRBhUpxBw7VlGKDQO2xTcc51Yv6DahHgScLwHgvMZoEtbzk_9vMJY_JknNFgVzVQ2g0FD_s/refineDEMO.gif?format=750w" width="70%">
</p>

### Once you have analyzed video data (and refined your maDeepLabCut tracklets):

Firstly, Here are some tips for scaling up your video analysis, including looping over many folders for batch processing: https://github.com/AlexEMG/DeepLabCut/wiki/Batch-Processing-your-Analysis

You can also filter the predicted bodyparts by:
```python
deeplabcut.filterpredictions(config_path,['/fullpath/project/videos/reachingvideo1.avi'])
```
Note, this creates a file with the ending filtered.h5 that you can use for further analysis. This filtering step has many parameters, so please see the full docstring by typing: ``deeplabcut.filterpredictions?``

### Plotting Results:

- **NOTE :bulb::mega::** Before you create a video, you should set what threshold to use for plotting. This is set in the `config.yaml` file as `pcutoff` - if you have a well trained network, this should be high, i.e. set it to `0.8` or higher!


- You can also determine a good `pcutoff` value by looking at the likelihood plot created during `plot_trajectories`:

Plot the outputs:
```python
deeplabcut.plot_trajectories(config_path,['/fullpath/project/videos/reachingvideo1.avi'],filtered = True)
```

Create videos:
```python
deeplabcut.create_labeled_video(config_path, [`/analysis/project/videos/reachingvideo1.avi','/fullpath/project/videos/reachingvideo2.avi'],filtered = True)
```

(more details [here](functionDetails.md#i-video-analysis-and-plotting-results))

### [optional] Active Learning --> Network Refinement - extract outlier frames from a video:

```python
deeplabcut.extract_outlier_frames(config_path,['full/videofile_path'])
```

(more details [here](functionDetails.md#j-refinement-extract-outlier-frames))

### [optional] Refinement of the labels with our GUI:
(refinement and augmentation of the training dataset)

```python
deeplabcut.refine_labels(config_path)
```

**mini-demo:** using the refinement GUI, a user can load the file then zoom, pan, and edit and/or remove points:

<p align="center">
<img src="http://www.people.fas.harvard.edu/~amathis/dlc/refinelabels.gif" width="90%">
</p>

When done editing the labels, merge:
**PRO TIP:** if you added new data, even without refining, i.e. you added and labeled frames from new videos,  also use merge before creating a new training data set!

```python
deeplabcut.merge_datasets(config_path)
```
Now, create a new training set and re-train (same steps as above)! i.e.
```python
deeplabcut.create_training_dataset(config_path)
deeplabcut.train_network(config_path)
```

(more details [here](functionDetails.md#k-refine-labels-augmentation-of-the-training-dataset))


### HELP:

In ipython/Jupyter notebook:

```
deeplabcut.nameofthefunction?
```

In python or pythonw:

```
help(deeplabcut.nameofthefunction)
```

### Tips for "daily" use:

<p align="center">
<img src= https://static1.squarespace.com/static/57f6d51c9f74566f55ecf271/t/5ccc5abe0d9297405a428522/1556896461304/howtouseDLC-01.png?format=1000w width="80%">
 </p>

You can always exit an conda environment and easily jump back into a project by simply:

Linux/MacOS formatting example:
```
source activate yourdeeplabcutEnvName
ipython or pythonw
import deeplabcut
config_path ='/home/yourprojectfolder/config.yaml'
```
Windows formatting example:
```
activate yourdeeplabcutEnvName
ipython
import deeplabcut
config_path = r'C:\home\yourprojectfolder\config.yaml'
```

Now, you can run any of the functions described in this documentation.