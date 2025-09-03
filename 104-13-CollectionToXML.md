# C# LINQ 기반 XML 생성 방식
C# LINQ 기반 XML 생성 방식은 정말 깔끔하고 함수 체이닝의 장점을 살림.

## C# 코드
```csharp
var novelists = new List<Novelist>()
{
    new Novelist()
    {
        Name = "마크 트웨인",
        EngName = "Mark Twain",
        Birth = DateTime.Parse("1835-11-30"),
        Death = DateTime.Parse("1910-03-21"),
        Masterpeices = new string[] {"톰 소여의 모험", "허클베리 핀의 모험"}
    },
    new Novelist()
    {
        Name = "어니스트 헤밍웨어",
        EngName = "Ernest Hemingway",
        Birth = DateTime.Parse("1899-07-21"),
        Death = DateTime.Parse("1961-07-02"),
        Masterpeices = new string[] {"무기여 잘 있거라", "노인과 바다"}
    }
};

var elements = novelists.Select(x =>
    new XElement("novelist",
        new XElement("name", x.Name, new XAttribute("eng", x.EngName)),
        new XElement("birth", x.Birth),
        new XElement("death", x.Death),
        new XElement("Masterpieces", x.Masterpeices.Select(t => new XElement("title", t)))
        ));
        
var root = new XElement("novelists", elements);
var xdoc = new XDocument(root);
xdoc.Save("/Users/jeongjunghwan/Downloads/SampleXML.xml");

```

## 📘 C++ (libxml2 사용)
```cpp
#include <libxml/parser.h>
#include <libxml/tree.h>

struct Novelist {
    std::string name;
    std::string engName;
    std::string birth;
    std::string death;
    std::vector<std::string> masterpieces;
};

// 예시 데이터
std::vector<Novelist> novelists = {
    {"마크 트웨인", "Mark Twain", "1835-11-30", "1910-03-21", {"톰 소여의 모험", "허클베리 핀의 모험"}},
    {"어니스트 헤밍웨이", "Ernest Hemingway", "1899-07-21", "1961-07-02", {"무기여 잘 있거라", "노인과 바다"}}
};

xmlDocPtr doc = xmlNewDoc(BAD_CAST "1.0");
xmlNodePtr root = xmlNewNode(NULL, BAD_CAST "novelists");
xmlDocSetRootElement(doc, root);

for (const auto& n : novelists) {
    xmlNodePtr novelistNode = xmlNewChild(root, NULL, BAD_CAST "novelist", NULL);
    xmlNodePtr nameNode = xmlNewChild(novelistNode, NULL, BAD_CAST "name", BAD_CAST n.name.c_str());
    xmlNewProp(nameNode, BAD_CAST "eng", BAD_CAST n.engName.c_str());
    xmlNewChild(novelistNode, NULL, BAD_CAST "birth", BAD_CAST n.birth.c_str());
    xmlNewChild(novelistNode, NULL, BAD_CAST "death", BAD_CAST n.death.c_str());

    xmlNodePtr mpNode = xmlNewChild(novelistNode, NULL, BAD_CAST "Masterpieces", NULL);
    for (const auto& title : n.masterpieces) {
        xmlNewChild(mpNode, NULL, BAD_CAST "title", BAD_CAST title.c_str());
    }
}

xmlSaveFormatFileEnc("SampleXML.xml", doc, "UTF-8", 1);
xmlFreeDoc(doc);
xmlCleanupParser();
```


## ☕ Java (DOM API 사용)
```java
import org.w3c.dom.*;
import javax.xml.parsers.*;
import javax.xml.transform.*;
import javax.xml.transform.dom.*;
import javax.xml.transform.stream.*;
import java.util.*;

class Novelist {
    String name, engName, birth, death;
    List<String> masterpieces;

    Novelist(String name, String engName, String birth, String death, List<String> masterpieces) {
        this.name = name; this.engName = engName; this.birth = birth; this.death = death; this.masterpieces = masterpieces;
    }
}

List<Novelist> novelists = Arrays.asList(
    new Novelist("마크 트웨인", "Mark Twain", "1835-11-30", "1910-03-21", Arrays.asList("톰 소여의 모험", "허클베리 핀의 모험")),
    new Novelist("어니스트 헤밍웨이", "Ernest Hemingway", "1899-07-21", "1961-07-02", Arrays.asList("무기여 잘 있거라", "노인과 바다"))
);

DocumentBuilderFactory factory = DocumentBuilderFactory.newInstance();
DocumentBuilder builder = factory.newDocumentBuilder();
Document doc = builder.newDocument();

Element root = doc.createElement("novelists");
doc.appendChild(root);

for (Novelist n : novelists) {
    Element novelist = doc.createElement("novelist");

    Element name = doc.createElement("name");
    name.setTextContent(n.name);
    name.setAttribute("eng", n.engName);
    novelist.appendChild(name);

    Element birth = doc.createElement("birth");
    birth.setTextContent(n.birth);
    novelist.appendChild(birth);

    Element death = doc.createElement("death");
    death.setTextContent(n.death);
    novelist.appendChild(death);

    Element masterpieces = doc.createElement("Masterpieces");
    for (String title : n.masterpieces) {
        Element t = doc.createElement("title");
        t.setTextContent(title);
        masterpieces.appendChild(t);
    }
    novelist.appendChild(masterpieces);
    root.appendChild(novelist);
}

Transformer transformer = TransformerFactory.newInstance().newTransformer();
transformer.setOutputProperty(OutputKeys.INDENT, "yes");
transformer.transform(new DOMSource(doc), new StreamResult(new File("SampleXML.xml")));

```

## 🐍 Python (ElementTree 사용)
```python
import xml.etree.ElementTree as ET

novelists = [
    {
        "name": "마크 트웨인",
        "eng": "Mark Twain",
        "birth": "1835-11-30",
        "death": "1910-03-21",
        "masterpieces": ["톰 소여의 모험", "허클베리 핀의 모험"]
    },
    {
        "name": "어니스트 헤밍웨이",
        "eng": "Ernest Hemingway",
        "birth": "1899-07-21",
        "death": "1961-07-02",
        "masterpieces": ["무기여 잘 있거라", "노인과 바다"]
    }
]

root = ET.Element("novelists")

for n in novelists:
    novelist = ET.SubElement(root, "novelist")
    name = ET.SubElement(novelist, "name", eng=n["eng"])
    name.text = n["name"]

    ET.SubElement(novelist, "birth").text = n["birth"]
    ET.SubElement(novelist, "death").text = n["death"]

    masterpieces = ET.SubElement(novelist, "Masterpieces")
    for title in n["masterpieces"]:
        ET.SubElement(masterpieces, "title").text = title

tree = ET.ElementTree(root)
tree.write("SampleXML.xml", encoding="utf-8", xml_declaration=True)
```
---
