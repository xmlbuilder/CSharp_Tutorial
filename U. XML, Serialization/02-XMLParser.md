# C# XMLParser

DOM 방식으로 XML 파일을 파싱해서 루트 노드와 자식 노드의 이름과 내용을 출력하는 구조. 


## 🧩 C#: LINQ to XML 방식

```csharp
using System.Xml.Linq;

var doc = XDocument.Load("novelists.xml");
var root = doc.Root;
Console.WriteLine(root.Name);

foreach (var node in root.Elements())
{
    foreach (var child in node.Elements())
    {
        Console.WriteLine($"{child.Name} : {child.Value.Trim()}");
    }
}
```

## ☕ Java: DOM 기반 XML 파싱
```java
DocumentBuilderFactory factory = DocumentBuilderFactory.newInstance();
DocumentBuilder builder = factory.newDocumentBuilder();
Document doc = builder.parse("novelists.xml");
Element root = doc.getDocumentElement();
System.out.println(root.getNodeName());
```

- DocumentBuilderFactory로 파서 생성
- parse()로 XML 파일 로딩
- getDocumentElement()로 루트 노드 접근
- getChildNodes()로 자식 노드 반복 탐색


- XDocument.Load()로 XML 로딩
- Root로 루트 노드 접근
- Elements()로 자식 노드 반복
- Name, Value로 정보 출력

C#은 LINQ 기반으로 XML을 매우 직관적으로 탐색할 수 있어요.


## 🧱 C++: libxml2 기반
```cpp
#include <libxml/parser.h>
#include <libxml/tree.h>
#include <iostream>

int main() {
    xmlDocPtr doc = xmlReadFile("novelists.xml", nullptr, 0);
    if (!doc) return 1;

    xmlNodePtr root = xmlDocGetRootElement(doc);
    std::cout << "Root: " << root->name << std::endl;

    for (xmlNodePtr node = root->children; node; node = node->next) {
        if (node->type == XML_ELEMENT_NODE) {
            for (xmlNodePtr child = node->children; child; child = child->next) {
                if (child->type == XML_ELEMENT_NODE) {
                    std::cout << child->name << " : " << xmlNodeGetContent(child) << std::endl;
                }
            }
        }
    }

    xmlFreeDoc(doc);
    xmlCleanupParser();
    return 0;
}
```

- xmlReadFile()로 XML 로딩
- xmlDocGetRootElement()로 루트 노드 접근
- xmlNodePtr로 자식 노드 반복
- xmlNodeGetContent()로 텍스트 추출
libxml2는 C 기반이지만 C++에서도 사용 가능하며, 고성능 XML 파서로 유명합니다.


## 🐍 Python: ElementTree 방식
```python
import xml.etree.ElementTree as ET

tree = ET.parse("novelists.xml")
root = tree.getroot()
print("Root:", root.tag)

for node in root:
    for child in node:
        print(f"{child.tag} : {child.text.strip()}")

```

- ET.parse()로 XML 로딩
- getroot()로 루트 노드 접근
- for 루프로 자식 노드 탐색
- tag, text로 정보 출력
Python은 ElementTree를 통해 XML을 간결하게 처리할 수 있어요.


## 🧭 XML 파싱 방식 비교

| 언어       | 파서 방식              | 특징 요약                                               |
|------------|------------------------|----------------------------------------------------------|
| **Java**   | `DocumentBuilder`      | DOM 기반, 표준 API, 타입 캐스팅 필요                    |
| **C#**     | `XDocument`, `XElement`| LINQ 기반, 선언적 탐색, 직관적인 문법                   |
| **C++**    | `libxml2`              | 고성능 C 라이브러리, 수동 탐색, 메모리 관리 필요         |
| **Python** | `ElementTree`          | 간결한 문법, 직관적인 탐색, 속성 접근이 쉬움             |

---

