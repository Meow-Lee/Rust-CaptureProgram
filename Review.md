# Rust Capture Program
## 개요
기존 캡쳐 프로그램을 대체하기 위한 프로그램으로 C++로 구성되어 있고, Rust WebAssembly를 사용하여 웹에서 DLL과 EXE을 호출할 수 있도록 구축하였습니다.

### 개발 및 테스트 환경
개발 언어 : C++, Rust  
개발 환경 : Windows 10  
테스트 환경 : Windows 10/11  

### 프로그램 아키텍쳐
프로그램 흐름도입니다.
처음 User가 HTML을 통해 버튼 클릭 등 이벤트를 발동시키면 Javascript의 Event Handler 함수를 통해 WebAssembly 모듈에 요청을 전송합니다.
WebAssembly 모듈은 WebSocket 통신을 통해 Rust 서버에 다시 그 요청을 전송하고, Rust에서 해당하는 요청을 수행 후 응답을 다시 WebAssembly 모듈로 전송합니다.
모듈은 받은 응답을 모듈 내 함수에서 Json 형태로 가공하여 Javascript 단에 전달하고, 결과값은 Javascript 단에서 HTML에 표시하는 방식으로 이루어져 있습니다.

추가로, WebAssembly 모듈은 수행시간을 단축해야 하는 함수들을 Javascript와 서로 상호작용할 수 있도록 모듈 내에 선언할 수 있습니다.

## 기능
### HTML / Javascript
1. 화면에서 사용자가 이벤트를 호출하고 서버와 연결 여부를 확인할 수 있도록 HTML을 활용해 구성요소를 넣어주었습니다.
2. WebAssembly 모듈에서 서버와의 연결 상태값을 받아와 Javascript를 활용해 사용자가 확인할 수 있도록 화면에 표시하였습니다.
3. 버튼 클릭 등 이벤트를 Javascript의 이벤트 핸들러를 통해 처리하였고, 핸들러 안에서 모듈로 요청을 전송하고 모듈을 통해 반환값을 받아 화면에 표시합니다.
4. 파라미터와 요청을 구분하기 위해 [ 함수명|@|(파라미터1, 파라미터2 ...) ] 와 같은 형식으로 처리하였습니다. EX) Capture|@|D:/TestFolder

### WebAssembly 모듈
1. Javascript에서 모듈 내 함수를 사용할 수 있도록 unsafe extern "C" {} 를 활용하였습니다.
2. 모듈 내에는, 특정 포트 웹소켓 통신을 할 수 있도록 연결 시도 및 onmessage, onopen, onclose, onerror 등의 바인딩 함수들을 작성하였습니다.
3. 서버에서 반환한 값을 모듈을 거쳐 Javascript 단으로 전송할 수 있습니다.

### Rust WebSocket
1. 함수 실행 결과값을 Json으로 반환하고 코드의 반복됨을 줄이기 위해 Json 응답 공통 구조를 작성하여 사용하였습니다.
```rust
// Json 공통 구조 작성
impl ApiResponse {
  fn new(code: bool, msg: String, data: Value) -> {
    let json_result: Value = json!({
      "code": code,
      "msg": msg,
      "data": data
    });

    return json_result.to_string()
  }
}

// 사용법
let mut code = bool::default();
let mut msg = String::new();
let mut data = Value::null;

...

return ApiResponse::new(code, msg, data)
```
2. WebSocket 연결 후에, 받은 문자열 요청을 split해서 실행되어야할 함수명과 이에 사용할 파라미터들을 변수에 담고 호출합니다.
3. 서버가 실행되면 Tray도 실행되게끔 설정하였습니다.
4. 캡쳐 프로그램의 EXE버전이나 DLL 버전 실행 시에는 Windows 라이브러리를 사용하였고 Rust에서 사용 시 unsafe{} 안에 사용하였습니다.
5. 
