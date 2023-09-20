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

## Compiling on Linux

```shell
$ sudo apt-get install libpng-dev libjpeg-dev libtiff-dev libxxf86vm1 libxxf86vm-dev libxi-dev libxrandr-dev
$ mkdir openMVG_Build && cd openMVG_Build
cmake -DCMAKE_BUILD_TYPE=RELEASE ../openMVG/src/
$ cmake --build . --target install
$ pip install -r requirements.txt
```

## Running Instrcution

```shell
$ cd openMVG_Build/software/SfM/
$ python SfM_SequentialPipeline.py [input image directory] [input json directory] [resulting directory]
$ python SfM_SequentialPipeline.py ~/home/user/data/Dataset/images ~/home/user/data/Dataset/json ~/home/user/data/Dataset/Incremental_Reconstruction
```

 - input image directory: images, JPG or PNG
 - input json directory: json files associated with images
 - resulting directory: result

## Result
After running the program, you can get following results:
- gobal pose estimation: /resulting directory/updated_json
- 3D reconstruction point cloud: /resulting directory/reconstruction_sequential/colorized.ply
- features: /resulting directory/matches
