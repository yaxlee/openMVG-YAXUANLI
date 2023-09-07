openMVG-YAXUAN
=====================================

Build instructions
------------------
Tested on Ubuntu 20.04 with Cuda 10.1, python 3.8, GCC 9.4.0, cmake 3.27.2.

Required tools:

- CMake
- Git
- GCC>= 4.8.1
- ceres-solver
- eigen

To avoid version conflict on ceres-solver and eigen, make sure to use internal packages by commenting out find_package function.
```shell
openMVG/src/CMakeLists.txt:
line284: #find_package(Eigen3 QUIET)
line300: #find_package(Ceres QUIET HINTS ${CERES_DIR_HINTS})
```

Compiling on Linux
-------------------

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

Running Instrcution
-------------------

```shell
cd openMVG_Build/software/SfM/
$ python SfM_SequentialPipeline.py [full path image directory] [resulting directory]
$ python SfM_SequentialPipeline.py ~/home/user/data/ImageDataset/images ~/home/user/data/ImageDataset/Incremental_Reconstruction
```

 - Input: folder of images
 - Output: reconstruction pointcloud (colorized.ply)

Need to declare camera model tpye for incremental reconstruction.
```shell
cd cd openMVG_Build/software/SfM/
vim SfM_SequentialPipeline.py
# declare camera model type at "openMVG_main_IncrementalSfM" by option "-c" at line 74, for example fisheye camera is 5
pRecons = subprocess.Popen( [os.path.join(OPENMVG_SFM_BIN, "openMVG_main_SfM"), "--sfm_engine", "INCREMENTAL", "--input_file", matches_dir+"/sfm_data.json", "--match_dir", matches_dir, "--output_dir", reconstruction_dir, "-c", "5"] )
```
Detailed tutorials can be found on https://openmvg.readthedocs.io/en/latest/software/SfM/SfM/#openmvg-sfm-pipelines

Troubleshoot
-------------------

By default, openMVG requires input images to have exif information. If images don't have that, it may cause some errors and you need to add it manually.

For ERROR: [sequential_SfM.cpp:110] Unable to choose an initial pair, since there is no defined intrinsic data.
```shell
cd cd openMVG_Build/software/SfM/
vim SfM_SequentialPipeline.py
# add intrinsic parameters for "openMVG_main_SfMInit_ImageListing" by option "-k" at line 50
pIntrisics = subprocess.Popen( [os.path.join(OPENMVG_SFM_BIN, "openMVG_main_SfMInit_ImageListing"),  "-i", input_dir, "-o", matches_dir, "-d", camera_file_params, "-k", "fx;0;cx;0;fy;cy;0;0;1"] )
```

For WARNING: [main_SfMInit_ImageListing.cpp:458] Warning & Error messages: camera model doesn't exist in the database:
```shell
cd openMVG/src/openMVG/exif/sensor_width_database
vim sensor_width_camera_database.txt
# add camera model manually
```

Detailed tutorials can be found on https://openmvg.readthedocs.io/en/latest/software/SfM/SfM/#openmvg-sfm-pipelines
