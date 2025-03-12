
# Numpy

Row-major order = C-language style `Z 모양`
`np.reshape(array, (-1, n), order='C')`

Column-major order = Fortran-language style `N 모양`
`np.reshape(array, (-1, n), order='F')`

`.copy()`를 이용해 temp와 기존 배열을 완전히 detach해주자.

`arr[n]`: Row 부르기 (가로)
`arr[:, n]`: Column 부르기 (세로)

Slicing에서 :를 잘 이용하여 Matrix의 특정 부분에 잘 접근해보자.

`arr[None]`을 이용하면 첫 번째 차원이 새로 생긴다.
`arr[:, None]`을 이용하면 두 번째 차원이,
`arr[:, :, None]`을 이용하면 세 번째 차원이 새로 생긴다.
차원 증가의 과정은
> 일단 차원 늘리기 -> 동일한 차원의 다른 배열과 합체

Logical Array: 일반 array에서 특정한 기준으로 뽑아낼 수 있는 boolean형 array
이 Logical Array로 다음과 같이 타겟 배열의 인덱스를 특정할 수 있다.
`data[logical_array]`

강의자료 30p.와 31p.의 차이를 잘 알기

`np.meshgrid()`