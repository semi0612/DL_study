# Chest X-ray Abnormalities Detection

- Colab, Jupyter lab에서 실험을 진행하였습니다
- 이 대회는 Vingroup Big Data Institute가 VinBigData의 웹 기반 플랫폼인 VinLab을 통해 수집한 이미지를 통해 기초 연구를 촉진하여 새롭고 적용 가능성이 높은 기술 조사를 목표로 한 대회이며, Kaggle Web Site를 통하여 데이터를 제공받았습니다.
- url : [https://www.kaggle.com/competitions/vinbigdata-chest-xray-abnormalities-detection/overview](https://www.kaggle.com/competitions/vinbigdata-chest-xray-abnormalities-detection/overview)

### 주제 선정 이유
이미지 데이터의 처리 및 분석에 대하여 궁금증이 있었기 때문에 프로젝트를 진행하며 공부가 되지 않을까 싶었고, 팀플을 함께 진행하는 팀원의 전공이 의료 관련이였기에 접목 시켜 분석 및 결과 해석이 용이 하지 않을까 싶어 의료 이미지를 이용해보는 것으로 방향을 잡았습니다.

### 주제(문제)
의사들은 CT와 PET스캔 MRI 그리고 X-ray와 같은 데이터를 이용하여 의학적 상태를 진단하고 치료하는데, 다른 부위의 X-ray결과보다 흉부 X-ray 결과는 의학적 오진이 생기기 쉬운 부위라고 한다. X-ray에서 작은 size의 이상까지 보다 정확하게 식별하고 위치를 파악하는 것은 정확한 진단을 내리는데 도움이 될 수 있을 것이다.

### 분석 목표
흉부의 X-ray 이미지만으로 이상 유무를 정확하게 평가할 수 있을까?<br>
⇒ 흉부 방사선 사진에서 13가지 유형의 흉부 이상과 1가지의 이상없음을 파악하고 bounding box 를 그려보는 것을 목표로 한다.

------

### DICOM
이미지를 처리하기 앞서, 다뤄야할 이미지의 확장자가 dcm으로 되어있는 것을 확인. 이를 불러오고 처리하기 위해 어떤 확장자인지 알아봤다.

DICOM은 의료용 디지털 영상 및 통신 표준으로 의료용 기기에서 디지털 영상표현과 통신에 사용되는 여러가지 표준을 총칭하는 말이다. 일반 RGB 이미지가 픽셀값(0~255)로 이루어진 것과는 다르게 dicom 파일을 구성하고 있는 픽셀값은 (-x ~ +x)와 같이 범위가 마이너스부터 시작되는 경우도 있고, bit epth가 12bit, 16bit로 구성되어있는 경우도 있다고 한다.

예를 들어 CT에는 Hounsfield Unit(HU)라는 단위가 있고, 이 HU는 X선이 몸을 투과할 때 감쇠되는 정도를 나타내는 단위라 한다. 물을 통과할 때를 0으로 둔채 상대값으로 표현하는 것으로, 보고싶은 신체 부위가 있다면 HU table을 참고해 Window Center(보고싶은 부위의 HU값)와 Window Width(WC를 중심으로 관찰하고자 하는 HU 번위)를 조절한 뒤 그 부분을 위주로 출력해 줄 수 있다. 예를들어 HU table상 폐는 -600 ~ -400이므로 Window Center는 -600으로 잡고, Window Width는 1600으로 잡아주면 되는 것이다.

<br>

```python
# 파이썬으로 DICOM 파일을 다룰 수 있도록 도와주는 library
! pip install pylibjpeg pylibjpeg-libjpeg pydicom 
```
```
Collecting pylibjpeg
  Using cached pylibjpeg-1.4.0-py3-none-any.whl (28 kB)
Collecting pylibjpeg-libjpeg
  Using cached pylibjpeg_libjpeg-1.3.2-cp39-cp39-win_amd64.whl (1.7 MB)
Collecting pydicom
  Using cached pydicom-2.3.0-py3-none-any.whl (2.0 MB)
Requirement already satisfied: numpy in c:\users\admin\anaconda3\lib\site-packages (from pylibjpeg) (1.23.4)
Installing collected packages: pylibjpeg-libjpeg, pylibjpeg, pydicom
Successfully installed pydicom-2.3.0 pylibjpeg-1.4.0 pylibjpeg-libjpeg-1.3.2
```
test_img를 뽑아보려 한다. 경로를 지정해주고, pydicom에서 dicom/dcm 파일을 읽어줄때는 `dcmread`를 사용한다
```python
test_img = './-/train_images/1.2.826.0.1.3680043.10014/100.dcm'
dcm = pydicom.dcmread(test_img)

# 받아온 이미지의 속성 정보 중 하나 받아오기
dcm.PatientID
```
```
'10014'
```
경로에서 PatientID를 가져오는 걸 보니 제대로 받아온 것 같다. 시각적 이미지로 확인해보자
```python
import matplotlib.pyplot as plt

# 읽어온 dcm을 pixel_array에 넣어서 시각적 이미지로 확인하기
img = dcm.pixel_array
plt.imshow(img, cmap = plt.cm.bone)
```
![image](https://user-images.githubusercontent.com/51469989/199439391-5e58d302-2442-4016-9695-d2b7542b5d25.png)
<br>
이미지가 제대로 출력되었다. DICOM 파일을 처리하는 코드의 순서는 대략 아래와 같다고 한다. <br>
![image](https://user-images.githubusercontent.com/51469989/199441346-ae831514-3d71-4640-8305-947b712e5d42.png)
[출처 : DICOM 파일 읽는 법](https://ballentain.tistory.com/53)

위와 같이 읽어서 출력하는 모습은 DICOM Viewer로 출력한 이미자와는 차이가 발생할 수 있기 때문에 꼭 `Rescale Slppe, WindowCenter`와 같은 속성을 적용해 주어야 한다고 한다. 미세한 차이라도 실제 이미지에 적용하였을 때의 오류와 오차를 최대한 줄여하 하기 때문으로 이해했다.
```python
import numpy as np
from pydicom.pixel_data_handlers.util import apply_modality_lut, apply_voi_lut

plt.figure(figsize=(50, 50))

img_read1 = pydicom.read_file(test_img)
s = int(img_read1.RescaleSlope)
b = int(img_read1.RescaleIntercept)
img1 = s * img_read1.pixel_array + b

# 단순 CT_image
plt.subplot(1, 3, 1)
plt.title('CT image')
plt.imshow(img1, cmap = 'gray')

# apply_modality_lut() 와 apply_voi_lut()를 해준 image
img_read1.WindowCenter = -400
img_read1.windowWidth = 1000

img1 = apply_modality_lut(img1, img_read1)
img1_1 = apply_voi_lut(img1, img_read1)
plt.subplot(1, 3, 2)
plt.title('apply_vot_lut()')
plt.imshow(img1_1, cmap = 'gray')


# 평탄화작업까지 해주었을때
img1_2 = np.clip(img1, -400 - (1000 / 2), -400 + (1000 / 2))
plt.subplot(1, 3, 3)
plt.title('normalize')
plt.imshow(img1_2, cmap = 'gray')
```
![image](https://user-images.githubusercontent.com/51469989/199439757-0b615d89-3458-4908-ab6f-0fb5afd324c5.png)

