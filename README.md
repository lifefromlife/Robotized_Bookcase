# Robotized_Bookcase


### 1. 모터 처음 작동시킬 때

높은 전압을 요구하므로 마이크로5핀 외에 (리눅스 기준 /dev/ttyACM0 경로) 모터 전원선 혹은 배터리를 연결해야 합니다. 


### 2. 모터 번호 설정 

처음 모터를 구매하면 id값이 1로 존재하므로 openCR 보드에서 id를 scan해준 후 id_change 예제로 변환해야 합니다. 
아두이노-예제-openCR-Dynamixel Workbench 경로에서 확인하실 수 있습니다. 


### 3. 처음 노트북에 openCR 보드를 세팅

아두이노-환경설정 에서 
(https://raw.githubusercontent.com/ROBOTIS-GIT/OpenCR/master/arduino/opencr_release/package_opencr_index.json) 복사 붙여넣기로 추가해줍니다. 
그 다음 board manager에서 openCR 다운 받아주고, Library Manager에서 dynamixel 관련 라이브러리 다운 받아주어야 합니다. 


### 4. 포트 문제 발생시 

(https://emanual.robotis.com/docs/en/software/arduino_ide/)
에서 찾아보면 됩니다.
포트 인식이 안될 경우 
```
sudo chmod 666 /dev/ttyACM0
```
로 권한 부여가 안된 상태거나 
```
sudo usermod -a -G dialout $USER 
```
후 다시 해주면 해결 됩니다. 
소프트웨어 문제가 아니라면 하드웨어를 확인해보셔야 합니다.  


### 5. 코드 

다이나믹셀 baudrate는 57600으로 고정. openCR workbench 참고 
(https://emanual.robotis.com/docs/en/software/dynamixel/dynamixel_workbench/)
```
int motor_open[5] = {0,};
int sensor_on[5] = {0,};
int stack[5] = {0,};
```

초음파 센서 관련된 함수. motor_open은 모터가 작동될 때 트리거 역할을 하는 변수이고, sensor on은 초음파 센서가 측정하도록 활성화하는 트리거 역할,
stack은 일정 stack이 쌓이면 motor_open을 0으로 만들어 서랍을 닫히게 하는 역할을 합니다.
이 때 delay를 쓰지 않고 stack이라는 특정 변수가 더하는 값으로 만들어졌기 때문에 닫히는 타이밍은 반복적인 실험을 통해 확정하였습니다. 

```
Serial.begin(9600);
Serial2.begin(9600);
```
serial.begin은 시리얼 모니터 통신용이고 serial2 begin은 uart 포트 통신용.

```
dxl_wb.init(DEVICE_NAME, BAUDRATE, &log); 
```
모터 초기 세팅 

```
dxl_wb.ping(motor[모터번호], &model_number, &log);  
```
모터 번호에 맞게 모터 추가.
```
dxl_wb.setExtendedPositionControlMode(motor[모터 번호], &log); 
```
모터 포지션 컨트롤 모드. 이것도 다이나믹셀 emanual api reference에 있는데 
여러 함수가 있음. setExtendedPositionControlMode은 1번 이상으로 position 이동이 가능. 
속도 제어보다 포지션 이동이 정확한 모터 구동이 되서 사용 중.
```
dxl_wb.getPresentPositionData(motor[모터번호], &presentposition[9], &log);
```
현재 위치를 지정해주는 거. 그래서 처음 서랍 위치를 잘 맞추는게 중요. 열린 상태로 처음 위치를 지정하면 레일하고 벨트 부분 망가집니다.


----------------------------------------------------------------

### 미니어처 책장
책장은 아두이노 우노 보드 같은 경우에는 UTP선과 보드를 납땜하였고,
열고자 하는 서랍 부분만 미리 홈을 내어 서보모터를 달았습니다. 

코드는 스위치 라이브러리 다운 받아주시고 업로드 해주셔야 합니다.

서보모터가 한쪽만 연결되어 있기 때문에 모멘트를 받아서 구동할 때 서랍이 덜컹 거리고 움직이지 않을 수가 있습니다. 
그래서 3D 프린터로 서랍 모양의 출력물을 만들 때 맞은 편 면에 지지대를 하나 만들었습니다. 
