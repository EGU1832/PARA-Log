# BVH(Boundary Volume Hierarchy)

#### What is BVH?
> Tree Structure on a set of Geometric objects
> 기하 객체의 묶음으로 이뤄진 트리 구조

BVH를 그림으로 간략히 나타내면 다음과 같다.
![500](https://upload.wikimedia.org/wikipedia/commons/thumb/2/2a/Example_of_bounding_volume_hierarchy.svg/750px-Example_of_bounding_volume_hierarchy.svg.png)

BVH에 대해서 좀 더 잘 이해하기 위해 먼저 Spatial Data Structure란 무엇인지 알아보자.

#### Spatial Data Stucture
> 기하 객체를 3차원 공간 안에서 구조화 한 것

컴퓨터 그래픽스에서 예를 들어 Ray Tracing을 구현한다고 생각해보자.
그렇다면 카메라(눈)에서 Ray를 쏘고 충돌하는지 여부부터 검사해야 할 것이다.
그런데 공간이 크면 이를 하나하나 다 검사하다간 연산량이 끝도 없이 늘어난다.
그래서 고안 된 것이 Spatial Data Structure인 것이다.

**Hierarchy**
Spatial Data Stucture는 일반적으로 **계층구조**에 기반하여 구현된다.
계층 구조는 부모/자식 노드로 구성되며 각 노드는 자신의 공간을 정의한다.
이런 구조를 이용해서 기하 개체를 구성하는 각각의 요소에 빠르게 접근할 수 있다.

**Kind**
- BVH: 기하 개체를 감싸는 박스를 만들며 **기하 개체** 분할
- BSP Tree: **공간**을 **불균등**하게 분할
- Octree: **공간**을 **균등**하게 분할

따라 BVH는 BSP Tree나 Octree와 다르게 공간이 겹치는 부분이 생길 수 있다.
BSP Tree와 Octree의 모든 리프 노드를 더하면 겹치는 부분 없이 공간 전체가 된다.

이제 대충 BVH의 특징이 뇌에 들어올 것이다.
본격적으로 BVH에 대해서 알아보자.

#### Components
BVH를 구성하는 노드는 다음 세 가지로 나눠볼 수 있다.
- **Root Node**: 최상단 노드
- **Internal Node**: 자식 노드를 가진 노드
- **Leaf Node**: 최하단 노드, 실질적으로 렌더링 될 기하요소가 이에 해당된다.

부모 노드가 만약 Ray와 교차한다면 자식 노드를 검사하고, 이를 반복하는 재귀적 방식으로 연산량을 줄일 수 있다.

이렇게 Real-time Rendering에서 기본적으로 쓰이는 BVh