#### conda를 통한 가상환경 설정
```powershell
(base) PS C:\Users\82104> conda create -n vision_module python=3.10
(base) PS C:\Users\82104> conda activate vision_module
(vision_module) PS C:\Users\82104> pip install mediapipe opencv-python numpy
(vision_module) PS C:\Users\82104> python -c "import mediapipe as mp; import cv2; print('✅ 설치 완료')"
✅ 설치 완료
```

#### `hello_mediapipe.py`
```python
import cv2
import mediapipe as mp

# Mediapipe 설정
mp_hands = mp.solutions.hands # 손 인식 모델 생성성
 # 최대 손 1개, 손을 익식할 최소 신뢰도, 추적 상태 유지 최소 신뢰도
hands = mp_hands.Hands(max_num_hands=1, min_detection_confidence=0.5, min_tracking_confidence=0.5)
mp_drawing = mp.solutions.drawing_utils # 손 관절에 시각적으로 선을 연결해주는 Util


def get_finger_status(hand):
    """
    손가락이 펴져 있는지 접혀 있는지 확인하는 함수

    Input:
        hand
    Returns:
        fingers_status[i, i, i, i, i](list)
        - 1: 펴져 있을 때
        - 0: 접혀 있을 때
    """
    # 오른손 기준
    fingers = []

    # 엄지 펼쳐졌는지 여부:
    # 랜드마크 4가 랜드마크 2의 오른쪽에 있으면 펼쳐진 상태
    if hand.landmark[4].x < hand.landmark[3].x:
        fingers.append(1)
    else:
        fingers.append(0)

    # 나머지 손가락 펼쳐졌는지 여부:
    # 각 손가락의 팁 (8, 12, 16, 20)이 PIP (6, 10, 14, 18) 위에 있으면 펼쳐진 상태
    tips = [8, 12, 16, 20]
    pip_joints = [6, 10, 14, 18]
    for tip, pip in zip(tips, pip_joints):
        if hand.landmark[tip].y < hand.landmark[pip].y:
            fingers.append(1)
        else:
            fingers.append(0)

    return fingers


def recognize_gesture(fingers_status):
    '''
    어느 손가락이 펼쳐졌는지에 따라 제스쳐의 의미를 정의함

    Input: 
        fingers_status(list)
    Returns:
        status(string)
    '''
    if fingers_status == [0, 0, 0, 0, 0]:
        return 'fist'
    elif fingers_status == [0, 1, 0, 0, 0]:
        return 'point'
    elif fingers_status == [1, 1, 1, 1, 1]:
        return 'open'
    elif fingers_status == [0, 1, 1, 0, 0]:
        return 'peace'
    elif fingers_status == [1, 1, 0, 0, 0]:
        return 'standby'


# 웹캠 설정
video = cv2.VideoCapture(0)

print("Webcam is running... Press 'ESC' to exit.")
while video.isOpened():
    ret, frame = video.read()
    if not ret:
        break

    frame = cv2.flip(frame, 1)
    img_rgb = cv2.cvtColor(frame, cv2.COLOR_BGR2RGB)
    result = hands.process(img_rgb)

    if result.multi_hand_landmarks:
        for hand_landmarks in result.multi_hand_landmarks:
            fingers_status = get_finger_status(hand_landmarks)
            print(recognize_gesture(fingers_status))

            # 손 랜드마크와 연결선 그리기
            mp_drawing.draw_landmarks(frame, hand_landmarks, mp_hands.HAND_CONNECTIONS)

    cv2.imshow('Hand Gesture', frame)
    if cv2.waitKey(1) == 27:  # ESC 키로 종료
        break

video.release()
cv2.destroyAllWindows()
```

#### 실행, VS Code

- `ctrl + shift + p` > Python:인터프리터 선택 > `Python 3.10.16(visual_module)` 선택
- 콘솔 창에서 `conda activate visual_module`
- `python hello_mediapipe.py` 또는 `F5`키로 실행

**실행화면** `화면 스티커로 가려놓음`
![](../../Z.%20Docs/img/Pasted%20image%2020250328174656.png)

---
#### Current Hand Poses

👌
✊
☝️
✋
✌️
👆
👍
🤙
🤟

---