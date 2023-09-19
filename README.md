# openMVG-SIT

Detailed tutorials can be found on 

https://openmvg.readthedocs.io/en/latest/software/SfM/SfM/#openmvg-sfm-pipelines

https://github.com/openMVG/openMVG

## Build instructions

Tested on Ubuntu 20.04 with python 3.8, GCC 9.4.0, cmake 3.27.2.

Required tools:

- CMake
- Git
- GCC>= 4.8.1
- ceres-solver
- eigen

Recommend using internal source of ceres and eigen packages by commenting out corresponding find_package functions in CMakeLists.txt.
```shell
openMVG/src/CMakeLists.txt:
line284: #find_package(Eigen3 QUIET)
line300: #find_package(Ceres QUIET HINTS ${CERES_DIR_HINTS})
```

## Compiling on Linux

1. Install the required external libraries.
```shell
$ sudo apt-get install libpng-dev libjpeg-dev libtiff-dev libxxf86vm1 libxxf86vm-dev libxi-dev libxrandr-dev
```

2. Checkout OpenMVG.
```shell
$ git clone https://github.com/yaxlee/openMVG-YAXUANLI.git
$ mkdir openMVG_Build && cd openMVG_Build
```

3. Configure and build
```shell
$ cmake -DCMAKE_BUILD_TYPE=RELEASE ../openMVG/src/
$ cmake --build . --target install
```

## Running Instrcution

Before running the program, make sure it gets correct parameters.   

Make sure camera type is in database: openMVG/src/openMVG/exif/sensor_width_database.txt. If not, add it manually:

```shell
$ cd openMVG/src/openMVG/exif/sensor_width_database
$ vim sensor_width_camera_database.txt
# add camera model manually
[camera name] [sensor width]
```

By default OpenMVG is using the sensor size and the focal length stored in the EXIF metadata, it provide a OK value to start from that the SfM process will refine later. If you have image with no metadata, you can:  

1. Specify the known pixel focal length value directly (from json file)
2. Or, if all your images have the same size, you can specify an approximate focal length to SfM_InitImageListing by using the -f option. The value to use as an argument can be 1.2 * max(image_width, image_height), which will be refined during the SfM process. (remeber f is pixel wise)
```shell
$ cd openMVG_Build/software/SfM/
$ vim SfM_SequentialPipeline.py
# add focal for "openMVG_main_SfMInit_ImageListing" by option "-k" at line 50
pIntrisics = subprocess.Popen( [os.path.join(OPENMVG_SFM_BIN, "openMVG_main_SfMInit_ImageListing"),  "-i", input_dir, "-o", matches_dir, "-d", camera_file_params, "-f", "your value"] )
```
3. Or, if all your images have the same intrinsic data, you can let the SfM process find it automatically(at least two images that share common keypoints and with valid intrinsic group must be defined). You may need to specify an intrinsic data to SfM_InitImageListing by using the -k option.
```shell
$ cd openMVG_Build/software/SfM/
$ vim SfM_SequentialPipeline.py
# add intrinsic parameters for "openMVG_main_SfMInit_ImageListing" by option "-k" at line 50
pIntrisics = subprocess.Popen( [os.path.join(OPENMVG_SFM_BIN, "openMVG_main_SfMInit_ImageListing"),  "-i", input_dir, "-o", matches_dir, "-d", camera_file_params, "-k", "fx;0;cx;0;fy;cy;0;0;1"] )
```
4. If your iamges have different size and different intrinsic parameters, it's ok to perform either step above, because SfM will refine these parameters. But for now I recommend to preprocess images to a same size for best reconstruction result. (Currently we are working on enabling the program to directly read intrinsic parametes from JSON file associated with the view.)


Make sure to declare camera model tpye for incremental reconstruction.

1: Pinhole

2: Pinhole radial 1

3: Pinhole radial 3 (default)

4: Pinhole radial 3 + tangential 2

5: Pinhole fisheye

```shell
$ cd openMVG_Build/software/SfM/
$ vim SfM_SequentialPipeline.py
# declare camera model type at "openMVG_main_IncrementalSfM" by option "-c" at line 74, for example fisheye camera is 5
pRecons = subprocess.Popen( [os.path.join(OPENMVG_SFM_BIN, "openMVG_main_SfM"), "--sfm_engine", "INCREMENTAL", "--input_file", matches_dir+"/sfm_data.json", "--match_dir", matches_dir, "--output_dir", reconstruction_dir, "-c", "5"] )
```

Then run the program by calling runing python file.

```shell
$ cd openMVG_Build/software/SfM/
$ python SfM_SequentialPipeline.py [full path image directory] [resulting directory]
$ python SfM_SequentialPipeline.py ~/home/user/data/ImageDataset/images ~/home/user/data/ImageDataset/Incremental_Reconstruction
```

 - full path image directory: folder of images
 - resulting directory: result, see example_output.json file for format


## Export undistorted images from known camera parameter intrinsic

The chain is designed to run on a sfm_data.json file. The sfm_data file should contains: - valid view with some defined intrinsics.
```
$ cd /openMVG_Build/Linux-x86_64-RELEASE
$ openMVG_main_ExportUndistortedImages -i [input_file] -o [output_dir]
```
 - input_file: a SfM_Data file with valid intrinsics and poses and optional structure (sfm_data.json) 
 - outdir: path where the undistorted images will be stored
