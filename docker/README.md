# Guidance to use OpenPCDet with docker

You can either build the docker image through Dockerfile or pull the docker image from dockerhub. Please make sure nvidia-docker is corretly installed and that OpenPCDet is cloned 
locally.

## Build through Dockerfile (WATO)
Build docker image to include local OpenPCDet repository:
```shell script
docker build -f wato_cu116.Dockerfile -t openpcdet-docker /home/{USER}/
```

Then login to the container and make sure to mount the path of the model and data as volumes:
```shell script
docker run -it -v {WATO_DRIVE_PATH_TO_DATA}:/data/ -v {WATO_DRIVE_PATH_TO_MODELS}:/models openpcdet-docker:latest /bin/bash
```
E.g.
```shell script
docker run -it -u root -v /mnt/wato-drive2/nuscenes-1.0-mini:/data/nuscenes-mini -v /mnt/wato-drive2/perception-weights/lidar_object_detection:/models openpcdet-docker:latest /bin/bash
```

Inside the container, you can run OpenPCDet through xvfb using the start-xvfb.sh script:  
where the models and data directories are from the root of your container and must be downloaded from their respective sources!
```shell script
cd tools
/usr/local/bin/start-xvfb.sh python3 demo.py --cfg_file cfgs/kitti_models/pv_rcnn.yaml --ckpt /models/pv_rcnn8369.pth --data_path /data/velodyne/data/0000000050.bin
```

The rendered image will be created in the same directory as demo.py, this can be copied from the docker container where the second argument can be customized to your desired path:  
```shell script
docker cp {CONTAINER_ID}:/OpenPCDet/tools/bb_output.png /home/{USER}
```
## Build Through Dockerfile
Build docker image that support OpenPCDet through:
```shell script
docker build ./ -t openpcdet-docker
```
Note that if you would like to use dynamic voxelization, you need further install [`torch_scatter`](https://github.com/rusty1s/pytorch_scatter) package. 

From this Dockerfile, the installed version of spconv is 2.x, if you would like to use spconv 1.2.1, please follow these steps:
```shell script
git clone -b v1.2.1 https://github.com/djiajunustc/spconv spconv --recursive
cd spconv
python setup.py bdist_wheel
cd ./dist
pip install *.whl
```

## Pull From Dockerhub
Run the following script to pull the docker image:
```shell script
docker pull djiajun1206/pcdet:pytorch1.6
```
