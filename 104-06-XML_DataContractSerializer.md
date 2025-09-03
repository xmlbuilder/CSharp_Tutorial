#  DataContractSerializer

C# 코드에서는 DataContractSerializer를 사용해 Novel 객체를 XML로 직렬화하고 다시 역직렬화하는 과정.

## 🧩 C# 전체 코드
```csharp
namespace  GrammarTest
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
            var novel = new Novel()
            {
                Author = "제임스 P. 호건",
                Title = "별의 계승자",
                Published = 1977,
            };
            var settings = new XmlWriterSettings()
            {
                Encoding = new System.Text.UTF8Encoding(false),
                Indent = true,
                IndentChars = " "
            };
            
            using (var writer = XmlWriter.Create("/Users/jeongjunghwan/Downloads/SampleTest.xml"))
            {
                var serializer = new DataContractSerializer(novel.GetType());
                serializer.WriteObject(writer, novel);
            }
            using (var reader = XmlReader.Create("/Users/jeongjunghwan/Downloads/SampleTest.xml"))
            {
                var serializer = new DataContractSerializer(typeof(Novel));
                var novel1 = serializer.ReadObject(reader) as Novel;
                Console.WriteLine(novel1);
            }
        }
    }
}
```
## C# 요약 코드
```csharp
var novel = new Novel { Title = "별의 계승자", Author = "제임스 P. 호건", Published = 1977 };
var serializer = new DataContractSerializer(typeof(Novel));
serializer.WriteObject(writer, novel); // XML로 저장
var novel1 = (Novel)serializer.ReadObject(reader); // XML에서 객체 복원
```

- DataContractSerializer 사용
- XMLWriter/Reader로 파일 입출력
- 속성 기반 자동 매핑

## ☕ Java: JAXB (Java Architecture for XML Binding)
```java
import javax.xml.bind.*;
import java.io.File;

@XmlRootElement
class Novel {
    public String title;
    public String author;
    public int published;

    @Override
    public String toString() {
        return "[Title = " + title + ", Author = " + author + ", Published = " + published + "]";
    }
}

public class XMLObjectTest {
    public static void main(String[] args) throws Exception {
        Novel novel = new Novel();
        novel.title = "별의 계승자";
        novel.author = "제임스 P. 호건";
        novel.published = 1977;

        JAXBContext context = JAXBContext.newInstance(Novel.class);
        Marshaller marshaller = context.createMarshaller();
        marshaller.setProperty(Marshaller.JAXB_FORMATTED_OUTPUT, true);
        marshaller.marshal(novel, new File("SampleTest.xml")); // 객체 → XML

        Unmarshaller unmarshaller = context.createUnmarshaller();
        Novel novel1 = (Novel) unmarshaller.unmarshal(new File("SampleTest.xml")); // XML → 객체
        System.out.println(novel1);
    }
}

```
Java에서는 JAXB를 통해 클래스에 어노테이션을 붙여 XML 자동 매핑이 가능합니다.


## 🧱 C++: TinyXML2 + 수동 매핑
```cpp
#include "tinyxml2.h"
#include <iostream>
#include <string>

struct Novel {
    std::string title;
    std::string author;
    int published;
};

int main() {
    Novel novel = { "별의 계승자", "제임스 P. 호건", 1977 };

    tinyxml2::XMLDocument doc;
    auto* root = doc.NewElement("Novel");
    doc.InsertFirstChild(root);

    root->InsertNewChildElement("Title")->SetText(novel.title.c_str());
    root->InsertNewChildElement("Author")->SetText(novel.author.c_str());
    root->InsertNewChildElement("Published")->SetText(novel.published);

    doc.SaveFile("SampleTest.xml"); // 객체 → XML

    // XML → 객체
    tinyxml2::XMLDocument doc2;
    doc2.LoadFile("SampleTest.xml");
    auto* root2 = doc2.FirstChildElement("Novel");

    Novel novel2;
    novel2.title = root2->FirstChildElement("Title")->GetText();
    novel2.author = root2->FirstChildElement("Author")->GetText();
    root2->FirstChildElement("Published")->QueryIntText(&novel2.published);

    std::cout << "[Title = " << novel2.title << ", Author = " << novel2.author
              << ", Published = " << novel2.published << "]" << std::endl;
}

```
C++에서는 TinyXML2 같은 라이브러리를 사용해 수동으로 XML 요소를 매핑해야 해요.


## 🐍 Python: xml.etree.ElementTree + dataclass
```python
import xml.etree.ElementTree as ET
from dataclasses import dataclass

@dataclass
class Novel:
    title: str
    author: str
    published: int

novel = Novel("별의 계승자", "제임스 P. 호건", 1977)

# 객체 → XML
root = ET.Element("Novel")
ET.SubElement(root, "Title").text = novel.title
ET.SubElement(root, "Author").text = novel.author
ET.SubElement(root, "Published").text = str(novel.published)

tree = ET.ElementTree(root)
tree.write("SampleTest.xml", encoding="utf-8", xml_declaration=True)

# XML → 객체
tree2 = ET.parse("SampleTest.xml")
root2 = tree2.getroot()
novel2 = Novel(
    title=root2.find("Title").text,
    author=root2.find("Author").text,
    published=int(root2.find("Published").text)
)

print(novel2)
```



Python은 ElementTree로 간단하게 XML을 생성하고 파싱할 수 있어요. 자동 매핑은 없지만 직관적입니다.


## 🧭 요약 비교
| 언어       | 방식                         | 특징 요약                                               |
|------------|------------------------------|----------------------------------------------------------|
| **C#**     | `DataContractSerializer`      | 속성 기반 자동 직렬화, XMLWriter/Reader 사용              |
| **Java**   | `JAXB`                        | 어노테이션 기반 자동 매핑, `Marshaller`/`Unmarshaller` 사용 |
| **C++**    | `TinyXML2`                    | 수동 매핑 필요, 직접 요소 생성 및 파싱                   |
| **Python** | `ElementTree` + `dataclass`   | 수동 매핑, 간결한 문법, 직관적인 구조                    |

---
