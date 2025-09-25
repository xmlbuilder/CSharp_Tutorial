# MemoryStream을 이용한 XML 직렬화/역직렬화
 MemoryStream을 이용한 XML 직렬화/역직렬화의 교과서적인 구현.  
 이 방식은 파일 없이 메모리 내에서 객체를 XML로 변환하고 다시 복원할 수 있어, 네트워크 전송, 테스트, IPC 등에 아주 유용.


## 🧠 XML 직렬화: 메모리 스트림 기반 핵심 개념 요약
| 언어     | 메모리 스트림 방식                          | XML 처리 방식                       | 특징 요약                          |
|----------|---------------------------------------------|-------------------------------------|------------------------------------|
| C#       | `MemoryStream` + `XmlWriter/XmlReader`      | `XmlSerializer`                     | .NET 기본 제공, 강력한 타입 지원   |
| C++      | `std::stringstream` 또는 `xmlBuffer`        | `libxml2`, `tinyxml2`               | 저수준 제어 가능, 고성능 처리      |
| Java     | `ByteArrayOutputStream` / `InputStream`     | `JAXB`, `DOM`, `SAX`                | JAXB는 직관적, DOM/SAX은 유연함    |
| Python   | `io.StringIO` 또는 `io.BytesIO`             | `xml.etree.ElementTree`             | 간단하고 직관적, 내장 라이브러리   |

## C# 코드
```csharp
namespace  GrammarTest
{
    public class Program
    {
        public class Novel
        {
            public string Title { get; set; }
            public string Author { get; set; }
            public int Published { get; set; }
            public override string ToString()
            {
                return $"[Title = {Title}, Author={Author}, Published={Published}]";
            }
        }
        
        public static void Main(String[] args)
        {
            Novel[] novels = new[]
            {
                new Novel()
                {
                    Author = "제임스 P. 호건",
                    Title = "별의 계승자",
                    Published = 1977,
                },
                new Novel()
                {
                    Author = "제임스 P. 호건",
                    Title = "별의 계승자",
                    Published = 1977,
                },
            };
            var settings = new XmlWriterSettings()
            {
                Encoding = new System.Text.UTF8Encoding(false),
                Indent = true,
                IndentChars = " "
            };

            var stream = new MemoryStream();
            using (var writer = XmlWriter.Create(stream))
            {
                var serializer = new XmlSerializer(novels.GetType());
                serializer.Serialize(writer, novels);
            }
            
            //메모리 스트림을 초기화 하는 것이 중요하다.
            stream.Position = 0; 
            using (var reader = XmlReader.Create(stream))
            {
                var serializer = new XmlSerializer(typeof(Novel[]));
                var novel1 = serializer.Deserialize(reader) as Novel[];
                //Console.WriteLine(novel1);
                foreach (var novel in novel1)
                {
                    Console.WriteLine(novel);   
                }
            }
        }
    }
}
namespace  GrammarTest
{
    public class Program
    {
        [XmlRoot("novel")]
        public class Novel
        {
            [XmlElement(ElementName = "title")]
            public string Title { get; set; }
            
            [XmlElement(ElementName = "author")]
            public string Author { get; set; }
            [XmlIgnore]
            [XmlElement(ElementName = "published")]
            public int Published { get; set; }
            public override string ToString()
            {
                return $"[Title = {Title}, Author={Author}, Published={Published}]";
            }
        }
        [XmlRoot("novels")]
        public class NovelCollection
        {
            [XmlElement(Type = typeof(Novel), ElementName = "novel")]
            public Novel[] Novels { get; set; }
        }
        
        public static void Main(String[] args)
        {
            Novel[] novels = new[]
            {
                new Novel()
                {
                    Author = "제임스 P. 호건",
                    Title = "별의 계승자",
                    Published = 1977,
                },
                new Novel()
                {
                    Author = "제임스 P. 호건",
                    Title = "별의 계승자",
                    Published = 1977,
                },
            };
            NovelCollection novelCollection = new NovelCollection();
            novelCollection.Novels = novels;
            var settings = new XmlWriterSettings()
            {
                Encoding = new System.Text.UTF8Encoding(false),
                Indent = true,
                IndentChars = " "
            };
            var stream = new MemoryStream();
            using (var writer = XmlWriter.Create(stream))
            {
                var serializer = new XmlSerializer(novelCollection.GetType());
                serializer.Serialize(writer, novelCollection);
            }
            byte[] bytes = stream.GetBuffer();
            Console.WriteLine(Encoding.Default.GetString(bytes));
            
            //메모리 스트림을 초기화 하는 것이 중요하다.
            stream.Position = 0; 
            using (var reader = XmlReader.Create(stream))
            {
                var serializer = new XmlSerializer(typeof(NovelCollection));
                var novelcol = serializer.Deserialize(reader) as NovelCollection;
                //Console.WriteLine(novel1);
                foreach (var novel in novelcol.Novels)
                {
                    Console.WriteLine(novel);   
                }
            }
        }
    }
}

```

🧩 C++ (libxml2 + xmlBuffer)
```cpp
#include <libxml/parser.h>
#include <libxml/tree.h>
#include <libxml/xmlsave.h>
#include <iostream>
#include <sstream>

int main() {
    xmlDocPtr doc = xmlNewDoc(BAD_CAST "1.0");
    xmlNodePtr root = xmlNewNode(NULL, BAD_CAST "novels");
    xmlDocSetRootElement(doc, root);

    xmlNodePtr novel = xmlNewChild(root, NULL, BAD_CAST "novel", NULL);
    xmlNewChild(novel, NULL, BAD_CAST "title", BAD_CAST "별의 계승자");
    xmlNewChild(novel, NULL, BAD_CAST "author", BAD_CAST "제임스 P. 호건");
    xmlNewChild(novel, NULL, BAD_CAST "published", BAD_CAST "1977");

    // 직렬화: 메모리 스트림(xmlBuffer)에 저장
    xmlChar* buffer;
    int size;
    xmlDocDumpMemory(doc, &buffer, &size);
    std::string xmlStr((char*)buffer, size);
    std::cout << xmlStr << std::endl;

    // 역직렬화: 메모리에서 다시 읽기
    xmlDocPtr parsedDoc = xmlReadMemory(xmlStr.c_str(), xmlStr.size(), "noname.xml", NULL, 0);
    xmlFree(buffer);
    xmlFreeDoc(doc);
    xmlFreeDoc(parsedDoc);
}
```



## ☕ Java (JAXB + ByteArrayOutputStream)
```java
import javax.xml.bind.*;
import java.io.*;

class Novel {
    public String title;
    public String author;
    public int published;

    public Novel() {}
    public Novel(String title, String author, int published) {
        this.title = title;
        this.author = author;
        this.published = published;
    }
}

public class Main {
    public static void main(String[] args) throws Exception {
        Novel novel = new Novel("별의 계승자", "제임스 P. 호건", 1977);

        // 직렬화: 메모리 스트림에 XML 저장
        ByteArrayOutputStream baos = new ByteArrayOutputStream();
        JAXBContext context = JAXBContext.newInstance(Novel.class);
        Marshaller marshaller = context.createMarshaller();
        marshaller.setProperty(Marshaller.JAXB_FORMATTED_OUTPUT, true);
        marshaller.marshal(novel, baos);

        String xml = baos.toString("UTF-8");
        System.out.println(xml);

        // 역직렬화: 메모리 스트림에서 객체 복원
        ByteArrayInputStream bais = new ByteArrayInputStream(xml.getBytes("UTF-8"));
        Unmarshaller unmarshaller = context.createUnmarshaller();
        Novel parsed = (Novel) unmarshaller.unmarshal(bais);
        System.out.println(parsed.title + ", " + parsed.author + ", " + parsed.published);
    }
}

```

## 🐍 Python (ElementTree + io.StringIO)
```python
import xml.etree.ElementTree as ET
import io

novel = {
    "title": "별의 계승자",
    "author": "제임스 P. 호건",
    "published": 1977
}

# 직렬화: 메모리 스트림에 XML 저장
root = ET.Element("novel")
ET.SubElement(root, "title").text = novel["title"]
ET.SubElement(root, "author").text = novel["author"]
ET.SubElement(root, "published").text = str(novel["published"])

stream = io.StringIO()
tree = ET.ElementTree(root)
tree.write(stream, encoding="unicode", xml_declaration=True)

xml_str = stream.getvalue()
print(xml_str)

# 역직렬화: 메모리 스트림에서 객체 복원
stream.seek(0)
tree = ET.parse(stream)
root = tree.getroot()
parsed = {
    "title": root.find("title").text,
    "author": root.find("author").text,
    "published": int(root.find("published").text)
}
print(parsed)
```


## 🧠 요약: 메모리 스트림 직렬화의 장점

| 장점                     | 설명                                                                 |
|--------------------------|----------------------------------------------------------------------|
| 파일 I/O 불필요           | 디스크 접근 없이 메모리 내에서 직렬화/역직렬화 가능                   |
| 빠른 처리 속도           | 파일 시스템보다 빠른 메모리 접근으로 성능 향상                         |
| 네트워크 전송에 최적화    | 스트림 데이터를 바로 소켓이나 HTTP로 전송 가능                         |
| 테스트 및 디버깅에 유용   | 임시 데이터 생성, 구조 확인, 단위 테스트에 적합                        |
| 유연한 데이터 흐름 제어   | 스트림 위치 지정, 버퍼 크기 조절 등으로 세밀한 제어 가능               |


---
# **Shared Memory(공유 메모리)**를 활용

메모리 스트림을 넘어서 **Shared Memory(공유 메모리)**를 활용하면, 프로세스 간에 데이터를 파일 없이도 빠르게 주고받을 수 있음.  
특히 XML이나 JSON 직렬화된 데이터를 공유 메모리에 올려두면, 다른 프로세스가 바로 읽어서 역직렬화할 수 있기 때문에 IPC 성능과 효율이 극대화됩니다.

## 🧠 Shared Memory + 직렬화: 핵심 아이디어

| 언어     | 공유 메모리 방식                        | 직렬화 방식                         |
|----------|-----------------------------------------|-------------------------------------|
| C#       | `MemoryMappedFile`                      | `XmlSerializer`, `JsonSerializer`   |
| C++      | `shm_open`, `mmap`                      | `libxml2`, `nlohmann/json`, `Boost` |
| Java     | `MappedByteBuffer` (`FileChannel`)      | `JAXB`, `Gson`, `Jackson`           |
| Python   | `multiprocessing.shared_memory`         | `xml.etree.ElementTree`, `json`     |


## 💡 C# 예시: MemoryMappedFile + XML 직렬화
```csharp
using System.IO.MemoryMappedFiles;
using System.Text;
using System.Xml.Serialization;

// 직렬화
var novel = new Novel { Title = "별의 계승자", Author = "제임스 P. 호건", Published = 1977 };
var serializer = new XmlSerializer(typeof(Novel));
using var ms = new MemoryStream();
serializer.Serialize(ms, novel);
byte[] data = ms.ToArray();

// 공유 메모리에 쓰기
using var mmf = MemoryMappedFile.CreateOrOpen("SharedNovel", data.Length);
using var accessor = mmf.CreateViewAccessor();
accessor.WriteArray(0, data, 0, data.Length);

// 다른 프로세스에서 읽기
using var mmfRead = MemoryMappedFile.OpenExisting("SharedNovel");
using var accessorRead = mmfRead.CreateViewStream();
var deserialized = (Novel)serializer.Deserialize(accessorRead);
Console.WriteLine(deserialized);
```


## ☕ Java 예시: MappedByteBuffer + JSON 직렬화
Java에서는 FileChannel을 통해 메모리 매핑을 구현할 수 있어요. OS 수준 공유는 JNI나 외부 라이브러리로 확장 가능.
```java
RandomAccessFile file = new RandomAccessFile("shared.dat", "rw");
FileChannel channel = file.getChannel();
MappedByteBuffer buffer = channel.map(FileChannel.MapMode.READ_WRITE, 0, 1024);

String json = new Gson().toJson(novel);
buffer.put(json.getBytes(StandardCharsets.UTF_8));

```


## 🐍 Python 예시: multiprocessing.shared_memory + JSON
```python
from multiprocessing import shared_memory
import json

novel = {"title": "별의 계승자", "author": "제임스 P. 호건", "published": 1977}
json_str = json.dumps(novel)
encoded = json_str.encode("utf-8")

shm = shared_memory.SharedMemory(create=True, size=len(encoded), name="novel_shm")
shm.buf[:len(encoded)] = encoded

# 다른 프로세스에서 접근
existing_shm = shared_memory.SharedMemory(name="novel_shm")
received = bytes(existing_shm.buf[:len(encoded)]).decode("utf-8")
parsed = json.loads(received)
print(parsed)
```


## 🚀 Shared Memory 직렬화 방식의 장점 요약

| 장점                 | 설명                                                                 |
|----------------------|----------------------------------------------------------------------|
| 초고속 데이터 전송     | 파일 I/O 없이 메모리 기반으로 즉시 데이터 교환 가능                   |
| 실시간 IPC 지원       | 프로세스 간 실시간 통신에 적합, 지연 최소화                           |
| 다양한 포맷 호환      | XML, JSON, 바이너리 등 원하는 직렬화 포맷을 자유롭게 사용 가능         |
| 시스템 자원 절약      | 디스크 접근 없이 메모리만 사용하므로 성능 및 자원 효율성 향상          |
| OS 간 호환성          | Windows, Linux 등 주요 운영체제에서 공유 메모리 API 제공               |

---



