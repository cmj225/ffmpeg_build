# FFmpeg Build

ffmpeg을 다양한 플랫폼 환경에서 쉽게 빌드하기 위해 정리한 프로젝트

***
## For Windows Builds
### MinGW, Msys, Git, Yasm, 
* **install <a href="http://mingw-w64.org/doku.php/download/mingw-build">MinGW-w64-build**</a>
  * Mingw-w64가 Mingw "mainline"보다 최신 버전으로 관리가 되고 있고, library compatibility가 좋아 공홈에서 MinGW-w64를 선호한다고 함
  * 설치 시 목적 Architecture 선택 및 Threads를 ``win32``로 변경
    * i686 : 32bit binary / x86_64 : 32bit memory map을 가지는 64bit binary
    * posix 선택 시, C++11/C11 multithreading feature 사용 가능, libwinpthreads.dll 필요
    * win32 선택 시, 향후 C++11/C11 multithreading feature(std::thread)에 대한 구현이 불확실하여, 해당 기능을 이용한 코드 컴파일 시 문제가 있을 수 있음 (To Do: 확인 필요)
    * 우선 빌드된 dll을 linking하여 구현할 프로젝트를 MinGW에서 컴파일할 계획이 없으므로, 배포하기 편리한 win32 thread로 선택

* **install MSYS**
  * MSYS2, Cygwin은 dll 사용 시 별도의 추가 dll
    * cygwin.dll, msys-2.0.dll을 필요로 하므로 MSYS를 이용
  * download <a href="https://sourceforge.net/projects/mingw/files/MSYS/Base/msys-core/msys-1.0.11/MSYS-1.0.11.exe/download?use_mirror=jaist">msys</a>
    * msys 내부에 /mingw directory에 etc/fstab에 기록된 directory를 mount
    * /가 msys.bat이 있는 위치(c:/msys/1.0)로 파일시스템이 분리됨
    * msys 설치과정에서 mingw가 설치된 위치를 잡아주거나, 혹은 설치 후 mingw 위치로 위에서 설치한 mingw를 복사
  * download <a href="https://sourceforge.net/projects/mingw/files/Other/Unsupported/MSYS/msysDTK/msysDTK-1.0.1/msysDTK-1.0.1.exe/download?use_mirror=jaist">developer kit</a>
    * msys와 동일 경로로 설치
  * ~~제일 편한 방법은 mingw-get을 이용해 mingw 및 개발자 툴킷들이 내장된 msys를 다운받는 것으로 보임 - installer에 mingw64(64bit gcc)는 없는 것으로 보임~~

* **install <a href="https://gitforwindows.org/">Git for Windows</a>**
  * project git clone을 위해 필요

* **install <a href="https://yasm.tortall.net/Download.html">yasm</a>**
* **install <a href="http://gnuwin32.sourceforge.net/packages/coreutils.htm">coreutil-bin, coreutil-dep</a>**
* **install <a href="https://download.gnome.org/binaries/win32">pkg-config, glib, gettext-runtime**</a>
* 위의 tool들을 모두 설치하고, path setting을 해줄 것


* **참고 문헌**
  * <a href="https://trac.ffmpeg.org/wiki/CompilationGuide">FFmpeg/CompliationGuide/</a>
  * <a href="https://trac.ffmpeg.org/wiki/CompilationGuide/MinGW">FFmpeg/CompliationGuide/MinGW</a>
  * <a href="http://www.mingw.org/wiki/msys">MinGW & MSYS</a>

### packaging
 * <a href="https://drive.google.com/file/d/1y0Y-LGv0OoOLG8hS-P-_I3T2eb8yswtS/view?usp=sharing">빌드환경</a>
 * packaging해둔 msys 압축파일을 [c:/]에 압축해제, ``[c:/msys]``에 위치하도록 함
 * script의 ffmpeg configure scriptor에서 configure를 상황에 맞게 수정
 * [c:/msys/1.0]에서 원하는 architecture에 맞춰 ``source [.x86_init|.x86_64_init]`` 실행
   * 해당 커맨드는 아래와 같은 일들을 함
   * ``mingw compilter path setting`` : target arch에 맞춰 ``/mingw``에 mount할 directory를 ``etc/fstab``을 변경
   * ``PATH setting [PATH/PKG_CONFIG_PATH]``
   * ffmpeg git clone & checkout & configure & make install

### output archive
* <a href="https://drive.google.com/file/d/16slDl_9gDGCCj6Vs5SkizXH-t1CBm_XL/view?usp=sharing">windows ffmpeg build output</a>
  * ``LGPL``
  * ``[x86|x86_64] / [debug/release] / [default|default+sdl+openh264]``

***
## For Linux Builds


***
## common build stage
* ``git clone https://git.ffmpeg.org/ffmpeg.git``
* ``git checkout -t origin/release/4.1``
* ``./configure``
  * 미리 만들어 둔 .sh 파일을 이용하여 쉽게 진행할 수 있다.
* ``make`` ``make install``

***

## external library add
* build하여 /external/[32/64] 맞는 위치에 넣어주어야 함
  * pkgconfig .pc파일의 prefix 경로는 절대경로를 넣어주어야 함

* sdl2
  * .pc path만 잘 맞춰주면 문제없이 진행

* openh264
  * windows x86은 큰 문제없이 빌드되는 것을 확인
  * windows x86_64의 경우, x86_64-w64-mingw32-gcc-ar.exe를 복사하여 x86_64-w64-mingw32-ar.exe를 만들어주어야 함

***

## 기타 이슈

* sed
  * 해당 tool이 설치되지 않을 경우, afilter 등의 일부 필터가 스크립팅되지 않아 정상적으로 빌드과정에 포함되지 않는 문제가 있어 ffplay로 재생 시 sound를 재생하지 못함.

* make 실행 시, crlf/lf 충돌로 문제가 발생하는 상황이 있을 수 있음
  * git config --global core.autocrlf false