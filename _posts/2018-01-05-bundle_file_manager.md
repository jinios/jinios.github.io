
### File System(Foundation>File System>FileManager)
> File System : Create, read, write, and examine files and folders in the file system.[(Go to  Official developer.apple)](https://developer.apple.com/documentation/foundation/filemanager)

An FileManager object lets you examine the contents of the file system and make changes to it. The FileManager class provides convenient access to a shared file manager object that is suitable for most types of file-related manipulations. A file manager object is typically your primary mode of interaction with the file system. You use it to locate, create, copy, and move files and directories. You also use it to get information about a file or directory or change some of its attributes.

### Bundle(Foundation>Resources>Bundle)
> Resources : A representation of the code and resources stored in a bundle directory on disk. [(Go to Official developer.apple)](https://developer.apple.com/documentation/foundation/bundle)

Any executable can use a bundle object to locate resources, either inside an app’s bundle or in a known bundle located elsewhere. <br/>
You don't use a bundle object to locate files in a container directory or in other parts of the file system. (컨테이너 경로- 그냥 담아두는 경로?나 파일시스템의 다른 부분에서 사용되진 않는다. - 같은 앱, 같은 번들 내에서만 읽고쓰기할때만 사용가능?
 : 파일시스템의 다른부분 = 다른 부분, 같은 번들 내에서만 읽고쓰기할때만 사용가능하다는 말!iOS는 App단위로만 접근이 가능하도록 제한되어있음)

번들? 파일매니저 하위 개념처럼 자체적으론 어떤 것에 의해서 관리당하는개념인건가

번들 : 앱 안에서 필요한 리소스들. (UI를 위한 이미지라든가 폰트..?) 그렇다면 InputView에서 쓸때 JSON프로그램이 앱(번들)으로 인식돼서 쓰였던건가
파일 : ...?? 그냥 general 한 파일들..?
그러면 JSON에서는 그냥 파일매니저를 쓰는게 맞는것같다.
