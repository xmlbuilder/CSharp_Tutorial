# C# Node 수정
C# 코드에서는 특정 XML 노드를 찾아서 전체를 교체한 뒤, 내부 요소의 값과 속성을 수정.


## C# 코드
```csharp
namespace  GrammarTest
{
    public class Program
    {
        public static void Main(String[] args)
        {
            var xdoc = XDocument.Load("/Users/jeongjunghwan/Development/Temp/novelists.xml");
            var element = xdoc.Root.Elements()
                .Single(x => x.Element("name").Value == "마크 트웨인");

            if (element != null)
            {
                string elemString = @"<novelist>
                    <name eng=""Mark Twain"">마크 트웨인</name>
                    <birth>1835-11-30</birth>
                    <death>1910-03-21</death>
                    <masterpieces>
                    <title>톰 소여의 모험</title>
                    <title>허클베리 핀의 모험</title>
                    <title>왕자와 거지</title>
                    </masterpieces>
                    </novelist>";
                var newElement = XElement.Parse(elemString);
                
                element.ReplaceWith(newElement);
                element.Element("name").Value = "정중환";
                element.Element("name").SetAttributeValue("eng", "JeongJungHwan");
                
                Console.WriteLine(element);
            }
        }
    }
}

```

## 🧩 C# 기준 코드 요약
```csharp
var element = xdoc.Root.Elements()
    .Single(x => x.Element("name").Value == "마크 트웨인");

var newElement = XElement.Parse(elemString);
element.ReplaceWith(newElement);

element.Element("name").Value = "정중환";
element.Element("name").SetAttributeValue("eng", "JeongJungHwan");
```

- 특정 조건으로 노드 찾기
- ReplaceWith()로 전체 교체
- 내부 요소 값 변경 및 속성 추가

☕ Java: DOM 방식
```java
import org.w3c.dom.*;
import javax.xml.parsers.*;
import javax.xml.transform.*;
import javax.xml.transform.dom.*;
import javax.xml.transform.stream.*;
import java.io.File;

public class XMLNodeUpdate {
    public static void main(String[] args) throws Exception {
        DocumentBuilder builder = DocumentBuilderFactory.newInstance().newDocumentBuilder();
        Document doc = builder.parse(new File("novelists.xml"));
        Element root = doc.getDocumentElement();
        NodeList novelists = root.getElementsByTagName("novelist");

        for (int i = 0; i < novelists.getLength(); i++) {
            Element novelist = (Element) novelists.item(i);
            String name = novelist.getElementsByTagName("name").item(0).getTextContent();
            if (name.equals("마크 트웨인")) {
                Element nameElem = (Element) novelist.getElementsByTagName("name").item(0);
                nameElem.setTextContent("정중환");
                nameElem.setAttribute("eng", "JeongJungHwan");
                break;
            }
        }

        Transformer transformer = TransformerFactory.newInstance().newTransformer();
        transformer.transform(new DOMSource(doc), new StreamResult(new File("novelists_modified.xml")));
    }
}

```

Java는 DOM API를 통해 요소 탐색 후 setTextContent()와 setAttribute()로 수정합니다.


## 🧱 C++: libxml2 방식
```cpp
#include <libxml/parser.h>
#include <libxml/tree.h>
#include <iostream>

int main() {
    xmlDocPtr doc = xmlReadFile("novelists.xml", nullptr, 0);
    xmlNodePtr root = xmlDocGetRootElement(doc);

    for (xmlNodePtr node = root->children; node; node = node->next) {
        if (node->type == XML_ELEMENT_NODE && xmlStrEqual(node->name, BAD_CAST "novelist")) {
            for (xmlNodePtr child = node->children; child; child = child->next) {
                if (child->type == XML_ELEMENT_NODE && xmlStrEqual(child->name, BAD_CAST "name")) {
                    std::string name = (char*)xmlNodeGetContent(child);
                    if (name == "마크 트웨인") {
                        xmlNodeSetContent(child, BAD_CAST "정중환");
                        xmlSetProp(child, BAD_CAST "eng", BAD_CAST "JeongJungHwan");
                        break;
                    }
                }
            }
        }
    }

    xmlSaveFormatFileEnc("novelists_modified.xml", doc, "UTF-8", 1);
    xmlFreeDoc(doc);
    xmlCleanupParser();
}
```

C++에서는 xmlNodeSetContent()로 텍스트 변경, xmlSetProp()으로 속성 추가합니다.


## 🐍 Python: ElementTree 방식
```python
import xml.etree.ElementTree as ET

tree = ET.parse("novelists.xml")
root = tree.getroot()

for novelist in root.findall("novelist"):
    name_elem = novelist.find("name")
    if name_elem.text == "마크 트웨인":
        name_elem.text = "정중환"
        name_elem.set("eng", "JeongJungHwan")
        break

tree.write("novelists_modified.xml", encoding="utf-8", xml_declaration=True)

```

Python에서는 text 속성으로 값 변경, set()으로 속성 추가가 가능합니다.


## 🧭 요약 비교
| 언어       | 노드 변경 방식                          | 특징 요약                                           |
|------------|-----------------------------------------|------------------------------------------------------|
| **C#**     | `ReplaceWith()`, `.Value`, `SetAttributeValue()` | LINQ + 선언적 문법으로 전체 교체 및 속성 수정 가능     |
| **Java**   | `setTextContent()`, `setAttribute()`     | DOM 기반, 명시적 탐색과 수정                         |
| **C++**    | `xmlNodeSetContent()`, `xmlSetProp()`    | libxml2로 직접 노드 수정, 메모리 관리 필요            |
| **Python** | `.text`, `.set()`                        | 간결하고 직관적인 문법으로 요소 및 속성 수정 가능      |

---
