Information & Contact
=====================

This code was used to compute the results of the following paper:
  
>"Efficient feature extraction, encoding and classification for action recognition",  
Vadim Kantorov, Ivan Laptev,  
In Proc. Computer Vision and Pattern Recognition (CVPR), IEEE, 2014  

If you use this code, please cite our work:

> @inproceedings{kantorov2014,  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;author = {Kantorov, V. and Laptev, I.},  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;title = {Efficient feature extraction, encoding and classification for action recognition},  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;booktitle = {Proc. Computer Vision and Pattern Recognition (CVPR), IEEE, 2014},  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;year = {2014}  
}

For any question or bug report, please contact Vadim Kantorov at vadim.kantorov@inria.fr or vadim.kantorov@gmail.com


Description and usage
=====================

We release two tools in this repository. The first tool **motion_descriptors** is a motion feature extractor based on motion vectors from video compression information. The second is a fast Fisher vector computation tool **fv_fast** that uses vector SSE2 CPU instructions.

### motion_descriptors

The tool accepts a video file path as input and writes descriptors to standard output.  
##### Command-line options:

Option | Default | Description
--- | --- | ---
-i video.avi | | specifies the path to the input video
--hog yes/no | **yes** | enables/disables HOG descriptor computation
--hof yes/no | **yes** | enables/disables HOF descriptor computation
--mbh yes/no | **yes** | enables/disables MBH descriptor computation
-f 1-10 | whole video | restricts descriptor computation to the given frame range

The output format:
   The first two lines of the standard output are comments explaining the format):
>  #descr = hog(96) hof(108) mbh(96 + 96)  
   #x y pts StartPTS EndPTS Xoffset Yoffset PatchWidth PatchHeight descr


  + "x" and "y" are the normalized frame coordinates of the spatio-temporal (s-t) patch  
  + "pts" is the frame number of the s-t patch center  
  + "StartPTS" and "EndPTS" are the frame numbers of the first and last frames of the s-t patch  
  + "Xoffset" and "Yoffset" are the non-normalized frame coordinates of the s-t patch  
  + "PatchWidth" and "PatchHeight" are the non-normalized width and height of teh s-t patch
  + "descr" is the array of floats of concatenated descriptors. The size of this array depends on the enabled   descriptor types. All values are from zero to one. The first comment line describes the enabled descriptor types, their order in the array, and the dimension of each descriptor in the array.  
     
After the comments every line corresponds to an extracted descriptor of a patch. All numbers in the output are floating point in text format and are separated by tabs.  
The standard error contains various debug / diagnostic messages like time measurements and parameters in effect.

##### Examples:
  - Compute HOG, HOF, MBH and save the descriptors in **descriptors.txt**:
    > ./motion_descriptors -i video.avi > descriptors.txt

  - Compute only HOF and MBH from the first 500 frames and save the descriptors in **descriptors.txt**:
    > ./motion_descriptors -i video.avi -hog no -hof yes -mbh yes -f 1-500 > descriptors.txt

### fv_fast
The tool accepts descriptors on the standard input and writes Fisher vector (FV) to the standard output or a specified HDF5 file.
##### Command-line options:

Option | Default | Description
--- | --- | ---
--xnpos 0 | | specifies the column with **x** coordinate of the s-t patch in the descriptor array
--xntot 1.0 | 1.0 | specifies the frame width. If the **x** coordinate is non-normalized, this option is mandatory
--ynpos 1 | | specifies the column with **y** coordinate of the s-t patch in the descriptor array
--yntot 1.0 | 1.0 | specifies the frame width. If the **y** coordinate is non-normalized, this option is mandatory
--tnpos 2 | | specifies the column with **t** coordinate of the s-t patch in the descriptor array
--tntot 1.0 | 1.0 | specifies the frame width. If the **t** coordinate is non-normalized, this option is mandatory
-o out.h5 | | specifies the output HDF5 file
--gmm_k 256 | 256 | specifies the number of GMM components used for FV computation
--knn 5 | 5 | FV parts corresponding to these many closest GMM centroids will be updated during processing of every input descriptor
--vocab 9-104 hog_K256.vocab | | specifies descriptor type location and path to GMM vocabs. This option is mandatory, and several options of this kind are allowed.
--grid 1x3x2x | | specifies the layout of the s-t grid (**x** cells times **y** cells times **t** cells). This option is mandatory, and several options of this kind are allowed.





Building from source
====================

To build a tool, navigate to the correspoding directory in **src**, make sure you have the dependencies installed and type:
> $ make

The binaries will be placed in the **build** sub-directory.

Dependencies for **motion_descriptors**:
 - ffmpeg
 - opencv

Dependencies for **fv_fast**:
 - hdf5
 - opencv
