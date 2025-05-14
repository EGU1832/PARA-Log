#### 측정 기준 및 환경

Vision-module(서버) 측의 성능은 다음을 측정하여 합산 한 시간으로 평가되었다.
- 제스처 인식 `/analyze, ms`
- Firebase 통신 `/send_gesture, ms`

$$L_{\text{total}} = L_{\text{analyze}} + L_{send\_gesture}$$

`/analyze` 구간은 Mediapipe를 이용한 손 랜드마크 추출과 손가락 상태 분석, 제스처 문자열 생성까지의 과정을 포함한다.
`/send_gesture` 구간은 해당 제스처를 Firebase Realtime Database로 전송하는 과정을 포함한다.

**측정 환경**은 다음과 같다. `System-control 측과 동일한 환경에서 측정하였다.`
- 인텔 i5 CPU 탑재 노트북
- Wifi RSSI: `[-40, -50]`

#### 측정 결과

| 구간명             | 평균 지연 시간 (ms) |
| --------------- | ------------- |
| `/analyze`      | 56.99         |
| `/send_gesture` | 165.50        |
| 전체 응답 시간 (추정)   | 약 222.49      |

측정 결과 Vision-module 측은 제스처 인식 단계에선 평균 56.99ms의 짧은 지연 시간을 보이며 실시간 제어에 적합한 성능을 보였으나,
Firebase 기반의 클라우드 통신에서는 평균 160.50ms의 상대적으로 높은 지연시간이 발생하였다.