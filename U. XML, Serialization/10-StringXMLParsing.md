# 문자열 XML Parsing
문자열로 들어온 XML을 XDocument.Parse()나 XElement.Parse()를 통해 파싱하는 방식. 
이건 파일 없이 메모리 내에서 XML을 다룰 수 있어서 테스트, 네트워크 응답 처리, IPC 등에 아주 유용.
각 언어별로 DOM 기반 라이브러리를 활용하며, XPath나 노드 탐색도 함께 사용.

## C# 코드
```csharp
namespace GrammarTest
{
    public class Program
    {
        public static void Main(String[] args)
        {
            string xmlstirng = @"<?xml version=""1.0"" encoding=""utf-8""?>
               <!-- List 11-1 -->
               <novelists>
               <novelist>
               <name eng=""Mark Twain"">마크 트웨인</name>
               <birth>1835-11-30</birth>
               <death>1910-03-21</death>
               <masterpieces>
               <title>톰 소여의 모험</title>
               <title>허클베리 핀의 모험</title>
               <title>왕자와 거지</title>
               </masterpieces>
               </novelist>
               <novelist>
               <name eng=""O. Henry"">오 헨리</name>
               <birth>1862-10-11</birth>
               <death>1910-06-05</death>
               <masterpieces>
               <title>현자의 선물</title>
               <title>마지막 잎새</title>
               </masterpieces>
               </novelist>
               <novelist>
               <name eng=""Agatha Christie"">아가사 크리스티</name>
               <birth>1890-09-15</birth>
               <death>1976-01-12</death>
               <masterpieces>
               <title>그리고 아무도 없었다</title>
               <title>오리엔트 특급 살인</title>
               </masterpieces>
               </novelist>
               <novelist>
               <name eng=""Ernest Hemingway"">어니스트 헤밍웨이</name>
               <birth>1899-07-21</birth>
               <death>1961-07-02</death>
               <masterpieces>
               <title>무기여 잘 있거라</title>
               <title>노인과 바다</title>
               </masterpieces>
               </novelist>
               <novelist>
               <name eng=""Sidney Sheldon"">시드니 셸던</name>
               <birth>1917-02-11</birth>
               <death>2007-01-30</death>
               <masterpieces>
               <title>게임의 여왕</title>
               <title>천사의 분노</title>
               </masterpieces>
               </novelist>
               </novelists>";

            var xdoc = XDocument.Parse(xmlstirng);
            var xnovelists = xdoc.Root.Elements();
            foreach (var xnovelist in xnovelists)
            {
                Console.WriteLine(xnovelist.Name);
            }
            
            string elems = @"<novelist>
                <name eng=""Sidney Sheldon"">시드니 셸던</name>
                <birth>1917-02-11</birth>
                <death>2007-01-30</death>
                <masterpieces>
                <title>게임의 여왕</title>
                <title>천사의 분노</title>
                </masterpieces>
                </novelist>";
            
            XElement element = XElement.Parse(elems);
            Console.WriteLine(element.Name);
        }
    }
}

```


## 🧾 C# 요약
```csharp
XDocument xdoc = XDocument.Parse(xmlString); // 전체 문서 파싱
XElement element = XElement.Parse(xmlFragment); // 단일 요소 파싱
```


## 🧩 C++ 예시 (libxml2 + xmlReadMemory)
```cpp
#include <libxml/parser.h>
#include <libxml/tree.h>
#include <iostream>

int main() {
    const char* xmlString = R"(<?xml version="1.0"?>
        <novelists>
            <novelist>
                <name eng="Mark Twain">마크 트웨인</name>
            </novelist>
        </novelists>)";

    xmlDocPtr doc = xmlReadMemory(xmlString, strlen(xmlString), "noname.xml", NULL, 0);
    xmlNodePtr root = xmlDocGetRootElement(doc);

    for (xmlNodePtr node = root->children; node; node = node->next) {
        if (node->type == XML_ELEMENT_NODE && xmlStrEqual(node->name, BAD_CAST "novelist")) {
            for (xmlNodePtr child = node->children; child; child = child->next) {
                if (child->type == XML_ELEMENT_NODE && xmlStrEqual(child->name, BAD_CAST "name")) {
                    xmlChar* content = xmlNodeGetContent(child);
                    std::cout << "Name: " << content << std::endl;
                    xmlFree(content);
                }
            }
        }
    }

    xmlFreeDoc(doc);
}

```

## ☕ Java 예시 (DOM + InputSource + StringReader)
```java
import javax.xml.parsers.*;
import org.w3c.dom.*;
import org.xml.sax.InputSource;
import java.io.*;

public class XmlFromString {
    public static void main(String[] args) throws Exception {
        String xmlString = "<?xml version=\"1.0\"?><novelists><novelist><name eng=\"Mark Twain\">마크 트웨인</name></novelist></novelists>";

        DocumentBuilder builder = DocumentBuilderFactory.newInstance().newDocumentBuilder();
        InputSource is = new InputSource(new StringReader(xmlString));
        Document doc = builder.parse(is);

        NodeList novelists = doc.getElementsByTagName("novelist");
        for (int i = 0; i < novelists.getLength(); i++) {
            Element novelist = (Element) novelists.item(i);
            Element name = (Element) novelist.getElementsByTagName("name").item(0);
            System.out.println("Name: " + name.getTextContent());
        }
    }
}

```

## 🐍 Python 예시 (ElementTree + fromstring)
```python
import xml.etree.ElementTree as ET

xml_string = """<?xml version="1.0"?>
<novelists>
    <novelist>
        <name eng="Mark Twain">마크 트웨인</name>
    </novelist>
</novelists>"""

root = ET.fromstring(xml_string)
for novelist in root.findall("novelist"):
    name = novelist.find("name")
    print("Name:", name.text)
```

fromstring()은 파일 없이 문자열을 직접 파싱할 때 사용합니다.


## 🧠 요약: 문자열 기반 XML 파싱의 장점

| 장점                 | 설명                                                                 |
|----------------------|----------------------------------------------------------------------|
| 파일 I/O 불필요       | 디스크 접근 없이 메모리 내 문자열만으로 XML 처리 가능                 |
| 테스트에 최적화       | 샘플 XML 문자열을 직접 코드에 삽입해 빠르게 테스트 가능               |
| 네트워크 응답 처리    | API 응답 등 문자열 형태의 XML을 즉시 파싱하여 데이터 추출 가능         |
| 유연한 구조 처리      | 전체 문서뿐 아니라 단일 요소(fragment)도 자유롭게 파싱 가능            |
| 다양한 언어 지원      | C#, C++, Java, Python 등 대부분의 언어에서 유사한 방식으로 구현 가능     |

---



