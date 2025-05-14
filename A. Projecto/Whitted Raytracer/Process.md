
#### 2025.03.21

오늘은 **OpenCV**를 이용해 `.ppm`파일을 실행 후 바로 윈도우 화면에 띄울 수 있도록 해보려고 한다.
OpenCV는 공식 사이트 > Library > Release에서 윈도우 버전으로 다운로드 받았다.
[Release - OpenCV](https://opencv.org/releases/)
중간에 Edge 보안 기능때문에 다운이 안 되길래 유지 > 그래도 유지로 마저 다운로드 해주었다.

다운로드 후 프로젝트와 연결하는 것은 다음 사이트 참고
[Visual Studio에서 OpenCV 설치하기](https://bigmacgood.tistory.com/3)

##### Error & Solve

bin 파일과 lib파일이 각각 Release용, Debugging용으로 두 개가 있는데 프로젝트 속성을 설정할 때, 그리고 프로젝트 빌드 시 `.exe` 폴더에 `.dll` 파일을 집어넣을 때 주의해야 한다.
- 프로젝트 속성: Debug, Release 모드를 따로 설정해야 한다. Debug는 `opencv_world4100d.lib`를, Release는 `opencv_world4110.lib`를 링커 > 입력 > 추가종속성에 추가한다.
- 프로젝트 빌드: 디버깅 모드에서는 `opencv_world4110d.dll`를 `.exe`파일이 있는 폴더에 추가하여야 하고 릴리즈 모드로 디버깅할 때는 `opencv_world4110.dll`을 Release 폴더에 추가하여야 한다.

참고 사이트는 다음과 같다.
[c++ - opencv 2.3.* imread not working - Stack Overflow](https://stackoverflow.com/questions/7817704/opencv-2-3-imread-not-working)
[c++ - OpenCV imread(filename) fails in debug mode when using release libraries - Stack Overflow](https://stackoverflow.com/questions/9125817/opencv-imreadfilename-fails-in-debug-mode-when-using-release-libraries)

![](../../Z.%20Docs/img/Pasted%20image%2020250321120540.png)
`Debug 모드`

> 아직 코드가 엉망이라 의미를 모르겠는 이미지가 출력되지만 어쨌든 디버깅 모드에서 `result.ppm`이 OpenCV를 통해 잘 출력되는 것을 확인할 수 있다.

문제가... Debug 모드랑 Release 모드에서 이미지 출력 결과가 다르다.
![](../../Z.%20Docs/img/Pasted%20image%2020250321123247.png)
`Release 모드`

근데 이건 아마도... OpenCV 문제가 아니라 내 코드가 문제 같다.

---
#### 2025.03.22

오늘은 해당 프로젝트를 github remote repository에 연결했다.
`.gitignore`은 Visual Studio용 ignore을 썼다.
github를 내가 이렇게 몰랐구나.. 반성하게되는 과정이었다.

---
