# C# XPath


XPath를 사용해 XML 문서에서 특정 노드를 선택하는 기본적인 방식.  
XML 문서에서 원하는 노드를 경로 기반으로 추출하는 것!

## 🧾 C# 예시 (기본 XPath 사용)
```csharp
XmlDocument xmlDocument = new XmlDocument();
xmlDocument.Load("/Users/jeongjunghwan/Development/Temp/novelists.xml");

XmlNodeList itemNodes = xmlDocument.SelectNodes("//novelist");
foreach (XmlNode itemNode in itemNodes)
{
    XmlNode nameNode = itemNode.SelectSingleNode("name");
    Console.WriteLine(nameNode.InnerText);
}
```

- //novelist: 루트부터 모든 novelist 노드 선택
- SelectSingleNode("name"): 현재 novelist 노드의 자식 중 name 하나 선택

## 🧩 C++ 예시 (libxml2 + XPath)
```cpp
#include <libxml/parser.h>
#include <libxml/xpath.h>
#include <iostream>

int main() {
    xmlDocPtr doc = xmlParseFile("novelists.xml");
    xmlXPathContextPtr context = xmlXPathNewContext(doc);
    xmlXPathObjectPtr result = xmlXPathEvalExpression((xmlChar*)"//novelist/name", context);

    xmlNodeSetPtr nodes = result->nodesetval;
    for (int i = 0; i < nodes->nodeNr; ++i) {
        xmlNodePtr node = nodes->nodeTab[i];
        std::cout << xmlNodeGetContent(node) << std::endl;
    }

    xmlXPathFreeObject(result);
    xmlXPathFreeContext(context);
    xmlFreeDoc(doc);
}
```

- xmlXPathEvalExpression("//novelist/name"): 모든 novelist의 name 노드 선택
- xmlNodeGetContent(node): 텍스트 추출

## ☕ Java 예시 (XPath API + DOM)
```java
import javax.xml.parsers.*;
import javax.xml.xpath.*;
import org.w3c.dom.*;
import java.io.File;

public class XPathExample {
    public static void main(String[] args) throws Exception {
        File xmlFile = new File("novelists.xml");
        DocumentBuilder builder = DocumentBuilderFactory.newInstance().newDocumentBuilder();
        Document doc = builder.parse(xmlFile);

        XPath xpath = XPathFactory.newInstance().newXPath();
        NodeList nodes = (NodeList) xpath.evaluate("//novelist/name", doc, XPathConstants.NODESET);

        for (int i = 0; i < nodes.getLength(); i++) {
            System.out.println(nodes.item(i).getTextContent());
        }
    }
}

```

- XPathFactory.newInstance().newXPath(): XPath 객체 생성
- xpath.evaluate("//novelist/name", ...): XPath 표현식 실행

## 🐍 Python 예시 (lxml 사용)
```python
from lxml import etree

tree = etree.parse("novelists.xml")
names = tree.xpath("//novelist/name")

for name in names:
    print(name.text)
```

- tree.xpath("//novelist/name"): XPath 표현식으로 노드 리스트 반환
- name.text: 텍스트 추출
lxml은 libxml2 기반으로 매우 빠르고 XPath 기능이 강력합니다.


## 🔍 XPath 표현식 예시 요약

| XPath 표현식             | 설명                                 |
|--------------------------|--------------------------------------|
| `/root/child`            | 루트 노드 아래의 `child` 노드 선택     |
| `//child`                | 문서 전체에서 모든 `child` 노드 선택   |
| `child[@id='123']`       | `id` 속성이 123인 `child` 노드 선택   |
| `child[position()=1]`    | 첫 번째 `child` 노드 선택              |
| `child/text()`           | `child` 노드의 텍스트 내용만 선택      |


## 🧠 요약: XPath의 장점

| 장점               | 설명                                                                 |
|--------------------|----------------------------------------------------------------------|
| 구조적 탐색         | XML 문서의 계층 구조를 경로 기반으로 직관적으로 탐색 가능             |
| 조건 필터링         | 속성, 위치, 텍스트 조건 등으로 원하는 노드만 정밀하게 선택 가능        |
| 표현식의 유연성     | 다양한 연산자와 함수로 복잡한 탐색 로직을 간결하게 표현 가능           |
| 언어 간 일관성      | XPath 문법은 C#, Java, Python 등 대부분의 언어에서 동일하게 적용 가능   |
| 성능 최적화         | 대용량 XML에서도 빠른 노드 검색 가능 (특히 lxml, libxml2 기반에서 우수) |

----

# XPath의 핵심 기능 중 하나인 조건 검색과 속성 기반 필터링
XPath의 핵심 기능 중 하나인 조건 검색과 속성 기반 필터링.   
이 두 가지는 XML 문서에서 원하는 데이터를 정확하게 추출할 수 있게 해주는 강력한 도구입니다.

## 🔍 XPath 조건 검색
XPath에서는 **조건(predicate)**을 대괄호 [] 안에 작성하여 특정 조건을 만족하는 노드를 선택할 수 있어요. 아래는 대표적인 조건 검색 예시입니다:
<bookstore>
  <book category="fiction">
    <title lang="en">별의 계승자</title>
    <price>29.99</price>
  </book>
  <book category="science">
    <title lang="ko">코스모스</title>
    <price>39.95</price>
  </book>
</bookstore>


### ✅ XPath 조건 검색 예시

| XPath 표현식                     | 설명 또는 선택된 노드 예시             |
|----------------------------------|----------------------------------------|
| `/bookstore/book[price > 30]`    | `price`가 30보다 큰 `book` 노드 선택     |
| `//book[title[@lang='ko']]`      | `title`의 `lang` 속성이 `ko`인 `book` 선택 |
| `//book[title='별의 계승자']`    | 제목이 "별의 계승자"인 `book` 노드 선택  |
| `//book[price > 30]/title`       | 가격이 30 초과인 책의 `title` 노드 선택  |
| `//book[position()=1]`           | 첫 번째 `book` 노드 선택                |

position()과 last() 같은 함수도 조건 검색에 자주 사용됩니다.


## 🏷️ XPath 속성 기반 필터링
속성(attribute)을 기준으로 노드를 선택할 때는 @속성명을 사용합니다.


### ✅ XPath 속성 필터링 예시

| XPath 표현식                          | 설명 또는 선택된 노드 예시             |
|--------------------------------------|----------------------------------------|
| `//book[@category='fiction']`        | `category` 속성이 `fiction`인 `book` 노드 선택 |
| `//title[@lang]`                     | `lang` 속성이 존재하는 `title` 노드 선택       |
| `//title[@lang='en']`                | `lang` 속성이 `en`인 `title` 노드 선택         |
| `//*[@id='novel1']`                  | `id` 속성이 `novel1`인 모든 노드 선택          |
| `//book[@category and price > 30]`   | `category` 속성이 존재하고 `price`가 30 초과인 `book` 노드 선택 |


## 🧠 실전 팁
- AND 조건: //book[@category='fiction' and price > 30]
- OR 조건: //book[price < 20 or price > 35]
- 부분 일치: //*[contains(@lang, 'ko')] → lang 속성에 ko가 포함된 노드
- 시작 문자열: //*[starts-with(@id, 'novel')]


