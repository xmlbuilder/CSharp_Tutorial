# XML Chaining
XElement와 XDocument를 연속적으로 호출해 XML을 생성하고 저장하는 방식. 
이 방식은 **함수 체이닝(chained calls)**을 통해 XML 구조를 직관적으로 표현할 수 있어 코드가 깔끔하고 유지보수 용이.

``

## 🧾 C# 예시 요약
```csharp
var novelists = new XElement("novelists",
    new XElement("novelist",
        new XElement("name", "마크 트웨인", new XAttribute("eng", "Mark Twain")),
        new XElement("birth", "1835-11-30"),
        new XElement("death", "1910-03-21"),
        new XElement("masterpieces",
            new XElement("title", "톰 소여의 모험"))
    )
);

var xdoc = new XDocument(novelists);
xdoc.Save("/Users/jeongjunghwan/Downloads/novelist.xml");

```

## 🧩 C++ XML 생성 및 저장 (libxml2)
```cpp
#include <libxml/parser.h>
#include <libxml/tree.h>

int main() {
    // 1. XML 문서 생성
    xmlDocPtr doc = xmlNewDoc(BAD_CAST "1.0");

    // 2. 루트 노드 생성
    xmlNodePtr root = xmlNewNode(NULL, BAD_CAST "novelists");
    xmlDocSetRootElement(doc, root);

    // 3. novelist 노드 생성
    xmlNodePtr novelist = xmlNewChild(root, NULL, BAD_CAST "novelist", NULL);

    // 4. name 노드 생성 + 속성 추가
    xmlNodePtr name = xmlNewChild(novelist, NULL, BAD_CAST "name", BAD_CAST "마크 트웨인");
    xmlNewProp(name, BAD_CAST "eng", BAD_CAST "Mark Twain");

    // 5. birth, death 노드 추가
    xmlNewChild(novelist, NULL, BAD_CAST "birth", BAD_CAST "1835-11-30");
    xmlNewChild(novelist, NULL, BAD_CAST "death", BAD_CAST "1910-03-21");

    // 6. masterpieces 노드와 title 자식 노드 추가
    xmlNodePtr masterpieces = xmlNewChild(novelist, NULL, BAD_CAST "masterpieces", NULL);
    xmlNewChild(masterpieces, NULL, BAD_CAST "title", BAD_CAST "톰 소여의 모험");

    // 7. 파일로 저장
    xmlSaveFormatFileEnc("novelist.xml", doc, "UTF-8", 1);

    // 8. 메모리 해제
    xmlFreeDoc(doc);
    xmlCleanupParser();

    return 0;
}
```


### 📌 주요 함수 요약 (libxml2)

| 함수명                   | 설명                                                                 |
|--------------------------|----------------------------------------------------------------------|
| `xmlNewDoc()`            | 새로운 XML 문서를 생성합니다.                                        |
| `xmlNewNode()`           | 새로운 XML 노드를 생성합니다.                                        |
| `xmlNewChild()`          | 부모 노드에 자식 노드를 추가합니다.                                 |
| `xmlNewProp()`           | 노드에 속성(attribute)을 추가합니다.                                |
| `xmlDocSetRootElement()` | 문서의 루트(root) 노드를 설정합니다.                                |
| `xmlSaveFormatFileEnc()` | XML 문서를 파일로 저장하며 인코딩과 들여쓰기를 지정할 수 있습니다. |
| `xmlFreeDoc()`           | XML 문서에 할당된 메모리를 해제합니다.                              |
| `xmlCleanupParser()`     | libxml2 내부 파서 상태를 정리하고 메모리를 해제합니다.              |



## ☕ Java 예시 (DOM + Method Chaining 스타일)
```java
import javax.xml.parsers.*;
import org.w3c.dom.*;
import java.io.*;
import javax.xml.transform.*;
import javax.xml.transform.dom.DOMSource;
import javax.xml.transform.stream.StreamResult;

public class XmlBuilder {
    public static void main(String[] args) throws Exception {
        DocumentBuilder builder = DocumentBuilderFactory.newInstance().newDocumentBuilder();
        Document doc = builder.newDocument();

        Element root = doc.createElement("novelists");
        doc.appendChild(root);

        Element novelist = doc.createElement("novelist");
        root.appendChild(novelist);

        Element name = doc.createElement("name");
        name.setAttribute("eng", "Mark Twain");
        name.setTextContent("마크 트웨인");
        novelist.appendChild(name);

        novelist.appendChild(doc.createElement("birth")).setTextContent("1835-11-30");
        novelist.appendChild(doc.createElement("death")).setTextContent("1910-03-21");

        Element masterpieces = doc.createElement("masterpieces");
        masterpieces.appendChild(doc.createElement("title")).setTextContent("톰 소여의 모험");
        novelist.appendChild(masterpieces);

        Transformer transformer = TransformerFactory.newInstance().newTransformer();
        transformer.setOutputProperty(OutputKeys.INDENT, "yes");
        transformer.transform(new DOMSource(doc), new StreamResult(new File("novelist.xml")));
    }
}

```

## 🐍 Python 예시 (ElementTree + 함수 체이닝 느낌)
```python
import xml.etree.ElementTree as ET

novelist = ET.Element("novelist")
ET.SubElement(novelist, "name", {"eng": "Mark Twain"}).text = "마크 트웨인"
ET.SubElement(novelist, "birth").text = "1835-11-30"
ET.SubElement(novelist, "death").text = "1910-03-21"

masterpieces = ET.SubElement(novelist, "masterpieces")
ET.SubElement(masterpieces, "title").text = "톰 소여의 모험"

root = ET.Element("novelists")
root.append(novelist)

tree = ET.ElementTree(root)
tree.write("novelist.xml", encoding="utf-8", xml_declaration=True)
```

Python은 lxml을 사용하면 더 강력한 XPath와 pretty print도 가능합니다.


## 🧠 요약: 함수 체이닝으로 XML 생성의 장점

| 장점                        | 설명                                                                 |
|-----------------------------|----------------------------------------------------------------------|
| 코드 간결성                 | 중복 없이 깔끔한 코드 작성이 가능하며 가독성이 향상됩니다.          |
| 구조적 흐름 유지            | XML 트리 구조를 따라 자연스럽게 노드와 속성을 추가할 수 있습니다.   |
| 유지보수 용이               | 각 단계가 명확하게 연결되어 있어 수정 및 디버깅이 쉬워집니다.       |
| 생산성 향상                 | 반복적인 작업을 줄이고 빠르게 XML 문서를 구성할 수 있습니다.         |
| 오류 감소                   | 명확한 체이닝 흐름으로 실수 가능성을 줄이고 안정적인 결과를 도출합니다. |

---



