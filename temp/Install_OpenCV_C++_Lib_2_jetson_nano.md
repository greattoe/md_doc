## 라즈베리파이4 OpenCV C++ Libray 설치

**이 튜토리얼 작성 환경 :**  

- **타겟 PC :** Jetson Nano (4GB RAM) / Ubuntu 18.04 LTS
- **OpenCV 버전 :** 4.5.1

**출처 :** <https://qengineering.eu/install-opencv-4.5-on-jetson-nano.html>

---

jetpack( Jetson Nano OS ) 설치가 완료된 상태에서 OpenCV 라이브러리를 CUDA 가 지원되도록 빌드하는 방법을 알아보자. 



---

### 0. `jetson-stats` 설치

`jetson-stats` : 모든 NVIDIA Jetson 에코시스템에서 작동하는 [NVIDIA Jetson](http://www.nvidia.com/object/embedded-systems-dev-kits-modules.html) [Xavier NX, Nano, AGX Xavier, TX1, TX2] 을 **모니터링** 하고 **제어** 하기 위한 패키지

`pip3` 설치

```
sudo apt install python3-pip
```

`jetson-stats`  설치

```
sudo -H pip3 install -U jetson-stats
```

`jetson_relase`  실행

```
jetson@nano:~$ jetson_release
 - NVIDIA Jetson Nano (Developer Kit Version)
   * Jetpack 4.6 [L4T 32.6.1]
   * NV Power Mode: MAXN - Type: 0
   * jetson_stats.service: active
 - Libraries:
   * CUDA: 10.2.300
   * cuDNN: 8.2.1.32
   * TensorRT: 8.0.1.6
   * Visionworks: 1.6.0.501
   * OpenCV: 4.1.1 compiled CUDA: NO
   * VPI: ii libnvvpi1 1.1.12 arm64 NVIDIA Vision Programming Interface library
   * Vulkan: 1.2.70
```

위 `jetson_release`  실행 결과로부터 `jetpack` 버전, 설치되어 있는 `CUDA` 및 `cuDNN` 버전, `OpenCV`  버전 정보를 알 수 있으며 `OpenCV` 가 `CUDA` 를 사용하도록 빌드되어 있지 않다는 것을 알 수 있다.  





---

### 1. 업데이트 및 업그레이드

설치가능한 패키지들의 Repository 정보 갱신을 위해 `apt update` 명령을 실행한다.

```
sudo apt update
```

이미 설치된 패키지들을 최신버전으로 유지하기위해 `apt upgrade` 명령을 실행한다.

```
sudo apt upgrade
```



---

### 2. OpenCV 설치를 위한 의존성 패키지 설치

설치 전 필요한 패키지들을 설치하는 과정이다. 

##### 2.1 컴파일 및 빌드 작업에 필요한 의존성 패키지 설치

```
sudo apt install -y build-essential cmake
```

##### 2.2 이미지 파일 열기 및 저장 작업에 필요한 의존성 설치

```
sudo apt-get install -y libjpeg-dev libtiff5-dev libjasper-dev libpng12-dev
```

##### 2.3 FFmpeg 코덱의 비디오 파일/스트리밍 읽기/쓰기 작업에 필요한 의존성 설치

```
sudo apt-get install -y libavcodec-dev libavformat-dev libswscale-dev libxvidcore-dev libx264-dev libxine2-dev
```

##### 2.4 실시간 비디오 캡쳐 지원을 위한 의존성 설치

```
sudo apt-get install -y libv4l-dev v4l-utils
```

##### 2.5 Gstreamer 스트리밍 읽기/쓰기 작업에 필요한 의존성 설치

```
sudo apt-get install -y libgstreamer1.0-dev libgstreamer-plugins-base1.0-dev gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly
```

##### 2.6 GUI환경에서 영상 출력 윈도우 생성등을 위한 의존성 설치

```
sudo apt-get install -y libgtk2.0-dev
```

##### 2.7 OpenGL 지원을 위한 의존성 설치

```
sudo apt-get install -y mesa-utils libgl1-mesa-dri libgtkgl2.0-dev libgtkglext1-dev
```

##### 2.8 OpenCV 코드 최적화 관련 의존성 설치

```
sudo apt-get install -y libatlas-base-dev gfortran libeigen3-dev
```

##### 2.9 파이썬 관련 의존성 설치

```
sudo apt-get install -y python2.7-dev python3-dev python-numpy python3-numpy
```



---

### 3. OpenCV C++ Library 설치

이 과정은 OpenCV C++ 라이브러리와 추가모듈의 소스코드를 다운로드, 빌드설정, 빌드, 설치의 과정으로 진행된다.

##### 3.1 OpenCV  및 opencv_contrib(extra modules) 소스코드 다운로드

작업 폴더 생성 후, 생성된 폴더로 경로 변경

```
mkdir opencv && cd opencv
```

OpenCV 4.5.1 소스코드 다운로드

```
wget -O opencv.zip https://github.com/opencv/opencv/archive/4.5.1.zip
```

아래와 같이 앞서 다운로드한 `opencv.zip` 파일의 압축을 해제한다.

```
unzip ./opencv.zip
```

opencv_contrib(extra modules)  소스코드 다운로드

```
wget -O opencv_contrib.zip https://github.com/opencv/opencv_contrib/archive/4.5.1.zip
```

아래와 같이 다운로드한 `opencv_contrib.zip` 파일의 압축을 해제한다. 

```
unzip opencv_contrib.zip
```

##### 3.2 빌드 설정

`opencv-4.5.1` 폴더로 작업경로 변경 

```
cd opencv-4.5.1
```

빌드를 위한 작업폴더 생성 및 경로 변경 

```
mkdir build && cd build
```

아래와 같이 `cmake` 를 이용하여 빌드를 위한 설정을 적용한다.

```
cmake -D CMAKE_BUILD_TYPE=RELEASE \
-D CMAKE_INSTALL_PREFIX=/usr \
-D OPENCV_EXTRA_MODULES_PATH=~/opencv_contrib/modules \
-D EIGEN_INCLUDE_PATH=/usr/include/eigen3 \
-D WITH_OPENCL=OFF \
-D WITH_CUDA=ON \
-D CUDA_ARCH_BIN=5.3 \
-D CUDA_ARCH_PTX="" \
-D WITH_CUDNN=ON \
-D WITH_CUBLAS=ON \
-D ENABLE_FAST_MATH=ON \
-D CUDA_FAST_MATH=ON \
-D OPENCV_DNN_CUDA=ON \
-D ENABLE_NEON=ON \
-D WITH_QT=OFF \
-D WITH_OPENMP=ON \
-D BUILD_TIFF=ON \
-D WITH_FFMPEG=ON \
-D WITH_GSTREAMER=ON \
-D WITH_TBB=ON \
-D BUILD_TBB=ON \
-D BUILD_TESTS=OFF \
-D WITH_EIGEN=ON \
-D WITH_V4L=ON \
-D WITH_LIBV4L=ON \
-D OPENCV_ENABLE_NONFREE=ON \
-D INSTALL_C_EXAMPLES=OFF \
-D INSTALL_PYTHON_EXAMPLES=OFF \
-D BUILD_NEW_PYTHON_SUPPORT=ON \
-D BUILD_opencv_python3=TRUE \
-D OPENCV_GENERATE_PKGCONFIG=ON \
-D BUILD_EXAMPLES=OFF ..
```

정상적으로 설정이 완료된 경우 다음과 같은 메세지로 종료된다.

```
-- Configuring done
-- Generating done
-- Build files have been written to: /home/pi/opencv/opencv-4.5.1/build
```

`make` 명령으로 빌드를 진행하기전 swap 사이즈를 늘려주기 위해( 빌드 시 메모리 부족으로 인한 에러 방지 및 빌드 시간단축을 위해 ) 다음과 같이 `/etc/dphys-swapfile` 파일을 편집한다.

```
sudo nano /etc/dphys-swapfile
```

```
# where we want the swapfile to be, this is the default
#CONF_SWAPFILE=/var/swap

# set size to absolute value, leaving empty (default) then uses computed value
#   you most likely don't want this, unless you have an special disk situation
CONF_SWAPSIZE=100 # <------ CONF_SWAPSIZE=2048 로 변경
```

아래 명령을 실행하여 변경된 내용을 반영한다.

```
sudo /etc/init.d/dphys-swapfile restart
```

다음 명령을 실행하여 빌드를 진행한다.

```
make -j4
```

완료까지 대략 1시간 정도 소요된다. ( 라즈베리파이4 모델B 4GB램 에서 빌드한 경우 )

##### 3.3 빌드된 라이브러리 설치

다음 명령을 실행하여 빌드된 OpenCV 4.5.1 C++ 라이브러리를 설치한다.

```
sudo make install
```

설치가 완료되면 참고할 수 있는 예제 코드들은 `/usr/local/share/opencv4/samples/` 에서 찾아볼 수 있다.

설치된 라이브러리 정보를 시스템 공유 라이브러리 정보에 반영하기위해 다음 명령을 실행한다.

```
sudo ldconfig
```

이제, 빌드를 위해 늘려 주었던 swap 사이즈를 되돌린다.

```
sudo nano /etc/dphys-swapfile
```

```
# set size to absolute value, leaving empty (default) then uses computed value
#   you most likely don't want this, unless you have an special disk situation
CONF_SWAPSIZE=1024 # <------ CONF_SWAPSIZE=100 으로 변경
```

변경된 swap 정보 반영을 위해 다음 명령을 실행한다.

```
sudo /etc/init.d/dphys-swapfile restart
```



---

### 4. 설치 결과 테스트

예제코드 중 하나를 컴파일 하여, 정상적으로 실행되는가를 확인해보자.

다음 명령을 입력하여 경로를 `/home/pi` 로 변경한다.

```
cd ~/
```

`/usr/local/share/opencv4/samples/cpp/videocapture_basic.cpp` 예제를 현재 경로로 복사한다.

```
cp /usr/local/share/opencv4/samples/cpp/videocapture_basic.cpp ./
```

`videocapture_basic.cpp` 를 컴파일하여 `videocap` 실행파일을 생성하도록 다음 명령을 실행한다.

```
g++ -o videocap videocapture_basic.cpp $(pkg-config opencv4 --libs --cflags)
```

생성된 실파일 `videocap` 을 실행한다.

```
./videocap
```

