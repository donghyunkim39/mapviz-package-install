# mapviz-package-install

For user who: ubuntu Ros1(ROS) user, GPS latitude(위도) / longitude(경도) 를 가지고 실제 map에 핑을 찍어 보고싶은 user, rviz 대신 직접적인 시각화를 원하는 user

필수사항: 위도, 경도를 topic이 담긴 rosbag 파일 혹은 실시간으로 위도 경도 topic을 출력하는 환경
![info](https://github.com/donghyunkim39/mapviz-package-install/assets/163104650/bb8ec5d1-ff4e-4dfe-a6b5-c45aa1131927)


```bash
rostopic info /GPS 토픽이름 ex) rostopic info /gps/fix
```
위 명령어를 터미널창에 입력했을때 위사진과 같이 Type: sensor_msgs/NavsatFix 이여야함.
만일 위 Type과 일치하지 않는다면 개인적으로 python과 같은 코딩 툴을 이용해서 Type을 바꿔주길 바람.

## 0단계(option): Package 다운받을 WS 폴더 만들기 

1) WS(폴더) 생성 #폴더 이름은 자유 but, 여기서 test_ws라고 함.
ubuntu 터미널창에 다음을 입력

```bash
mkdir -p ~/test_ws/src
```
2) catkin_make 진행
```bash
cd ~/test_ws/
```

```bash
catkin_make
```
3) 변경 설정 적용 (bash가 아닌 zsh 사용중이면 source devel/setup.zshrc
```bash
source devel/setup.bash
```

## 1단계 plugin install

준비: 자신의 ubuntu 버전(DISTRO) 를 알아야함 (18.04: melodic, 20.04: noetic 등)

1)

ex) 본인의 ubuntu버전이 noetic이라면(20.04) sudo apt-get install ros-noetic-mapviz 이라 입력, 아래 3개도 마찬가지
```bash
sudo apt-get install ros-$ROS_DISTRO-mapviz
```

2)
```bash
sudo apt-get install ros-$ROS_DISTRO-mapviz-plugins
```

3)
```bash
sudo apt-get install ros-$ROS_DISTRO-tile-map
```

4)
```bash
sudo apt-get install ros-$ROS_DISTRO-multires-image
```

## 2단계 mapviz package install

1) 원하는 폴더의 src경로 내 접속 (여기선 test_ws/src에 접속함)
   
```bash
cd test_ws/src
```

2) src폴더내에 ROS1버전 mapviz package install
   
```bash
git clone -b master https://github.com/swri-robotics/mapviz --recursive
```

3) catkin_make 진행

```bash
cd ~/test_ws
```

```bash
catkin_make
```

4) AttributeError: module 'em' has no attribute 'RAW_OPT' , Invoking "make -j8 -l8" failed  오류발생
   
이 단계는 AttributeError: module 'em' has no attribute 'RAW_OPT',  Invoking "make -j8 -l8" failed 오류가 발생안하면  5. 으로 jump
만약 발생한다면 em module 설치

```bash
pip uninstall em
```
애초에 안깔려있다면 파일이 존재하지 않는다고 뜰수있음

```bash
pip install empy==3.3.4
```

이후 test_ws내에서(src경로 밖) catkin_make 다시 진행하면 성공적으로 완료!

5) 변경 사항 적용
test_ws 경로에서(src 폴더 경내 밖)
```bash
source devel/setup.bash
```

## 3단계 launch 파일 수정

1) test_ws/src/mapviz/mapviz/launch 경로 내 mapviz.launch 파일을 연다.
  
  본인 컴퓨터 마다 경로는 다를수있으나 your_ws/src/mapviz/mapviz/launch 는 같다.

2) launch 파일 수정


> <수정 전>
![수정전](https://github.com/donghyunkim39/mapviz-package-install/assets/163104650/775ce8ad-2375-4fd3-866d-a97889d8b6b7)

> <수정 후>

![Screenshot from 2024-04-24 00-40-16](https://github.com/donghyunkim39/mapviz-package-install/assets/163104650/d791c786-538e-43f7-927f-48d4d7af9ec6)

필독) 수정 내용:
 > 수정1) <param name="local_xy_origin" value="swri"/> 에서 <param name="local_xy_origin" value="auto"/> 
 : 실시간으로 GPS값을 읽어오기 위함
 
 > 수정2) <remap from="fix" to="/gps/fix"/> 추가 하기 
 : 필자의 경우 위도, 경도 정보가 /gps/fix 토픽에 담겨있으므로 /gps/fix라고 설정함. 개인환경에 맞춰 수정 필요. (본인 토픽이름에 맞게 수정 필요) 

 3) 수정한 launch file을 Save 한다.

## 4단계 launch 실행

```bash
roslaunch mapviz mapviz.launch
```

## 5단계 GPS 핑 찍어 보기

1) 위도, 경도를 topic이 담긴 rosbag 파일 혹은 실시간으로 위도 경도 topic을 출력하는 환경을 구성하여 rostopic list에서 GPS 토픽이 출력되게 한다.
 (필자의 경우 rosbag play를 통해 gps/fix 토픽을 실시간으로 받아오는 중임)

2) 'navsat' Add 하기

>왼쪽 하단 "Add" 버튼 클릭후 navsat 클릭
![gps](https://github.com/donghyunkim39/mapviz-package-install/assets/163104650/40b9ebb3-2a1a-4db4-beef-08bc4415e0ec)


3) 'tile_map' Add 하기 
>왼쪽 하단 "Add" 버튼 클릭후 tile_map 클릭
![map](https://github.com/donghyunkim39/mapviz-package-install/assets/163104650/1c98f83c-294d-4b49-8137-5cd166a843d4)

4) GPS 토픽 설정하기
> navsat 윈도우에서 'Topic:' 칸에 'select' 클릭 -> 목록에 뜨는 현재 출력되는 GPS 토픽값으로 설정 -> OK버튼 클릭(필자의 경우 /gps/fix)
 -> 'usb Latest Transforms:' 칸을 check해준다.
만약 'usb Latest Transforms:' 칸이 보이지 않는다면 아래 코드 실행 후 mapviz종료 -> 아래코드 실행 -> roslaunch 재실행 등 여러방법을 동원하면 갑자기 뜨는 경우 있음 (이유는 모르겠음)

```bash
sudo rm ~/.mapviz_config
```
![설정완료](https://github.com/donghyunkim39/mapviz-package-install/assets/163104650/9a7576fa-149e-4ceb-a6b7-5f935c799794)

하지만, 우리가 원하는건 GPS포인트를 "실제 지도위에 찍어보는것!!"

## 6단계 실제 지도 불러오기

1) docker install

새 터미널 창에 아래 코드 입력 (오류 발생시 https://docs.docker.com/engine/install/ubuntu/ 참고)
```bash
sudo apt-get update
```
```bash
sudo apt-get install ca-certificates curl
```
```bash
sudo install -m 0755 -d /etc/apt/keyrings
```
```bash
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
```
```bash
sudo chmod a+r /etc/apt/keyrings/docker.asc
```

```bash
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```
```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```
```bash
mkdir ~/mapproxy
```
```bash
sudo docker run -p 8080:8080 -d -t -v ~/mapproxy:/mapproxy danielsnider/mapproxy
```

2) mapviz 에서 지도 설정하기
 이전에 켜놓았던 mapviz 윈도우에서 'source:' 클릭 -> custom WMTS source... 클릭 -> 'base URL:' 에 http://localhost:8080/wmts/gm_layer/gm_grid/{level}/{x}/{y}.png 로 변경 -> 'max Zoom:' 에서 'save' 클릭 -> 'Title source Name'은 알아서 설정(필자는 GIST라 함)
처음 켜면 시간이 걸림
![Screenshot from 2024-04-24 01-59-02](https://github.com/donghyunkim39/mapviz-package-install/assets/163104650/f33764f6-1107-4ae4-94f1-42930e616bc8)

## 추후에 실행할시

1)

```bash
roslaunch mapviz mapviz.launch
```

2)

>If case 1) sudo rm ~/.mapviz_config 진행 했을 경우

```bash
sudo docker run -p 8080:8080 -d -t -v ~/mapproxy:/mapproxy danielsnider/mapproxy
```
진행후

지도, GPS설정 다시한다.

3) rosbag play를 실행
> mapviz를 실행하고 rosbag play를 해야 GPS 토픽이 뜨는경우 존재



   
