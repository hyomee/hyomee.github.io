# 읽기

파일을 읽기 위해서는 다음 순서로 코드를 작성해야 합니다.

1. **파일 객체 생성**: - `File file = new File("filename.txt");`
2. **파일 입력 스트림 생성**: - `FileInputStream inputStream = new FileInputStream(file);`
3. **버퍼 생성**: - `byte[] buffer = new byte[1024];`
4. **입력 스트림으로부터 데이터를 버퍼에 읽어옴**: - `inputStream.read(buffer);`
5. **버퍼에서 읽어온 데이터를 문자열로 변환**: - `String data = new String(buffer);`
6. **입력 스트림 닫기**: -`inputStream.close();`

## 1. Byte 단위로 읽기 <a href="#id-1.-byte" id="id-1.-byte"></a>

InputStream은 Byte 단위로 읽어 들이는 것으로 InputStream 확장해서 만든 FileInputStream 클래스를 사용하여 파일에서 데이터를 읽게 합니다.

예제 1 : Byte 단위로 읽기

```java
FileInputStream fileInputStream = null;
try {
  fileInputStream =  new FileInputStream(fileName) ;
  int readChar = 0;
  while ((readChar = fileInputStream.read()) != -1) {
    System.out.println((char)readChar);
  }
} catch (IOException e) {
  System.out.println("파일을 확인해 주세요");
  throw new RuntimeException(e);
} finally {
  if (fileInputStream != null  ) {
    try {
      fileInputStream.close();
    } catch (IOException e) {
      throw new RuntimeException(e);
    }
  }
}
```

* 3 line : 파일 객체 생성 합니다.
  * FileInputStream 생성자의 인수로 파일을 넘겨주면 내부에서 File를 객체를 생성합니다.
  *   파일 읽기의 1, 2번 사항 입니다.

      Copy

      ```
      public FileInputStream(String name) 
                      throws FileNotFoundException {
          this(name != null ? new File(name) : null);
      }
      ```
* 4 line : read() 메서드는 Data를 한 Byte씩 읽어서 값이 없으면 -1로 돌려 주는 메서드로 파일을 읽어 마지막 인지 체크하기 위해 선언한 변수로 초기값 0으로 설정합니다.
* 5 line : Data를 한 Byte씩 읽어서 없으면 while문을 벗어납니다.
* 6 line : 읽은 문자는 Char로 변환 해서 출력합니다.

<figure><img src="https://hyomee.gitbook.io/~gitbook/image?url=https%3A%2F%2F1889142648-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FVfLaXPTe0t3ptNpFbmH5%252Fuploads%252FkOQp8unhbkdfzQzZH0ts%252Fimage.png%3Falt%3Dmedia%26token%3Dc8759a95-95cd-4852-bf19-16f0d32a8f54&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=5a85a63b3cf600c728211772b92dd14870c8d53c0c101db6953f720be1a28bff" alt=""><figcaption></figcaption></figure>

* 8 - 11 line : File에 관한 예외 처리 로직
* 12 - 17 line : File을 열어서 작업을 했으므로 열려 있는 파일을 닫아야 합니다.
* **예제 1의 문제점**

한글 파일인 경우 **한글이 깨지는 문제** 입니다. 해당 문제를 해결하기 위해서는 문자셋을 지정해야 하므로 InputStreamReader 클래스를 사용하여 FileInputStream객체에서 읽어들인 데이터를 문자셋을 변환합니다.

Copy

```java
 InputStreamReader inputStreamReader = null;
  try {
    inputStreamReader = new InputStreamReader(
             new FileInputStream(fileName),
            "UTF-8");
    int readChar = 0;
    while ((readChar = inputStreamReader.read()) != -1) {
      System.out.println((char)readChar);
    }
  } catch ....
```

*   3 line : InputStreamReader 메서드 시그니처로의 두번째 파라메터에 문자셋을 지정 하여 읽어야 합니다.

    Copy

    ```java
     public InputStreamReader(InputStream in, String charsetName)
                    throws UnsupportedEncodingException {
    ```

    * 파일의 문자셋에 맞는 문자셋를 지정 합니다. ( "UTF-8", "EUC-KR" )

<details>

<summary>Byte 단위로 읽기 (문자셋 설정) - 전체 소스</summary>

```java
// charsetName : "UTF-8"으로 넘김
public static void CharReadFile(String fileName, 
                                String charsetName) {
    InputStreamReader inputStreamReader = null;
    try {
      inputStreamReader = new InputStreamReader(
               new FileInputStream(fileName),
              charsetName);
      int readChar = 0;
      while ((readChar = inputStreamReader.read()) != -1) {
        System.out.println((char)readChar);
      }
    } catch (UnsupportedEncodingException e) {
      System.out.println("엔코딩에 문제가 있습니다.");
      throw new RuntimeException(e);
    } catch (IOException e) {
      System.out.println("파일을 확인해 주세요");
      throw new RuntimeException(e);
    } finally {
      if (inputStreamReader != null  ) {
        try {
          inputStreamReader.close();
        } catch (IOException e) {
          throw new RuntimeException(e);
        }
      }
    }
  }
}
```

</details>

## 2. Line 단위로 읽기 <a href="#id-2.-line" id="id-2.-line"></a>

line 단위로 읽기 위해서는 reader 객체나 자바 8 이상에서는 Files 클래스를 이용하는 방법이 있습니다.

### 2-1. InputStream / Reader 클래스 사용 <a href="#id-2-1.-inputstream-reader" id="id-2-1.-inputstream-reader"></a>

Reader 클래스를 상속 받은 BufferedReader를 Byte로 읽어 들이는 InputStream 클래스와 같이 사용해서 Line 단위로 읽게할 수 있습니다.

```java
BufferedReader  bufferedReader  = null;
try {
   bufferedReader = new BufferedReader(
      new InputStreamReader(
              new FileInputStream(fileName),
              "UTF-8"));
   String readLine;
   while ((readLine = bufferedReader.readLine()) != null) {
       System.out.println(readLine);
   } 
} catch ( .....
```

* 3 - 6 line : Byte 단위로 읽어 들이는 FileInputStream 클래스에 InputStreamReader 클래스를 이용하여 문자셋으로 적용하고 읽어 들인 것을 BufferReader를 사용하여 Line단위로 읽을수 있게 합니다.
* 7 - 9 line : bufferedReader.readLine()은 읽을 데이터가 없으면 null로 반환하고 있으면 라인의 데이터를 돌려 주는 메서드여서 readLine 변수를 사용하고 데이터가 없으면 while문을 탈출 합니다.

<details>

<summary>Line 단위로 읽기 - 전체 소스</summary>

```java
public static void LineReadFile(String fileName, String charsetName) {
    BufferedReader  bufferedReader  = null;
    try {
      bufferedReader = new BufferedReader(
              new InputStreamReader(
                      new FileInputStream(fileName),
                      charsetName));
      String readLine;
      while ((readLine = bufferedReader.readLine()) != null) {
        System.out.println(readLine);
      }
    } catch (UnsupportedEncodingException e) {
      System.out.println("엔코딩에 문제가 있습니다.");
      throw new RuntimeException(e);
    } catch (IOException e) {
      System.out.println("파일을 확인해 주세요");
      throw new RuntimeException(e);
    } finally {
      if (bufferedReader != null  ) {
        try {
          bufferedReader.close();
        } catch (IOException e) {
          throw new RuntimeException(e);
        }
      }
    }
  }
```

</details>

## 3. Line 단위로 읽기 문자셋 변경 <a href="#id-3.-line" id="id-3.-line"></a>

### 3-1. InputStream / Reader 클래스 사용 <a href="#id-3-1.-inputstream-reader" id="id-3-1.-inputstream-reader"></a>

2-2. Line 단위로 읽기 코드에서 라인 단위로 읽은 데이터를 getBytes() 메서드를 사용해서 문자셋 변경을 하고 문자열로 바꾸면 됩니다.

```java
while ((readLine = bufferedReader.readLine()) != null) {
  byte[] utf8Bytes = readLine.getBytes("UTF-8");
  String utf8String = new String(utf8Bytes, "UTF-8");
  System.out.println(utf8String);
}
```

* 2 line : getBytes를 사용해서 문자셋 변환("UTF-8", "EUC-KR") 합니다.
* 3 line : System.out으로 출력하기 위해 문자열로 변환합니다.

<details>

<summary>Line 단위로 읽기 문자셋 변경 - 전체 소스</summary>

```java
public static void ConvertLineReadFile(String fileName, 
                      String fromCharset , 
                      String toCharset) {
    BufferedReader  bufferedReader  = null;
    try {
      bufferedReader = new BufferedReader(
              new InputStreamReader(
                      new FileInputStream(fileName),
                      fromCharset));
      String readLine;
      while ((readLine = bufferedReader.readLine()) != null) {
        byte[] utf8Bytes = readLine.getBytes(toCharset);
        String utf8String = new String(utf8Bytes, toCharset);
        System.out.println(utf8String);
      }
    } catch (UnsupportedEncodingException e) {
      System.out.println("엔코딩에 문제가 있습니다.");
      throw new RuntimeException(e);
    } catch (IOException e) {
      System.out.println("파일을 확인해 주세요");
      throw new RuntimeException(e);
    } finally {
      if (bufferedReader != null  ) {
        try {
          bufferedReader.close();
        } catch (IOException e) {
          throw new RuntimeException(e);
        }
      }
    }
}
```

</details>

### 3-2. FileReader / BufferedReader 클래스 사용 <a href="#id-3-2.-filereader-bufferedreader" id="id-3-2.-filereader-bufferedreader"></a>

InputStream 클래스를 FileReader 클래스로 변경을 하면 됩니다. FileReader 샹성자로 두번째 파라메터가 Charset 타입을 전달 해야하며 내부적으로는 FileInputStream를 사용합니다. 다음은 FileReader 클래스생성자 입니다.

```java
public FileReader(String fileName, Charset charset) throws IOException {
    super(new FileInputStream(fileName), charset);
}

// super 로 Charset 을 전달 하기 위해서는 Charset.forName(fromCharset)을 사용해야 한다.
public InputStreamReader(InputStream in, Charset cs) {
    super(in);
    if (cs == null)
        throw new NullPointerException("charset");
    sd = StreamDecoder.forInputStreamReader(in, this, cs);
}
```

* FileReader / BufferedReader 클래스 사용 소스

```java
BufferedReader  bufferedReader  = null;
try {
  bufferedReader = new BufferedReader(
          new FileReader(fileName, Charset.forName("UTF-8")) );
  String readLine;
  while ((readLine = bufferedReader.readLine()) != null) {
    byte[] utf8Bytes = readLine.getBytes(toCharset);
    String utf8String = new String(utf8Bytes, toCharset);
    System.out.println(utf8String);
  }
} catch ( ..... 
```

* 3 - 4 line : InputStream 클래스 대신 FileReader 클래스로 변경하고 Charset.forName() 메서드를 사용하여 Charset 타입으로 변경합니다.
* 나머지는 2-2-1과 동일 합니다.

<details>

<summary>Line 단위로 읽기 문자셋 변경 (FileReader / BufferedReader 클래스 사용) - 전체 소스</summary>

```java
public static void ConvertLineReaderFile(String fileName, 
                String fromCharset , 
                String toCharset) {
    BufferedReader  bufferedReader  = null;
    try {
      bufferedReader = new BufferedReader(
              new FileReader(fileName, Charset.forName(fromCharset)) );
      String readLine;
      while ((readLine = bufferedReader.readLine()) != null) {
        byte[] utf8Bytes = readLine.getBytes(toCharset);
        String utf8String = new String(utf8Bytes, toCharset);
        System.out.println(utf8String);
      }
    } catch (UnsupportedEncodingException e) {
      System.out.println("엔코딩에 문제가 있습니다.");
      throw new RuntimeException(e);
    } catch (IOException e) {
      System.out.println("파일을 확인해 주세요");
      throw new RuntimeException(e);
    } finally {
      if (bufferedReader != null  ) {
        try {
          bufferedReader.close();
        } catch (IOException e) {
          throw new RuntimeException(e);
        }
      }
    }
  }
```

</details>

### 4. CSV 파일 읽기 <a href="#id-4.-csv" id="id-4.-csv"></a>

CSV 파일은 쉼표(",")로 구분된 파일로 라인 단위로 읽고 쉼표(",") 하면 쉽게 만들수 있습니다.

CSV 파일을 관련 부분은 다음과 같습니다.

```java
// 라인 단위 읽기
row = convertCharSet(br, frpVO.getToCharset());
      
while (row != null) {
    // CSV 헤더 포함 여부
    if (frpVO.isHeader()) {
      // 반환 값 List에 저장
      results.add(getCols( frpVO.getDelimiter(),  row));
    } else {
      frpVO.setHeader(true);
    }

    // 다음 라인 단위 읽기
    row = convertCharSet(br, frpVO.getToCharset());

}

// CSV 파일과 같이 구분자가 있는 것에 대한 처리
private static <T> T getCols(String delimiter, String row) {
    if (delimiter != null) {
      String[] columns = row.split(delimiter);
      // TODO 이관에서 비동기 처리 저장 System.out.println("String[] :" + columns);
      return (T) columns;
    } else {
      // TODO 이관에서 비동기 처리 저장
      System.out.println("String :" + row);
    }
    return (T) row;
}
```

* 6- 11 line : 해더 포함 이면 결과에 해더 정보를 추가하고 해더 미포함 이면 해더 포함으로 설정을 변경합니다. 그래야 14 line 에서 읽은 데이터를 6 line조건이 만족하여 결과에 데이터가 포함됩니다.
* 19 - 28 line : 구분자가 있는 경우 구분자를 기준으로 21 line 에서 구분자로 잘라서 배열에 넣어서 반환 하고 구분자가 없는 경우 파라터로 받은 ( row ) 데이터를 그냥 반환 합니다.
  * **주의 사항 : 성능 및 트랜잭션애 대한 고려를 해야 합니다.**
    * **업무처리를 여기서 하는 경우 : 성능, DB 처리 갯수는 여러 사항에 대해서 고려 하여 동기적, 비동기적으로 설계 할지 결정을 해아합니다.**
    * **호출한 곳에서 하는 경우 : 성능, DB 처리 갯수등 여러 사항에 대해서 고려 해야 히지만 병렬, 분산 등을 선택의 폭이 넓을 것으로 생각이 됩니다. 업무처리를 여기서 하는 경우에서 병렬, 분산까지 고려 해서 코드를 작성하기는 난이도가 더 높습니다.**

전체 소스는 다음과 같으며 소스에 대한 설명을 주석에 표시 하였습니다.

<details>

<summary>공통 유틸</summary>

```java
package com.hyomee;
import java.io.*;
import java.util.ArrayList;
import java.util.List;
public class FileReadUTIL {

  /**
   * 파일을 읽고 List로 돌려줌
   * @param file  파일 명이 포함된 파일 경로
   * @param frpVO 파일을 읽고 해석 할 때 속성
   * @return
   * @param <T> List안에 있는 객체로 String or String[]
   */
  public static <T> List<T> read(String file,
                                 FileReadParameterVO frpVO )   {
    List<T> results = new ArrayList<>();

    BufferedReader br = null;
    String row = null;
    try {
      // 문자셋 포함 여부에 따른 문자셋 변환
      if (frpVO.getCharset() != null) {
        br = new BufferedReader(new InputStreamReader(
                  new FileInputStream(file),
                frpVO.getCharset()));
      } else {
        br = new BufferedReader(new InputStreamReader(new FileInputStream(file)));
      }

      // 라인 단위 읽기
      row = convertCharSet(br, frpVO.getToCharset());

      while (row != null) {
        // CSV 헤더 포함 여부
        if (frpVO.isHeader()) {
          // 반환 값 List에 저장
          results.add(getCols( frpVO.getDelimiter(),  row));
        } else {
          frpVO.setHeader(true);
        }

        // 다음 라인 단위 읽기
        row = convertCharSet(br, frpVO.getToCharset());
      }
    } catch ( UnsupportedEncodingException e) {
      System.out.println("문자셋을 확인해 주세요.");
      throw new RuntimeException(e);
    } catch (IOException e) {
      System.out.println("파일을 확인해 주세요.");
      throw new RuntimeException(e);
    } finally {
      if (br != null  ) {
        try {
          br.close();
        } catch (IOException e) {
          throw new RuntimeException(e);
        }
      }
    }
    return results;
  }


  /**
   * CSV 파일과 같이 구분자가 있는 것에 대한 처리
   * @param delimiter
   * @param row
   * @return
   * @param <T>
   */
  private static <T> T getCols(String delimiter, String row) {
    if (delimiter != null) {
      String[] columns = row.split(delimiter);
      // TODO 이관에서 비동기 처리 저장 System.out.println("String[] :" + columns);
      return (T) columns;
    } else {
      // TODO 이관에서 비동기 처리 저장
      System.out.println("String :" + row);
    }

    return (T) row;

  }

  /**
   * CSV 파일과 같이 구분자에 의해서 행/열로 되어 있는 데이터에
   * 대해서 열을 String[]로 변환 후 반환
   * - 문자셋 이 없으면 String 반환
   * @param br
   * @param charset
   * @return
   * @throws IOException
   */
  private static String convertCharSet(BufferedReader br, String charset ) throws IOException {
    String record = br.readLine();
    if ( charset != null && record != null) {
      byte[] utf8Bytes = record.getBytes(charset);
      return new String(utf8Bytes, charset);
    }

    return record;
  }

}


class FileReadParameterVO {
  private boolean isHeader;           // CSV 해더 포함 여부
  private final String delimiter;     // CSV 열 구분자
  private final String charset;       // 원본 파일의 문자셋
  private final String toCharset;     // 변경할 문자셋

  FileReadParameterVO(boolean isHeader,
                      String delimiter,
                      String charset,
                      String toCharset) {
    this.isHeader = isHeader;
    this.delimiter = delimiter;
    this.charset = charset;
    this.toCharset = toCharset;
  }

  // 해더 포함 기본 파라메터 생성
  public static FileReadParameterVO initFrpVO() {
    return new FileReadParameterVO(true, null, null, null) ;
  }

  // 해더 포함 문자셋 지정 생성
  public static FileReadParameterVO initFrpVO(String charset) {
    return new FileReadParameterVO(true, null, charset, null) ;
  }

  // 해더 포함 문자셋을 다른 문자셋으로 변경
  public static FileReadParameterVO initFrpVO(String charset, String toCharset) {
    return new FileReadParameterVO(true, null, charset, toCharset) ;
  }


  // 해더 포함 여부 설정 , CSV 파일 열 구분자 지정
  public static FileReadParameterVO initFrpVO(boolean isHeader, String delimiter) {
    return new FileReadParameterVO(isHeader, delimiter, null , null) ;
  }

  // 해더 포함 여부 설정 , 문자셋을 다른 문자셋으로 변경
  public static FileReadParameterVO initFrpVO(boolean isHeader, String charset, String toCharset) {
    return new FileReadParameterVO(isHeader, null, charset, toCharset) ;
  }

  // 해더 포함 여부 설정 , CSV 구분자, 문자셋을 다른 문자셋으로 변경
  public static FileReadParameterVO initFrpVO(boolean isHeader,
                                                            String delimiter,
                                                            String charset,
                                                            String toCharset) {
    return new FileReadParameterVO(isHeader, delimiter, charset, toCharset) ;
  }


  public boolean isHeader() {
    return isHeader;
  }
  public boolean setHeader(boolean isHeader) {
    return this.isHeader = isHeader;
  }

  public String getDelimiter() {
    return delimiter;
  }

  public String getCharset() {
    return charset;
  }

  public String getToCharset() {
    return toCharset;
  }
}
```

</details>

#### 4-1. 구분자 "," 인 EUC-KR로 된 파일을 UTF-8로 변환 <a href="#id-4-1.-euc-kr-utf-8" id="id-4-1.-euc-kr-utf-8"></a>

공통 유틸을 사용의 read() 메서드 호출시 해더포함, 구분자(","), EUC-KR을 UTF-8로 변경 하기 위한 소스 코트 입니다.

```java
List<T> lists = FileReadUTIL.read("D:\\문서\\교육\\txt\\CAS_DAS_이관대사기능_EUCKR.csv",
            FileReadParameterVO.initFrpVO( true,",","EUC-KR", "UTF-8"));

int row = 0;
 
for ( T strings : lists) {
    int j = 0;
    for ( String str : (String[]) strings) {
       System.out.println(String.format("[ %s : %s ] : %s", row, col, str));
     }
   row = row + 1;
}
```

| 헤더 포함                                                                                                                                                                                                                                                                                                                                                                                                        | 해더 미포함                                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| ![](https://hyomee.gitbook.io/\~gitbook/image?url=https%3A%2F%2F1889142648-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FVfLaXPTe0t3ptNpFbmH5%252Fuploads%252F917JAtY1XefLdCLUwtKK%252Fimage.png%3Falt%3Dmedia%26token%3D43496b40-f7fa-4a3c-9a15-e9af3b392a19\&width=300\&dpr=4\&quality=100\&sign=6b67becb8f375224963181b355437ec5f48c6fb04ebe7130e65d532894848209) | ![](https://hyomee.gitbook.io/\~gitbook/image?url=https%3A%2F%2F1889142648-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FVfLaXPTe0t3ptNpFbmH5%252Fuploads%252Fa0HsCgT4JQ8jdrF4eHHd%252Fimage.png%3Falt%3Dmedia%26token%3Dac93031b-4332-4eea-9352-a1d4654c66e0\&width=300\&dpr=4\&quality=100\&sign=9a19b59765ba641cfe217e0fab215866f2e5c8007c9247d2c64b636e6f45fd5a) |
