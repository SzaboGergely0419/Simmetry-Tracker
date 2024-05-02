# Symmetry Tracker

The aim of this library is to provide a general tracking framework for ML-based time-symmetric tracking.

All codes here were developed by Gergely Szabó during his PhD research at [PPCU](https://itk.ppke.hu/en)

For any additional information or comments, please contact me via my university email address: szabo.gergely@itk.ppke.hu

Paper publication is still in progress, for the preprint version please check out: 
https://arxiv.org/abs/2308.03887

The frozen codebase version for the publication can be found at:
https://github.com/SzaboGergely0419/PPCU_IFOM_YeastTracker

While publication is in progress, please cite our work as:

```
@article{szabo2023enhancing,
  title={Enhancing Cell Tracking with a Time-Symmetric Deep Learning Approach},
  author={Szab{\'o}, Gergely and Bonaiuti, Paolo and Ciliberto, Andrea and Horv{\'a}th, Andr{\'a}s},
  journal={arXiv preprint arXiv:2308.03887},
  year={2023}
}
```

## Installation Requirements
      
```
pip install numpy
pip install opencv-python
pip install pycocotools
pip install matplotlib
pip install scipy
pip install pandas
pip install torch
pip install "git+https://github.com/facebookresearch/detectron2.git"
pip install segmentation-models-pytorch
pip install filterpy
```

## Resources

Models available at:
https://users.itk.ppke.hu/~szage11/IFOM%20tracking/TrainedModels/

Sample data available at:
https://users.itk.ppke.hu/~szage11/IFOM%20tracking/SampleData/

## Getting Started

#### Yeast Cell Tracking full pipeline (easier):
[![Open In Colab](https://img.shields.io/badge/Open%20in%20Colab-Open%20Notebook-blue?logo=google-colab)](https://colab.research.google.com/drive/1yAb-Cu4AcPdbFsU_OR_5rscIZaGri5lX?usp=drive_link)

#### Synthesized Person Tracking full pipeline (more complex and in development):
[![Open In Colab](https://img.shields.io/badge/Open%20in%20Colab-Open%20Notebook-blue?logo=google-colab)](https://colab.research.google.com/drive/1iyPZXEpoUV8twv48rMqeO0X5bjnzWJD8?usp=drive_link)

## Main functionalities

All fuctionalities which are intended to be directly used are listed here. The other functions and classes in the library have supportive roles and are only intended to be utilized by the functions listed here.

### Video Transformation
#### TransformVideoFromTIFF
- At: symmetry_tracker/video_transformation.py
- Transforms the input .tiff format video to the appropriate "folder of .png images" format

### Segmentation
#### SingleVideoSegmentation
- At: symmetry_tracker/segmentation/segmentator.py
- Performs segmentation on a single video. The models are only capable of handling the fixed image resolution they were trained on.
#### DisplaySegmentation
- At: symmetry_tracker/segmentation/segmentation_io.py
- Displays segmentation results generated by SingleVideoSegmentation() as images
#### WriteSegmentation
- At: symmetry_tracker/segmentation/segmentation_io.py
- Saves segmentation results in serial object contour txt format, which is later usable for tracking

### Tracking
#### SingleVideoSymmetryTracking
- At: symmetry_tracker/tracking/symmetry_tracker.py
- Performs object tracking using Symmetry Tracking based NN model on a video which was already segmented using both the video and the segmentation outputs
#### SingleVideoKalmanTracking
- At: symmetry_tracker/tracking/kalman_tracker.py
- Performs object tracking using the Kalman Filter on a video which was already segmented using both the video and the segmentation outputs.
This is only a reference estimator, and there are most likely other more convenient Kalman Filter implementations instead of this.
#### SingleVideoSymmetryTracking_L2Distance
- At: symmetry_tracker/tracking/symmetry_tracker_l2dist.py
- Performs object tracking using the Symmetry Tracker in a way that only object centroids are considered for similarity measurement, ignoring morphology
This is only a reference estimator for ablation study, use SingleVideoSymmetryTracking instead for optimal results and performance
#### SingleVideoSymmetryTracking_ShapeDistance
- At: symmetry_tracker/tracking/symmetry_tracker_shapedist.py
- Performs object tracking using the Symmetry Tracker in a way that only object morphology is considered for similarity measurement, ignoring position
This is only a reference estimator for ablation study, use SingleVideoSymmetryTracking instead for optimal results and performance
#### InterpolateMissingObjects
- At: symmetry_tracker/tracking/post_processing.py
- Interpolates the segmentations which should be part of contiuous tracks but were missed. This is not based on heuristics, but the interpolated positions and shapes might not fully match reality. 
Interpolated object instances are displayed with different colors at DisplayTracks() and SaveTracksVideo().
#### DisplayTracks
- At: symmetry_tracker/tracking/tracking_io.py
- Displays the tracking results generated by SingleVideoCellTracking() as images
#### WriteTracks
- At: symmetry_tracker/tracking/tracking_io.py
- Saves tracking results in standard txt format
#### SaveTracksVideo
- At: symmetry_tracker/tracking/tracking_io.py
- Saves tracking results as an .mp4 video

### Heuristical post-processing
These functionalities can improve / change tracking results significantly, but are based on additional heuristics and manual setup. 
#### HeuristicalEquivalence
- At: symmetry_tracker/tracking/post_processing.py
- Connects broken up tracks which have a significant metric similarity at the breaking point with a given maximal time difference.
This is a metric similarity between objects on different frames that are not supposed to look the same.
While performing this operation may lead to better results in practice, it is not necessarily sound mathematically and should be used carefully.
#### RemoveShortPaths
- At: symmetry_tracker/tracking/post_processing.py
- Removes the too short paths from a tracking under a given threshold.