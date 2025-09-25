# LINQ + XML
LINQ를 활용해 XML을 객체로 매핑하는 방식은 가독성과 유지보수 측면에서 매우 뛰어남. 

## 🧩 C# LINQ to XML (기준 코드)
```csharp
var novelist = xdoc.Root.Elements()
    .Select(x => new Novelist {
        Name = (string)x.Element("name"),
        Birth = (DateTime)x.Element("birth"),
        Death = (DateTime)x.Element("death"),
        Masterpieces = x.Element("masterpieces")
            .Elements("title").Select(t => t.Value).ToArray()
    }).ToArray();
```

- XML → 객체 변환
- LINQ로 필터링, 매핑, 컬렉션 생성
- 매우 선언적이고 직관적인 문법

## ☕ Java: Stream API + DOM
```java
import org.w3c.dom.*;
import javax.xml.parsers.*;
import java.io.File;
import java.time.LocalDate;
import java.util.*;
import java.util.stream.*;

class Novelist {
    String name;
    LocalDate birth;
    LocalDate death;
    List<String> masterpieces;
}

public class XMLStreamParser {
    public static List<Novelist> readNovelists(String path) throws Exception {
        DocumentBuilder builder = DocumentBuilderFactory.newInstance().newDocumentBuilder();
        Document doc = builder.parse(new File(path));
        NodeList nodes = doc.getDocumentElement().getElementsByTagName("novelist");

        return IntStream.range(0, nodes.getLength())
            .mapToObj(i -> {
                Element e = (Element) nodes.item(i);
                Novelist n = new Novelist();
                n.name = e.getElementsByTagName("name").item(0).getTextContent();
                n.birth = LocalDate.parse(e.getElementsByTagName("birth").item(0).getTextContent());
                n.death = LocalDate.parse(e.getElementsByTagName("death").item(0).getTextContent());
                NodeList titles = ((Element)e.getElementsByTagName("masterpieces").item(0)).getElementsByTagName("title");
                n.masterpieces = IntStream.range(0, titles.getLength())
                    .mapToObj(j -> titles.item(j).getTextContent())
                    .collect(Collectors.toList());
                return n;
            }).collect(Collectors.toList());
    }
}
```

Java는 Stream API를 활용하면 LINQ와 유사한 방식으로 XML을 객체로 변환할 수 있어요.


## 🧱 C++: libxml2 + STL
```cpp
#include <libxml/parser.h>
#include <libxml/tree.h>
#include <iostream>
#include <vector>
#include <string>

struct Novelist {
    std::string name;
    std::string birth;
    std::string death;
    std::vector<std::string> masterpieces;
};

std::vector<Novelist> readNovelists(const char* path) {
    std::vector<Novelist> result;
    xmlDocPtr doc = xmlReadFile(path, nullptr, 0);
    xmlNodePtr root = xmlDocGetRootElement(doc);

    for (xmlNodePtr node = root->children; node; node = node->next) {
        if (node->type == XML_ELEMENT_NODE && xmlStrEqual(node->name, BAD_CAST "novelist")) {
            Novelist n;
            for (xmlNodePtr child = node->children; child; child = child->next) {
                if (child->type == XML_ELEMENT_NODE) {
                    std::string tag = (char*)child->name;
                    std::string content = (char*)xmlNodeGetContent(child);
                    if (tag == "name") n.name = content;
                    else if (tag == "birth") n.birth = content;
                    else if (tag == "death") n.death = content;
                    else if (tag == "masterpieces") {
                        for (xmlNodePtr title = child->children; title; title = title->next) {
                            if (title->type == XML_ELEMENT_NODE && xmlStrEqual(title->name, BAD_CAST "title")) {
                                n.masterpieces.push_back((char*)xmlNodeGetContent(title));
                            }
                        }
                    }
                }
            }
            result.push_back(n);
        }
    }
    xmlFreeDoc(doc);
    return result;
}
```

C++은 LINQ 스타일은 없지만 STL과 반복문으로 유사한 구조를 만들 수 있어요.


## 🐍 Python: List Comprehension + ElementTree
```python
import xml.etree.ElementTree as ET
from datetime import datetime

class Novelist:
    def __init__(self, name, birth, death, masterpieces):
        self.name = name
        self.birth = birth
        self.death = death
        self.masterpieces = masterpieces

def read_novelists(path):
    tree = ET.parse(path)
    root = tree.getroot()
    return [
        Novelist(
            name=novel.find("name").text,
            birth=datetime.strptime(novel.find("birth").text, "%Y-%m-%d"),
            death=datetime.strptime(novel.find("death").text, "%Y-%m-%d"),
            masterpieces=[t.text for t in novel.find("masterpieces").findall("title")]
        )
        for novel in root.findall("novelist")
    ]
```

Python은 리스트 컴프리헨션으로 LINQ와 유사한 선언적 스타일을 구현할 수 있어요.


## 🧭 XML 파싱 방식 요약 비교

| 언어       | 방식                     | 특징 요약                                               |
|------------|--------------------------|----------------------------------------------------------|
| **C#**     | LINQ to XML              | 선언적 문법, 객체 매핑이 간편, LINQ로 직관적 탐색 가능     |
| **Java**   | Stream API + DOM         | DOM 기반 탐색, Stream으로 LINQ 유사 처리 가능             |
| **C++**    | libxml2 + STL            | 고성능 C 라이브러리, 반복문 기반 탐색, 수동 메모리 관리 필요 |
| **Python** | ElementTree + List Comp  | 간결한 문법, 리스트 컴프리헨션으로 LINQ 스타일 구현 가능     |

---





