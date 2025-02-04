![](./media/image1.png)

# 고급 광원 샘플

*이 샘플은 Microsoft 게임 개발 키트(2022년 3월)와 호환됩니다.*

# 설명

이 샘플에서는 장면에서 많은 수의 광원을 처리할 때 렌더링 성능을 향상시키는 다양한 기술을 보여 줍니다.

![](./media/image2.png)

# 샘플 빌드

Xbox One 개발 키트를 사용하는 경우 활성 솔루션 플랫폼을 `Gaming.Xbox.XboxOne.x64`(으)로 설정합니다.

Xbox Series X|S를 사용하는 경우 활성 솔루션 플랫폼을 `Gaming.Xbox.Scarlett.x64`(으)로 설정합니다.

PC에서 실행 중인 경우 활성 솔루션 플랫폼을 `Gaming.Xbox.Desktop.x64`(으)로 설정합니다.

*자세한 내용은* *GDK 설명서의* __샘플 실행을__ 참조하세요.&nbsp;

# 컨트롤

| 동작 | Gamepad |
|---|---|
| 기술 간 전환 | A/B |
| 광원의 반지름 수정 | 오른쪽/왼쪽 DPad |
| 광원 토글 | LB |
| 광원 렌더링 출처 토글(색이 지정된 입자) | RB |
| 카메라 주변 모드 | 위, 아래로 이동하려면 LT-RT 보기를 회전하려면 오른쪽 스틱 번역을 이동하려면 왼쪽 스틱 |
| 샘플을 종료합니다. | 보기 버튼 |

# 구현 참고 사항

이 샘플에서는 다음과 같은 렌더링 기술을 보여 줍니다.

- 지연 렌더링

- 광원 볼륨

- 타일형 지연

- 클러스터형 지연

이 샘플에서는 GPU에 걸리는 시간 및 전체 프레임에 걸린 시간의 타이머를 보여 줌으로써 이러한 기술을 서로 비교하는 방법을 보여 줍니다.

광원 볼륨의 경우 알고리즘은 광원의 세계 위치와 반경을 사용하여 구 메시를 렌더링하여 장면에 배치하고 크기를 조정합니다. 이렇게 하면 구에 의해 래스터화된 픽셀의 특정 광원에 대해서만 작업합니다. 물론 위치 정보, 광원 감쇠 및 기하 도형 버퍼에서 제공하는 장면 정보를 사용하여 해당 픽셀이 광원의 영향을 받거나 영향을 받지 않는지 확인해야 합니다.

광원의 볼륨 단점은 래스터화할 때 여러 구가 겹칠 때 발생합니다. 동일한 픽셀에 대해 여러 그리기 호출을 호출하게 되므로 발생합니다.

타일형 지연은 이 문제에 대한 해결 방법입니다. 구를 렌더링하는 대신 화면을 타일로 나눈 다음 광원을 여기에 버리도록 하여 각 타일에 영향을 주는 모든 광원 목록이 포함됩니다. 그런 다음 타일당 스레드 그룹을 할당하고 광원이 없는 타일을 건너뛰고 모든 광원 계산을 수행하는 컴퓨팅 셰이더를 디스패치할 수 있습니다.

타일형 지연은 광원 볼륨에 비해 개선되지만 여전히 몇 가지 문제가 있습니다. 하나는 타일에 영향을 주는 광원을 보고 있기 때문에 해당 타일의 장면 요소에 영향을 주지 않는 광원에서 시간을 낭비할 수 있다는 것입니다(instance 경우 타일에서 가장 먼 요소보다 더 멀리 있을 수 있습니다). 이 문제는 깊이 범위를 정의하여 해결할 수 있으므로 장면의 최소 깊이와 최대 깊이 사이에 있는 광원만 고려하면 됩니다. 그러나 또 다른 문제는 깊이 불연속성입니다. 동일한 타일에 깊이가 서로 다른 두 개의 개체가 있는 경우 장면의 요소에 영향을 주지 않더라도 둘 사이의 모든 광원이 포함됩니다.

이 문제를 해결하기 위해 제시된 세 번째 기술은 클러스터형 지연입니다. 기본적으로는 타일형 지원과 원리가 같지만 화면을 타일(2D)로 분할하는 대신 절두체를 셀(3D 클러스터)로 분할합니다. 이렇게 하여 클러스터에 광원을 처리하면 장면에서 아무 영향을 주지 않지만 깊이 범위 내에는 존재하는 광원에 대한 처리 시간을 아낄 수 있습니다.

# 참고

- 현재 광원 볼륨은 절두체 컬링되지 않습니다.

- 광원은 현재 샘플이므로 다른 광원의 영향을 받지 않습니다.

- 타일 하나에 허용된 최대 광원 개수인 512개를 초과할 경우 표시되는 아티팩트가 있습니다. 반경이 충분히 팽창하면 발생합니다.

# 업데이트 기록

2022년 9월 26일 -- 이식된 샘플.

# 개인정보처리방침

샘플을 컴파일하고 실행하는 경우 샘플 사용량을 추적할 수 있도록 샘플 실행 파일의 파일 이름이 Microsoft에 전송됩니다. 이 데이터 수집을 옵트아웃하려면 Main.cpp에서 "샘플 사용량 원격 분석"이라는 레이블이 지정된 코드 블록을 제거할 수 있습니다.

일반적인 Microsoft의 개인 정보 정책에 대한 자세한 내용은 [Microsoft 개인정보처리방침](https://privacy.microsoft.com/en-us/privacystatement/)을 참조하세요.


