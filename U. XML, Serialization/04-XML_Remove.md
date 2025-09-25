# XML Remove
C# 코드에서는 XDocument를 사용해 XML을 로드하고, 특정 조건(name == "마크 트웨인")에 맞는 요소를 찾아 .Remove()로 삭제한 뒤 결과를 출력.

## C# 전체 코드
```csharp
namespace  GrammarTest
{
    public class Program
    {
        public static void Main(String[] args)
        {
            var xdoc = XDocument.Load("/Users/jeongjunghwan/Development/Temp/novelists.xml");
            var elements = xdoc.Root.Elements()
                .Where(x => x.Element("name").Value == "마크 트웨인");
            
            Console.WriteLine(elements.Count());
            
            elements.Remove();
            elements = xdoc.Root.Elements();
            foreach (XElement element in elements)
            {
                Console.WriteLine(element.Element("name").Value);
            }
        }
    }
}

```

## 🧩 C# 코드 요약
```csharp
var elements = xdoc.Root.Elements()
    .Where(x => x.Element("name").Value == "마크 트웨인");

elements.Remove(); // 해당 요소 제거
```

- Elements()로 자식 요소 탐색
- Where()로 조건 필터링
- Remove()로 해당 요소 삭제

## ☕ Java: DOM 방식
```java
import org.w3c.dom.*;
import javax.xml.parsers.*;
import java.io.File;

public class XMLRemove {
    public static void main(String[] args) throws Exception {
        DocumentBuilder builder = DocumentBuilderFactory.newInstance().newDocumentBuilder();
        Document doc = builder.parse(new File("novelists.xml"));
        Element root = doc.getDocumentElement();
        NodeList novelists = root.getElementsByTagName("novelist");

        for (int i = 0; i < novelists.getLength(); i++) {
            Element novelist = (Element) novelists.item(i);
            String name = novelist.getElementsByTagName("name").item(0).getTextContent();
            if (name.equals("마크 트웨인")) {
                root.removeChild(novelist);
                break; // 하나만 제거할 경우
            }
        }

        // 저장은 Transformer 사용
    }
}
```

- getElementsByTagName()으로 요소 탐색
- removeChild()로 삭제
- 저장하려면 Transformer API 사용

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
                        xmlUnlinkNode(node); // 트리에서 제거
                        xmlFreeNode(node);   // 메모리 해제
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

- xmlUnlinkNode()로 트리에서 제거
- xmlFreeNode()로 메모리 해제
- xmlSaveFormatFileEnc()로 저장

## 🐍 Python: ElementTree 방식
```python
import xml.etree.ElementTree as ET

tree = ET.parse("novelists.xml")
root = tree.getroot()

for novelist in root.findall("novelist"):
    name = novelist.find("name").text
    if name == "마크 트웨인":
        root.remove(novelist)
        break

tree.write("novelists_modified.xml", encoding="utf-8", xml_declaration=True)
```

- findall()로 요소 탐색
- remove()로 삭제
- write()로 저장

## 🧭 요약 비교
| 언어       | 제거 방식                     | 특징 요약                                      |
|------------|-------------------------------|------------------------------------------------|
| **C#**     | `Elements().Where().Remove()` | LINQ로 조건 필터링 후 직접 제거 가능            |
| **Java**   | `removeChild()`               | DOM 탐색 후 조건 일치 시 제거                   |
| **C++**    | `xmlUnlinkNode()` + `xmlFreeNode()` | libxml2로 직접 노드 제거 및 메

---


