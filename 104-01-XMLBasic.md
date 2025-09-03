# XML 기본
C#, C++(libxml2), Java, Python에서 XML 파일을 읽고 특정 요소를 추출하는 기본 예제를 정리한 버전입니다.

## 🧩 C# (LINQ to XML)

```csharp
using System.Xml.Linq;

var filePath = "/Users/jeongjunghwan/Development/Temp/novelists.xml";
var xdoc = XDocument.Load(filePath);
XElement? rootElem = xdoc.Root;
Console.WriteLine(rootElem?.Name);

var xelements = rootElem?.Elements();
foreach (var xelem in xelements)
{
    XElement? name = xelem.Element("name");
    var birth = (DateTime)xelem.Element("birth");
    Console.WriteLine(name?.Value + " " + birth);
}
```

C#은 XDocument, XElement를 통해 XML을 매우 직관적으로 탐색할 수 있어요.


## 🧱 C++ (libxml2)
```cpp
#include <libxml/parser.h>
#include <libxml/tree.h>
#include <iostream>

int main() {
    const char* filePath = "/Users/jeongjunghwan/Development/Temp/novelists.xml";
    xmlDocPtr doc = xmlReadFile(filePath, nullptr, 0);
    if (doc == nullptr) {
        std::cerr << "Failed to parse XML\n";
        return 1;
    }

    xmlNodePtr root = xmlDocGetRootElement(doc);
    std::cout << "Root: " << root->name << std::endl;

    for (xmlNodePtr node = root->children; node; node = node->next) {
        if (node->type == XML_ELEMENT_NODE) {
            xmlNodePtr nameNode = xmlFirstElementChild(node);
            xmlNodePtr birthNode = nameNode ? nameNode->next : nullptr;

            if (nameNode && birthNode) {
                std::cout << "Name: " << xmlNodeGetContent(nameNode) << ", Birth: "
                          << xmlNodeGetContent(birthNode) << std::endl;
            }
        }
    }

    xmlFreeDoc(doc);
    xmlCleanupParser();
    return 0;
}
```


libxml2는 C 기반이지만 C++에서도 사용 가능하며, xmlNodePtr을 통해 트리 탐색이 가능합니다.


## ☕ Java (DOM Parser)
```java
import javax.xml.parsers.*;
import org.w3c.dom.*;
import java.io.File;

public class XmlReader {
    public static void main(String[] args) throws Exception {
        File file = new File("/Users/jeongjunghwan/Development/Temp/novelists.xml");
        DocumentBuilderFactory factory = DocumentBuilderFactory.newInstance();
        DocumentBuilder builder = factory.newDocumentBuilder();
        Document doc = builder.parse(file);

        Element root = doc.getDocumentElement();
        System.out.println("Root: " + root.getTagName());

        NodeList items = root.getChildNodes();
        for (int i = 0; i < items.getLength(); i++) {
            Node node = items.item(i);
            if (node.getNodeType() == Node.ELEMENT_NODE) {
                Element elem = (Element) node;
                String name = elem.getElementsByTagName("name").item(0).getTextContent();
                String birth = elem.getElementsByTagName("birth").item(0).getTextContent();
                System.out.println(name + " " + birth);
            }
        }
    }
}
```

Java는 DocumentBuilder를 통해 DOM 트리를 구성하고 getElementsByTagName으로 요소를 추출합니다.


## 🐍 Python (ElementTree)
```python
import xml.etree.ElementTree as ET

file_path = "/Users/jeongjunghwan/Development/Temp/novelists.xml"
tree = ET.parse(file_path)
root = tree.getroot()

print("Root:", root.tag)

for elem in root:
    name = elem.find("name")
    birth = elem.find("birth")
    print(f"{name.text} {birth.text}")
```

Python은 ElementTree를 통해 XML을 간단하게 파싱할 수 있으며, find()로 요소를 탐색합니다.

## 🧭 XML 파일 읽기 방식 비교

| 언어     | XML 파서 방식           | 특징 요약                                       |
|----------|--------------------------|------------------------------------------------|
| **C#**   | `XDocument`, `XElement`  | LINQ 기반, 매우 직관적이고 선언적 접근 가능       |
| **C++**  | `libxml2`                | C 스타일 API, 고성능이지만 수동 탐색 필요          |
| **Java** | `DocumentBuilder`        | DOM 기반, 타입 캐스팅과 예외 처리 필요             |
| **Python** | `ElementTree`            | 간결하고 직관적, 동적 탐색과 속성 접근이 쉬움       |

---

