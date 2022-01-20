# instructions for creating robomaker environment for mini pupper
## 1.create a development environment
create a development environment in aws robomaker console,</br>
[check this link](https://ap-northeast-1.console.aws.amazon.com/robomaker/home?region=ap-northeast-1#ides/new)</br>
remember to set "pre-installed robot software suite" to "Melodic"</br>
</br>
## 2. configure your development environment
### 2.1 install colcon


```sh
sudo apt-get update
sudo apt-get install python3-pip python3-apt
```
```sh
pip3 install -U setuptools
pip3 install -U colcon-common-extensions colcon-ros-bundle
```
### 2.2 install dependencies
```sh
sudo apt install ros-melodic-ecl-threads ros-melodic-robot-localization  ros-melodic-controller-manager ros-melodic-hector-sensors-description ros-melodic-map-server ros-melodic-move-base ros-melodic-iris-lama ros-melodic-iris-lama-ros sphinxsearch
```
```sh
pip install --upgrade requests
pip3 install --upgrade requests
```

### 2.3 configure the application
```sh
cd ~/environment
git clone --recursive https://github.com/0nhc/aws-robomaker-mini-pupper-application.git
cd robot_ws
colcon build
colcon bundle
cd ..
cd simulation_ws
colcon build
colcon bundle
echo "source ~/environment/aws-robomaker-mini-pupper-application/robot_ws/install/setup.bash" >> ~/.bashrc
echo "source ~/environment/aws-robomaker-mini-pupper-application/simulation_ws/install/setup.bash" >> ~/.bashrc
source ~/.bashrc
```

### 2.4 create aws S3 buckets
create S3 buckets for mini pupper</br>
[check this link](https://s3.console.aws.amazon.com/s3/bucket/create?region=ap-northeast-1)</br>
set "bucket name" to "mini-pupper-bucket"</br>


### 2.5 upload bundle file to the buckets
return to aws development environment</br>
```sh
cd ~/environment/aws-robomaker-mini-pupper-application/robot_ws/bundle
aws s3api put-object --bucket mini-pupper-bucket --key mnpp_robot.tar --body output.tar
cd ~/environment/aws-robomaker-mini-pupper-application/simulation_ws/bundle
aws s3api put-object --bucket mini-pupper-bucket --key mnpp_sim.tar --body output.tar
```

## 3. create robot application
return to robomaker console</br>
then create a new robot application</br>
[check this link](https://ap-northeast-1.console.aws.amazon.com/robomaker/home?region=ap-northeast-1#robotApplications/new)</br>
set "name" to "mini-pupper-robot"</br>
set "robot software suite" to "Melodic"</br>
set "application" to "provide a colcon-bundled source file"</br>
and then click "browse S3"</br>
choose "mini-pupper-bucket/mnpp_robot.tar"</br>
then click "create"</br>

## 4. create simulation application
return to robomaker console</br>
then create a new simulation application</br>
[check this link](https://ap-northeast-1.console.aws.amazon.com/robomaker/home?region=ap-northeast-1#robotApplications/new)</br>
set "name" to "mini-pupper-simulation"</br>
set "robot software suite" to "Melodic"</br>
set "application" to "provide a colcon-bundled source file"</br>
and then click "browse S3"</br>
choose "mini-pupper-bucket/mnpp_sim.tar"</br>
then click "create"</br>

## 5. create simulation job
return to robomaker console</br>
then create a new simulation job</br>
[check this link](https://ap-northeast-1.console.aws.amazon.com/robomaker/home?region=ap-northeast-1#simulationJobs/new)</br>

### 5.1
in section "simulation job details"</br>
set "simulation job duration" to 1 hour(for saving money)</br>
set "robot software suite" to "Melodic"</br>
set "IAM role" to whatever you want</br>

in section "simulation job output"</br>
click "browse S3"</br>
choose "mini-pupper-bucket"</br>

### 5.2
in section "choose method"</br>
click "select existing application"</br>

in section "robot application"</br>
choose "mini-pupper-robot"</br>

in section "robot application configuration"</br>
set "launch package name" to "mini_pupper_navigation"</br>
set "launch file" to "navigate.launch"</br>


### 5.3
in section "choose method"</br>
click "select existing application"</br>

in section "robot application"</br>
choose "mini-pupper-simulation"</br>

in section "robot application configuration"</br>
set "launch package name" to "mini_pupper_gazebo"</br>
set "launch file" to "gazebo.launch"</br>

### 5.4
just click "create"</br>
