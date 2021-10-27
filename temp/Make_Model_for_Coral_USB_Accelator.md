## 모델 생성

**이 튜토리얼 작성 환경 :**  

- **타겟 플랫폼 :** Raspberry Pi 4 Model B (4GB RAM) / Raspbian Buster

**출처 :** <https://coral.ai/docs/accelerator/get-started/>

---

이 문서를 작성하는 현재( 2021. 10. 20 ) 최신 Raspbian 배포 이미지는 `2021-05-07-raspios-buster-armhf.img` 이며, 이하의 내용은 이 이미지가 설치된 라즈베리파이4를 기준으로 설명하고 있음을 밝힌다. 

라즈베리파이 운영을 위한 모든 작업이 완료된 상태(인터넷 연결 포함)의 라즈베리파이에서 다음의 작업들을 수행한다. 



---

### 1. Edge TPU 를 위한 TensorFlow 모델

- 다음 운영체제 중 하나가 설치된 PC
  - 리눅스
    - Linux Debian 10, or a derivative thereof (such as Ubuntu 18.04), and a system architecture of either x86-64, Armv7 (32-bit), or Armv8 (64-bit) (Raspberry Pi is supported, but we have only tested Raspberry Pi 3 Model B+ and Raspberry Pi 4)
  - MacOS
    - macOS 10.15 (Catalina) or 11 (Big Sur), with either [MacPorts](https://www.macports.org/) or [Homebrew](https://brew.sh/) installed
  - Windows10

- 사용가능한 USB 포트 1EA( 최적의 성능을 위해 USB 3.0 권장 )

- Python 버전 3.6 ~ 3.9



### 2. Edge TPU 런타임 설치

Edge TPU 런타임은 Edge TPU에 대한 핵심 프로그래밍 인터페이스를 제공합니다. [Linux](https://coral.ai/docs/accelerator/get-started/#runtime-on-linux) , [Mac](https://coral.ai/docs/accelerator/get-started/#runtime-on-mac) 또는 [Windows ](https://coral.ai/docs/accelerator/get-started/#runtime-on-windows)[에서](https://coral.ai/docs/accelerator/get-started/#runtime-on-mac) 다음과 같이 호스트 컴퓨터에 설치할 수 있습니다.

USB Accelator 를 분리하고 다음 사항을 진행한다.

**레포지토리 등록**

```
echo "deb https://packages.cloud.google.com/apt coral-edgetpu-stable main" | sudo tee /etc/apt/sources.list.d/coral-edgetpu.list
```

**설치 키 획득**

```
curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
```

**레포지토리 반영**

```
sudo apt-get update
```

Edge TPU 런타임을 아래 두 가지 방법 중 한가지로 설치한다.

- Edge TPU 런타임 설치( 표준처리속도 )

```
sudo apt install libedgetpu1-std
```

- Edge TPU 런타임 설치( 최대처리속도 )

```
sudo apt install libedgetpu1-max
```

USB Accelator 를 USB 포트에 연결한다.



### 3. PyCoral 라이브러리 설치

```
sudo apt-get install python3-pycoral
```



### 4. 예제코드를 이용한 테스트

Github 에서 예제를 다운로드할 폴더 생성 후 해당 폴더로 경로변경

```
mkdir coral && cd coral
```

 예제 다운로드

```
git clone https://github.com/google-coral/pycoral.git
```

예제가 들어있는 `pycoral` 폴더로 경로 변경

```
cd pycoral
```

모델, 라벨 및 새(bird) 사진 다운로드

```
bash examples/install_requirements.sh classify_image.py
```

새(bird) 이미지 분류 코드를 실행

```
python3 examples/classify_image.py \
--model test_data/mobilenet_v2_1.0_224_inat_bird_quant_edgetpu.tflite \
--labels test_data/inat_bird_labels.txt \
--input test_data/parrot.jpg
```



