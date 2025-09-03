# 문자열 기반 직렬화/역직렬화
C# 코드처럼 XML을 문자열 기반으로 직렬화/역직렬화하는 방식은 다양한 언어에서도 활용.

## 🧾 C# 예시 요약
```csharp
namespace GrammarTest
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

            var sb = new StringBuilder();
            using (var writer = XmlWriter.Create(sb))
            {
                var serializer = new XmlSerializer(novels.GetType());
                serializer.Serialize(writer, novels);
            }
            var xmlText = sb.ToString();
            
            using (var reader = XmlReader.Create(new StringReader(xmlText)))
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

```

- XmlSerializer를 사용해 Novel[] 객체를 XML 문자열로 직렬화
- StringBuilder와 XmlWriter로 메모리 내에서 처리
- XmlReader와 StringReader로 역직렬화

## 🧩 C++ (libxml2)
libxml2는 기본적으로 DOM 기반이지만, 문자열로 XML을 처리할 수 있습니다.
```cpp
#include <libxml/parser.h>
#include <libxml/tree.h>
#include <libxml/xmlsave.h>

std::string serializeXmlToString(xmlDocPtr doc) {
    xmlChar *xmlBuff;
    int buffersize;
    xmlDocDumpFormatMemory(doc, &xmlBuff, &buffersize, 1);
    std::string result((char*)xmlBuff, buffersize);
    xmlFree(xmlBuff);
    return result;
}

xmlDocPtr deserializeXmlFromString(const std::string& xmlStr) {
    return xmlReadMemory(xmlStr.c_str(), xmlStr.size(), "noname.xml", NULL, 0);
}

```


## ☕ Java (JAXB)
Java에서는 JAXB를 사용해 객체를 XML 문자열로 직렬화/역직렬화합니다.
```java
StringWriter writer = new StringWriter();
JAXBContext context = JAXBContext.newInstance(Novel[].class);
Marshaller marshaller = context.createMarshaller();
marshaller.setProperty(Marshaller.JAXB_FORMATTED_OUTPUT, true);
marshaller.marshal(novels, writer);
String xml = writer.toString();

StringReader reader = new StringReader(xml);
Unmarshaller unmarshaller = context.createUnmarshaller();
Novel[] deserialized = (Novel[]) unmarshaller.unmarshal(reader);
```

Novel 클래스에는 @XmlRootElement와 @XmlElement 어노테이션이 필요합니다.


## 🐍 Python (xml.etree.ElementTree)
Python에서는 ElementTree를 사용해 XML 문자열을 다룹니다.
```python
import xml.etree.ElementTree as ET

def serialize_novels(novels):
    root = ET.Element("Novels")
    for novel in novels:
        item = ET.SubElement(root, "Novel")
        ET.SubElement(item, "Title").text = novel["Title"]
        ET.SubElement(item, "Author").text = novel["Author"]
        ET.SubElement(item, "Published").text = str(novel["Published"])
    return ET.tostring(root, encoding="unicode")

def deserialize_novels(xml_str):
    root = ET.fromstring(xml_str)
    novels = []
    for item in root.findall("Novel"):
        novels.append({
            "Title": item.find("Title").text,
            "Author": item.find("Author").text,
            "Published": int(item.find("Published").text)
        })
    return novels
```


## 🎯 XML 문자열 직렬화 용도 요약

| 용도             | 설명                                                                 | 예시 활용 시나리오                          |
|------------------|----------------------------------------------------------------------|---------------------------------------------|
| 네트워크 전송     | XML을 문자열로 만들어 HTTP, TCP 등으로 전송                          | REST API, SOAP 메시지, IoT 디바이스 통신     |
| IPC 통신         | 파일 없이 메모리 기반으로 프로세스 간 데이터 교환                    | 서버-클라이언트 간 메시지 교환, RPC         |
| 테스트/디버깅     | 파일 없이 XML 구조를 빠르게 확인하거나 로그로 출력                    | 단위 테스트, 로그 기록, 개발 중 구조 확인    |
| 임베디드 시스템   | 파일 시스템이 제한된 환경에서 메모리 기반 직렬화 활용 가능             | 센서 데이터 직렬화, 펌웨어 내 설정 저장      |
| 캐싱 및 임시 저장 | XML을 문자열로 캐싱하거나 메모리 내 임시 저장                         | Redis 캐시, 세션 데이터 저장                |

---
# JSON 직렬화

JSON 기반 문자열 직렬화를 정리.  
JSON은 XML보다 더 간결하고 가독성이 좋아서 웹 API, 모바일 앱, 클라우드 서비스 등에서 널리 사용됩니다.

## 🧠 JSON 문자열 직렬화의 핵심 용도

| 용도             | 설명                                                                 | 예시 활용 시나리오                          | 장점                                  |
|------------------|----------------------------------------------------------------------|---------------------------------------------|----------------------------------------|
| 네트워크 전송     | JSON 문자열로 만들어 HTTP, WebSocket 등으로 전송                     | REST API, GraphQL, 모바일 앱 통신           | 경량, 빠른 파싱, 표준화된 포맷         |
| 캐싱 및 로그 저장 | JSON 포맷으로 메모리 캐싱하거나 로그 기록                             | Redis, 로그 파일, 디버깅 출력                | 구조화된 로그, 빠른 조회                |
| 테스트/디버깅     | 객체 구조를 쉽게 확인하고 테스트 데이터로 활용                        | 단위 테스트, 샘플 데이터 생성               | 사람이 읽기 쉬움, 수정 용이             |
| 프론트엔드 연동   | 웹 UI와 백엔드 간 데이터 교환에 최적화                                | React, Vue, Angular와의 통신                | 자바스크립트와 자연스럽게 호환          |
| 설정 및 구성 저장 | 앱이나 시스템의 설정을 JSON으로 저장                                  | config.json, 환경 설정 파일                  | 간결한 구조, 다양한 언어에서 지원       |

## C#
### 1. System.Text.Json
```csharp
using System;
using System.Text.Json;

public class Novel
{
    public string Title { get; set; }
    public string Author { get; set; }
    public int Published { get; set; }
}

public class Program
{
    public static void Main()
    {
        var novel = new Novel
        {
            Title = "별의 계승자",
            Author = "제임스 P. 호건",
            Published = 1977
        };

        // 직렬화: 객체 → JSON 문자열
        string jsonString = JsonSerializer.Serialize(novel, new JsonSerializerOptions { WriteIndented = true });
        Console.WriteLine(jsonString);

        // 역직렬화: JSON 문자열 → 객체
        var deserialized = JsonSerializer.Deserialize<Novel>(jsonString);
        Console.WriteLine($"[Title={deserialized.Title}, Author={deserialized.Author}, Published={deserialized.Published}]");
    }
}
```

### 2. Networksoft.Json
```csharp
using System;
using Newtonsoft.Json;

public class Novel
{
    public string Title { get; set; }
    public string Author { get; set; }
    public int Published { get; set; }
}

public class Program
{
    public static void Main()
    {
        var novel = new Novel
        {
            Title = "별의 계승자",
            Author = "제임스 P. 호건",
            Published = 1977
        };

        // 직렬화
        string jsonString = JsonConvert.SerializeObject(novel, Formatting.Indented);
        Console.WriteLine(jsonString);

        // 역직렬화
        var deserialized = JsonConvert.DeserializeObject<Novel>(jsonString);
        Console.WriteLine($"[Title={deserialized.Title}, Author={deserialized.Author}, Published={deserialized.Published}]");
    }
}
```

## 🧩 C++ (nlohmann/json 라이브러리)
```cpp
#include <nlohmann/json.hpp>
using json = nlohmann::json;

struct Novel {
    std::string title;
    std::string author;
    int published;
};

void to_json(json& j, const Novel& n) {
    j = json{{"Title", n.title}, {"Author", n.author}, {"Published", n.published}};
}

void from_json(const json& j, Novel& n) {
    j.at("Title").get_to(n.title);
    j.at("Author").get_to(n.author);
    j.at("Published").get_to(n.published);
}


// 직렬화
Novel novel = {"별의 계승자", "제임스 P. 호건", 1977};
json j = novel;
std::string jsonStr = j.dump(4); // pretty print

// 역직렬화
Novel parsed = j.get<Novel>();
```

## ☕ Java (Gson 또는 Jackson)
### 1. Gson 예시:
```java
import com.google.gson.Gson;

class Novel {
    String title;
    String author;
    int published;
}

// 직렬화
Novel novel = new Novel();
novel.title = "별의 계승자";
novel.author = "제임스 P. 호건";
novel.published = 1977;

Gson gson = new Gson();
String jsonStr = gson.toJson(novel);

// 역직렬화
Novel parsed = gson.fromJson(jsonStr, Novel.class);
```

### 2. Jackson 예시:
```java
import com.fasterxml.jackson.databind.ObjectMapper;

ObjectMapper mapper = new ObjectMapper();
String jsonStr = mapper.writeValueAsString(novel);
Novel parsed = mapper.readValue(jsonStr, Novel.class);
```

## 🐍 Python (json 모듈)
```python
import json

class Novel:
    def __init__(self, title, author, published):
        self.title = title
        self.author = author
        self.published = published

    def to_dict(self):
        return {
            "Title": self.title,
            "Author": self.author,
            "Published": self.published
        }

    @staticmethod
    def from_dict(d):
        return Novel(d["Title"], d["Author"], d["Published"])

# 직렬화
novel = Novel("별의 계승자", "제임스 P. 호건", 1977)
json_str = json.dumps(novel.to_dict(), indent=4)

# 역직렬화
parsed_dict = json.loads(json_str)
parsed_novel = Novel.from_dict(parsed_dict)
```

## 📊 XML vs JSON 직렬화 비교

| 항목             | XML 직렬화                                      | JSON 직렬화                                     | 비고 및 활용 팁                          |
|------------------|--------------------------------------------------|--------------------------------------------------|-------------------------------------------|
| 구조 표현         | 태그 기반, 계층적 구조 표현에 강함               | 키-값 기반, 간결한 계층 표현                    | 복잡한 문서 구조는 XML이 유리              |
| 가독성            | 장황하고 중첩이 많아 사람이 읽기 어려움          | 간결하고 직관적, 사람이 읽기 쉬움               | 로그나 디버깅에는 JSON이 선호됨            |
| 데이터 크기       | 태그로 인해 크기가 큼                            | 상대적으로 작고 전송 효율 높음                  | 네트워크 전송 시 JSON이 유리               |
| 스키마 지원       | XSD 등으로 엄격한 구조 정의 가능                 | 별도 스키마 없음, 유연한 구조                   | 정형 데이터는 XML, 유연한 구조는 JSON      |
| 네임스페이스       | 복잡한 네임스페이스 지원                         | 네임스페이스 개념 없음                          | XML은 표준 문서 교환에 적합                |
| 파싱 속도         | 느림 (DOM/SAX 방식)                              | 빠름 (대부분의 언어에서 기본 지원)              | 실시간 처리에는 JSON이 적합                |
| 사용 환경         | SOAP, 레거시 시스템, 문서 중심 통신              | REST API, 모바일 앱, 웹 프론트엔드              | 현대 시스템은 대부분 JSON 기반             |
| 언어 호환성       | 다양한 언어에서 지원되지만 설정이 복잡할 수 있음 | 거의 모든 언어에서 기본 라이브러리로 지원        | Python, JavaScript 등에서 JSON이 기본      |



---
