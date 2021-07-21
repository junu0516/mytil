# Base64 String으로 받은  pdf 파일을, 브라우저 화면에 띄워보기

> 회사 프로젝트를 수행하면서 pdf로 변환 가능한 base64 문자열을 다운로드없이 화면에서 바로 볼 수 있도록 한다.

![5 Online PDF To Base64 Converter Websites Free](https://cdn.ilovefreesoftware.com/wp-content/uploads/2018/04/pdf2base64-featured.png)



회사 자체 스크래핑 모듈을 통해 특정 사이트로부터 PDF파일을 hex string으로 전달받아 서버 로컬에 저장했다. 

문제는 이 hex string을 클라이언트가 요청할 때마다 서버로부터 받아와 웹브라우저에서 pdf 미리보기 방식으로 보여주는 것이었는데, 결과적으로 간단한 코드와 메소드 몇개로 끝났지만 나름 삽질을 많이 해서 이번 기회에 삽질 과정에서 배운 것들을 정리해보고자 한다.

---



### Base64

- 8비트의 이진데이터(pdf, zip 등의 여러 파일)를 ASCII 문자로만 이루어진 문자열로 바꾸는 인코딩 방식

  ```
  인코딩은 파일에 저장된 정보를 다른 형태로 변환 혹은 처리하는 방식으로 엄밀히는 '암호화'와 구분되야 함
  ```

- 64진법을 사용하는 이유는 2의 제곱수 기반 진법들 중 ASCII 문자를 사용하여 표현할 수 있는 제일 큰 진법이기 때문(ASCII 문자의 개수가 2^7 = 128개가 되지 않음) 

- 굳이 이렇게 인코딩을 하는 이유는 통신과정에서 안전하게 데이터를 전달하기 위한 것(특히 신뢰할 수 없는 통신의 경우 중요)

  - ASCII(일반적인 바이너리)의 경우 1비트를 처리하는 방식이 시스템별로 상이함
  - 일부 제어문자의 경우에도 시스템 별로 코드값이 서로 다른 경우가 존재함
  - 따라서 ASCII로 그냥 보내면 시스템별 차이로 인해 안전한 데이터의 전송을 담보하지 못하는 셈인 것

  

  ![img](https://blog.kakaocdn.net/dn/cUtiW8/btqC6PUQfRF/w8l5dQgbmWVLZD12MneZ90/img.png)

- 24비트의 버퍼를 생성하여 위부터 바이트(byte)를 넣고, 버퍼 위쪽부터 6비트 단위로 잘라서 Base64 테이블을 참고하여 ASCII 문자로 변환

  - __원본 -> ASCII Binary -> 6비트 단위로 잘린 형태 -> Base64로 인코딩된 형태__ 의 순서로 변환되는 것!

.

### Hexadecimal

- 16진법 기반의 인코딩 기법으로, 0~9와  A~F의 16개 문자를 대소문자 구분없이 사용(Base16)
- 이진법으로 표기된 4자리와 16진법 한자리가 1:1로 대응하며, 따라서 원본 데이터 바이트 수의 2배만큼 사이즈가 증가하게 됨(Base64의 경우 약 1.3333..배)
- 색상 코드(RGB 방식에서 #뒤에 붙는 6자리)에서 빨강,초록,파랑의 각 2자리를 16진법으로 표시(0~255)
  - 따라서 헥사로 나타낼 수 있는 색상의 총 개수는 16^6 가지가 되는 것

..

---



여기서부턴, Java(Springboot)와 Javascript를 통해 서버 로컬 경로로부터 가져온 pdf를 웹브라우저에 띄우는  과정을 살펴보도록 하자

.

### 1. hex 문자열로 된 pdf를 로컬 환경에 저장하기

```
굳이 hex 문자열로 받는 이유는 회사 스크래핑 모듈에서 제공하는 pdf 데이터가 hex이기 때문이다 ...ㅋㅋ
```

```java
import org.apache.commons.codec.binary.Hex;
import org.apache.commons.io.FileUtils;
import java.io.File;
import java.io.FileOutputStream;

....

public void parseHexToPdf(String hexString){ //파라미터로 헥사 문자열을 받았음
    //로컬 서버에 hex pdf로 변환해서 저장
    String folderPath = "디렉토리명";
    String filePath = folderPath+"\\"+"pdf 파일명"+".pdf";

    //디렉토리 생성
    File directory = new File(folderPath);
    if(!directory.exists()){
        directory.mkdirs();
    }
	
    //pdf 생성 후 디렉토리에 저장
    File pdfFile = new File(filePath);
    try(FileOutputStream fileOutputStream = new FileOutputStream(pdfFile)){
        byte[] decodedOutput = Hex.decodeHex(hex); //hex 데이터를 바이트스트림으로 복호화
        fileOutputStream.write(decodedOutput);
    }catch(Exception e){
        e.printStackTrace();
    }
    
}
```

- Java.io 패키지에서 제공하는 __`File`__ 클래스를 사용하여 파일 및 디렉토리를 다루고 있음

  - 다루고자 하는 파일 혹은 폴더의 __경로__ 에 대한 문자열을 파라미터로 전달하는 구조

  - 디렉토리가 존재하지 않을 경우에는 __`.mkdirs()`__ 로 디렉토리를 생성

  - 파일의 경우 디렉토리 하위에 파일이 존재하지 않을 경우에는 자동으로 생성됨

    - 동일한 파일명이 존재할 경우에는 덮어씀
    - 여기서는 새로운 File 객체 생성을 통해 아무 내용이 없는 pdfFile을 생성하였음

    

-  이후 만들어진 pdf 파일에 전달받은 hex 문자열을 바이트스트림으로 복호화해서 입력하는 것

  - __`org.apache.commons`__ 패키지에서 제공하는 __`Hex`__ 클래스를 사용하여 복호화
  - __`Hex`__ 사용법은 [공식문서](https://commons.apache.org/proper/commons-codec/apidocs/org/apache/commons/codec/binary/Hex.html) 를 참고하자
  - 이후 출력스트림 __`FileOutputStream`__ 을 통해 복호화된 문자열을 생성한 pdf 파일에 입력한 것
  - try-with-resource 의 구조로 선언할 경우 별도로 __`close()`__ 를 통해 자원 해제를 명시하지 않아도 됨

.

### 2.  pdf 요청시 저장해둔 pdf를 다시 Base64 문자열로 받아 응답하기



- 1번 과정을 정상적으로 수행했다면, 명시한 디렉토리에 pdf파일이 존재하는 것을 확인할 수 있을 것
- 이후 클라이언트가 pdf를 보기 위해 서버에 요청을 보내면, 서버는 저장된 pdf 파일을 Base64 문자열로 변환시켜 응답할 것

```java
import org.apache.commons.io.FileUtils;
import java.util.Base64;
import java.util.ArrayList;
import java.util.List;

public Map getPdfView(ArrayList<String> fileList) throws Exception {
    List<String> pdfList = new ArrayList<String>();

    for(String file : fileList){
        byte[] pdfBytes = FileUtils.readFileToByteArray(new File(file));
        String pdfString = Base64.getEncoder().encodeToString(pdfBytes);
        if(base64String.length()>=1){
            pdfList.add(pdfString);
        }
    }
    return resultMap;
}
```

- FileUtils와 File을 통해 읽어들일 pdf 파일이 저장된 경로를 명시하여 바이트 스트림을 생성(`byte[]`)

- 기본적으로 __`java.util`__ 에서 제공하는 __`Base64`__ 클래스를 통해 바이트 배열을 문자열로 변환

.

### 3. 자바스크립트에서 Base64 문자열을 전달받아 다시 웹브라우저에 pdf를 띄우기



- Spring 혹은 SpringBoot라면 ajax 요청에 대해 json으로 응답을 내보내는 식으로 해서 클라이언트쪽으로 변환해둔 Base64 문자열을 전달하게 될 것

- 이후 전달받은 문자열을 __`<iframe>`__ 태그를 통해 크롬 기준 새 탭에서 열었음

  ```javascript
  function showPdfOnBrowser(pdfList){
      //pdfList는 응답받은 Base64 문자열 리스트
      
      for(let i=0;i<pdfList.length;i++){
          let pdfData = fileList[i];
          let pdfWindow = window.open("");
          pdfWindow.document.write(
              "<iframe width='100%' height='100%' src='data:application/pdf;base64, "
              +encodeURI(pdfData)+"'></iframe>"
          );
      }
  }
  ```

  

---

#### 결론

사실 코드 자체는 몇 줄 안될 정도로 간단하지만, pdf.js 사용법을 뒤져보다 실패하기도 했고 __`<iframe>`__을 __`document.write()`__없이 그냥 만드려다가 크롬 정책 위반으로 에러가 나는 등 삽질을 계속 반복한 작업이었다.

하나씩 문제를 해결하면서 Base64와 Hex, 그리고 입출력스트림 사용 등의 내용을 새로 배우거나 복습할 수 있었던 소중한 기회였다고 생각한다.
