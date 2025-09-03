# Collection 직렬화

C# 코드에서는 Novel 객체 배열을 XML로 직렬화하고 다시 역직렬화하는 과정.

## C# 코드
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
            Novel[] novels = new[]
            {
                new Novel()
                {
                    Author = "제임스 P. 호건",
                    Title = "별의 계승자",
                    Published = 1977,
                },
                new Novel()
                {
                    Author = "제임스 P. 호건",
                    Title = "별의 계승자",
                    Published = 1977,
                },
            };
            var settings = new XmlWriterSettings()
            {
                Encoding = new System.Text.UTF8Encoding(false),
                Indent = true,
                IndentChars = " "
            };
            
            using (var writer = XmlWriter.Create("/Users/jeongjunghwan/Downloads/SampleTest.xml"))
            {
                var serializer = new DataContractSerializer(novels.GetType());
                serializer.WriteObject(writer, novels);
            }
            using (var reader = XmlReader.Create("/Users/jeongjunghwan/Downloads/SampleTest.xml"))
            {
                var serializer = new DataContractSerializer(typeof(Novel[]));
                var novel1 = serializer.ReadObject(reader) as Novel[];
                //Console.WriteLine(novel1);
                foreach (var novel in novel1)
                {
                    Console.WriteLine(novel);   
                }
            }
        }
    }
}

```

## 🧩 C# 기준: DataContractSerializer로 배열 직렬화
```csharp
Novel[] novels = new[] {
    new Novel { Title = "별의 계승자", Author = "제임스 P. 호건", Published = 1977 },
    new Novel { Title = "별의 계승자", Author = "제임스 P. 호건", Published = 1977 }
};

var serializer = new DataContractSerializer(typeof(Novel[]));
serializer.WriteObject(writer, novels); // 객체 배열 → XML
var novel1 = (Novel[])serializer.ReadObject(reader); // XML → 객체 배열
```


- 자동으로 <ArrayOfNovel> 루트 생성
- 각 Novel 객체가 <Novel> 요소로 표현됨
- 매우 간단하고 강력한 직렬화 방식

## ☕ Java: JAXB로 List 직렬화
```java
import javax.xml.bind.annotation.*;
import javax.xml.bind.*;
import java.io.File;
import java.util.*;

@XmlRootElement(name = "Novels")
class NovelList {
    @XmlElement(name = "Novel")
    public List<Novel> novels = new ArrayList<>();
}

@XmlAccessorType(XmlAccessType.FIELD)
class Novel {
    public String title;
    public String author;
    public int published;

    @Override
    public String toString() {
        return "[Title = " + title + ", Author = " + author + ", Published = " + published + "]";
    }
}

public class XMLCollectionTest {
    public static void main(String[] args) throws Exception {
        NovelList list = new NovelList();
        list.novels.add(new Novel() {{ title = "별의 계승자"; author = "제임스 P. 호건"; published = 1977; }});
        list.novels.add(new Novel() {{ title = "별의 계승자"; author = "제임스 P. 호건"; published = 1977; }});

        JAXBContext context = JAXBContext.newInstance(NovelList.class);
        Marshaller marshaller = context.createMarshaller();
        marshaller.setProperty(Marshaller.JAXB_FORMATTED_OUTPUT, true);
        marshaller.marshal(list, new File("SampleTest.xml")); // List → XML

        NovelList loaded = (NovelList) context.createUnmarshaller().unmarshal(new File("SampleTest.xml")); // XML → List
        loaded.novels.forEach(System.out::println);
    }
}

```
Java에서는 @XmlRootElement, @XmlElement 어노테이션으로 컬렉션을 XML에 매핑합니다.


## 🧱 C++: Boost.Serialization로 vector 직렬화
```cpp
#include <boost/archive/xml_oarchive.hpp>
#include <boost/archive/xml_iarchive.hpp>
#include <boost/serialization/vector.hpp>
#include <boost/serialization/nvp.hpp>
#include <fstream>
#include <iostream>
#include <string>
#include <vector>

struct Novel {
    std::string title;
    std::string author;
    int published;

    template<class Archive>
    void serialize(Archive& ar, const unsigned int version) {
        ar & BOOST_SERIALIZATION_NVP(title);
        ar & BOOST_SERIALIZATION_NVP(author);
        ar & BOOST_SERIALIZATION_NVP(published);
    }
};

int main() {
    std::vector<Novel> novels = {
        { "별의 계승자", "제임스 P. 호건", 1977 },
        { "별의 계승자", "제임스 P. 호건", 1977 }
    };

    {
        std::ofstream ofs("SampleTest.xml");
        boost::archive::xml_oarchive oa(ofs);
        oa << BOOST_SERIALIZATION_NVP(novels); // vector → XML
    }

    std::vector<Novel> loaded;
    {
        std::ifstream ifs("SampleTest.xml");
        boost::archive::xml_iarchive ia(ifs);
        ia >> BOOST_SERIALIZATION_NVP(loaded); // XML → vector
    }

    for (const auto& n : loaded)
        std::cout << "[Title = " << n.title << ", Author = " << n.author << ", Published = " << n.published << "]\n";
}

```
Boost는 C++에서 가장 강력한 XML 직렬화 라이브러리 중 하나입니다. vector도 자동 처리됩니다.


## 🐍 Python: ElementTree로 리스트 직렬화
```python
import xml.etree.ElementTree as ET
from dataclasses import dataclass

@dataclass
class Novel:
    title: str
    author: str
    published: int

novels = [
    Novel("별의 계승자", "제임스 P. 호건", 1977),
    Novel("별의 계승자", "제임스 P. 호건", 1977)
]

# 리스트 → XML
root = ET.Element("Novels")
for novel in novels:
    elem = ET.SubElement(root, "Novel")
    ET.SubElement(elem, "Title").text = novel.title
    ET.SubElement(elem, "Author").text = novel.author
    ET.SubElement(elem, "Published").text = str(novel.published)

tree = ET.ElementTree(root)
tree.write("SampleTest.xml", encoding="utf-8", xml_declaration=True)

# XML → 리스트
tree2 = ET.parse("SampleTest.xml")
root2 = tree2.getroot()
loaded = []
for elem in root2.findall("Novel"):
    loaded.append(Novel(
        title=elem.find("Title").text,
        author=elem.find("Author").text,
        published=int(elem.find("Published").text)
    ))

for n in loaded:
    print(n)
```

Python은 수동 매핑이 필요하지만 ElementTree로 간결하게 처리할 수 있어요.


## 🧭 요약 비교
| 언어       | 방식                          | 특징 요약                                               |
|------------|-------------------------------|----------------------------------------------------------|
| **C#**     | `DataContractSerializer`       | 배열 자동 직렬화, 매우 간단하고 강력                     |
| **Java**   | `JAXB` + `List`                | 어노테이션 기반 자동 매핑, `@XmlElement`로 리스트 처리     |
| **C++**    | `Boost.Serialization` + `vector`| 템플릿 기반 자동 직렬화, 고성능, 복잡한 설정 가능         |
| **Python** | `ElementTree` + `list`         | 수동 매핑 필요, 간결하고 직관적인 구조                   |

---


