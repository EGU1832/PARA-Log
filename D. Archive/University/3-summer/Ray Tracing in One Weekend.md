
Ray Tracing in One Weekend로 공부한 것을 정리한 것이다.
나의 최종 목표는 다음 영상에서 나온 것과 비슷한 수준의 Ray Tracer를 구축하는 것이다.
[Coding Adventure: Ray Tracing (youtube.com)](https://www.youtube.com/watch?v=Qz0KTGYJtUk)

# 1. Overview

Ray Tracer를 만드는 것은 쉬운 것이 아니다. 하지만 여기선 일단 만들어보는 것이 목적이므로
최적화, 객체 모양의 다양성, 여러가지 질감 등을 모두 무시한다고 생각하면 된다.
우리가 만들 Ray Tracer를 간략히 설명하면 다음과 같다.
- **사용 언어**:: C++
- **Ray Tracing 방식**: Path Tracing
- **객체 형태**: Sphere
따라서 전체적으로 아주 단순한 Ray Tracer를 만든다고 생각하면 된다.

#### 작업 환경 세팅
> `Visual Studio 2022`를 사용하여 `InOneWeekend.sln` 프로젝트 생성

전체적인 방식은 다음과 같다.
- Ray Tracer 코드를 짜 `.ppm` 포멧의 Pixmap으로 렌더링
- `imageMagick`을 이용하여 `.ppm`을 `.jpg`로 변환
- 최종 결과 이미지 확인

완성본은 다음 Github를 확인하면 된다.
[GitHub - RayTracing/raytracing.github.io: Main Web Site (Online Books)](https://github.com/RayTracing/raytracing.github.io)

# 2. Output an Image

#### imageMagick
> 디지털 이미지를 편집하고 조작하는 데 사용되는 무료 오픈 소스 소프트웨어

[ImageMagick](https://imagemagick.org/script/download.php)에서 Window 기준 `.exe` 파일을 받아 설치 후
프로젝트 cmd 창에서 다음 명령어를 입력한다.
```
> magick "input.ppm" "output.ppm"
```

#### `Problem & Solve`
Problem:
Windows에서 발생 가능한 문제로, `.ppm` 파일의 인코딩 방식이 `UTF-16`일 경우 magick을 사용하였을 때 다음과 같이 에러 메세지가 뜬다.
```
> magick.exe: impproper image header 'image.ppm' @ error/pnm.c/ReadPMMImage/343.
```
Solve:
`.ppm` 파일 생성 시, 옵션을 추가하여 인코딩 방식을 지정해준다.
```
> /Debug/InOneWeekend.exe | set-content output.ppm -encoding String
```

