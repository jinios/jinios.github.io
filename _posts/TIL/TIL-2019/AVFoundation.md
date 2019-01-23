
- QR code 리딩을 위해서는 미디어 데이터를 캡쳐하는 기능을 구현해야한다.
- 캡쳐 아키텍쳐는 크게 sessions, inputs, and outputs이라는 세 개의 세션으로 구분된다.
- inputs: 미디어의 소스, 카메라나 마이크같은 캡쳐 디바이스
- outputs: 인풋에서부터 획득한 유용한 데이터, 디스크의 영상 파일이나 raw픽셀 버퍼가 가능한 데이터

- AVFoundation: Work with audiovisual assets, control device cameras, process audio, and configure system audio interactions.
- 기본적으로 QR code 리딩을 위해서는 단말기의 카메라를 사용해야하기 때문에, 기본적으로 AVFoundation이 제공하는 클래스를 이용해서 세팅한다. 주로 사용되는 기능은 카메라를 사용해서 화면을 계속 읽어들이는 기능, 장면을 캡쳐하는 기능이 필요하고, 캡쳐한 데이터의 아웃풋을 핸들링하는 기능이 필요하다.
- 미디어 캡쳐를 위해서 필요한 세팅들
- AVCaptureSession: 캡쳐를 위해 해당 객체를 초기화하고 (such as AVCaptureDeviceInput), and outputs (such as AVCaptureMovieFileOutput)와 같은 속성들을 추가해줘야한다.
- AVCaptureMetadataOutput: 캡쳐 세션에서 생성된 메타데이터를 핸들링하는 클래스. AVCaptureMetadataOutput객체는 관련된 캡쳐 커넥션에서 발생된 메타데이터를 델리게이트 객체에 전달한다. 아주 심플하게는 이 아웃풋을 AVCaptureSession 객체에 add하여 사용할 수 있다.




### 참고링크
- [Cameras and Media Capture](https://developer.apple.com/documentation/avfoundation/cameras_and_media_capture)
