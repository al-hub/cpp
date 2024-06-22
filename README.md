# cpp

## const std:string&
```cpp
#include <iostream>
#include <string>

void printMessage(const std::string& message) {
    std::cout << message << std::endl;
}

int main() {
    std::string greeting = "Hello, World!";
    
    printMessage(greeting); 
    printMessage("Hello, Literal!");
    
    return 0;
}
```

## std::vector, std::map, std::tuple

- 숲 속의 마법 학교에서는 동물들이 각자 마법, 물약, 비행 과목에서 시험을 보았습니다.
- 여우, 곰, 부엉이, 토끼는 열심히 공부하여 좋은 성적을 거두었습니다.
- 학교는 이 동물들의 성적을 관리하기 위해 특별한 성적표 시스템을 도입했습니다.
- 이 시스템은 각 학생의 이름과 과목별 점수를 효율적으로 저장하고 출력하는 기능을 제공합니다.

```cpp
#include <iostream>
#include <vector>
#include <map>
#include <tuple>
#include <string>

// 학생의 성적 정보를 저장하는 데이터 구조
using GradeBook = std::vector<std::map<const std::string, std::tuple<int, int, int>>>;

// 클래스의 성적을 출력하는 함수
void printClassGrades(const std::map<const std::string, std::tuple<int, int, int>>& classGrades) {
    for (const auto& [name, grades] : classGrades) {
        std::cout << name << ": ";
        std::cout << "Magic: " << std::get<0>(grades) << ", ";
        std::cout << "Potion: " << std::get<1>(grades) << ", ";
        std::cout << "Flying: " << std::get<2>(grades) << std::endl;
    }
}

// 성적을 처리하는 템플릿 함수
template <typename T>
void processGrades(const GradeBook& magicGrades, T printFunc) {
    for (const auto& classGrades : magicGrades) {
        printFunc(classGrades);
    }
}

int main() {
    // 성적 데이터 초기화
    GradeBook magicGrades = {
        {
            {"Fox", std::make_tuple(92, 88, 91)},
            {"Bear", std::make_tuple(85, 87, 90)}
        },
        {
            {"Owl", std::make_tuple(95, 94, 97)},
            {"Rabbit", std::make_tuple(88, 86, 89)}
        }
    };

    // 성적 출력 람다 함수 정의
    auto printGrades = [](const std::map<const std::string, std::tuple<int, int, int>>& classGrades) {
        printClassGrades(classGrades);
    };

    // 성적 데이터 처리 및 출력
    processGrades(magicGrades, printGrades);

    return 0;
}

```

## auto magicBox = std::make_shared<MagicBox<std::string>>()

- story
    - 숲 속에는 전설적인 마법 상자가 있었습니다.
    - 이 상자는 무엇이든 넣으면 두 배로 늘어나는 신비한 힘을 가지고 있었습니다.
    - 마을 사람들은 이 상자를 이용해 숲의 자원을 두 배로 늘려 마을을 풍요롭게 하기로 했습니다.
    - 탐험가들은 자원을 모아 상자에 넣고, 상자를 관리하는 마법사를 도왔습니다.

- code
```cpp
#include <iostream>
#include <vector>
#include <string>
#include <map>
#include <unordered_map>
#include <memory>
#include <thread>
#include <mutex>
#include <functional>
#include <tuple>

template <typename T>
class MagicBox {
public:
    void addItem(const T& item) {
        std::lock_guard<std::mutex> lock(mtx_);
        items_.push_back(item); items_.push_back(item); // 두 배로 추가
    }
    void showItems() const {
        for (const auto& item : items_) std::cout << item << " ";
        std::cout << std::endl;
    }
private:
    std::vector<T> items_;
    mutable std::mutex mtx_;
};

template <typename T>
void doubleResources(std::map<T, int>& resources, MagicBox<T>& box) {
    for (auto& resource : resources) {
        box.addItem(resource.first); resource.second *= 2;
    }
}

template <typename T>
void doubleExtraResources(std::unordered_map<T, int>& extraResources, MagicBox<T>& box) {
    for (auto& resource : extraResources) {
        box.addItem(resource.first); resource.second *= 2;
    }
}

int main() {
    auto magicBox = std::make_shared<MagicBox<std::string>>();
    std::map<std::string, int> resources = { {"Wood", 10}, {"Stone", 20} };
    std::unordered_map<std::string, int> extraResources = { {"Gold", 5}, {"Silver", 15} };
    std::pair<std::string, int> specialItem = {"Diamond", 1};
    std::tuple<std::string, int, float> resourceInfo = std::make_tuple("Emerald", 3, 5.5f);

    std::thread t1(doubleResources<std::string>, std::ref(resources), std::ref(*magicBox));
    std::thread t2(doubleExtraResources<std::string>, std::ref(extraResources), std::ref(*magicBox));
    t1.join(); t2.join();

    specialItem.second *= 2;
    std::get<1>(resourceInfo) *= 2;

    std::cout << "MagicBox contains: "; magicBox->showItems();

    std::cout << "Resources: " << std::endl;
    for (const auto& resource : resources) std::cout << resource.first << ": " << resource.second << std::endl;

    std::cout << "Extra Resources: " << std::endl;
    for (const auto& resource : extraResources) std::cout << resource.first << ": " << resource.second << std::endl;

    auto showSpecialItem = [](const std::pair<std::string, int>& item) {
        std::cout << "Special Item: " << item.first << ", Quantity: " << item.second << std::endl;
    };
    showSpecialItem(specialItem);

    std::cout << "Resource Info: " << std::endl;
    std::cout << "Item: " << std::get<0>(resourceInfo) << ", Quantity: " << std::get<1>(resourceInfo)
              << ", Value: " << std::get<2>(resourceInfo) << std::endl;

    return 0;
}

```

- result
```shell
MagicBox contains: Wood Wood Stone Stone Gold Gold Silver Silver 
Resources: 
Wood: 20
Stone: 40
Extra Resources: 
Gold: 10
Silver: 30
Special Item: Diamond, Quantity: 2
Resource Info: 
Item: Emerald, Quantity: 6, Value: 5.5

```

## 순수가상함수
- 오구현 오류
```cpp
// 오류 발생 코드
class Base {
public:
    virtual void func(); // 순수 가상 함수로 선언되지 않음
};

class Derived : public Base {
public:
    void func() override {
        // Derived 클래스에서 func 함수를 구현함
    }
};

int main() {
    Derived d;
    Base* b = &d;
    b->func();
    return 0;
}
```

- 오류 메세지
```shell
/tmp/ccK6ZtMI.o: In function `main':
main.cpp:(.text+0x2f): undefined reference to `Base::func()'
collect2: error: ld returned 1 exit status
```

- 수정
```cpp
// 수정된 코드
class Base {
public:
    virtual void func() = 0; // 순수 가상 함수로 선언
};

class Derived : public Base {
public:
    void func() override {
        // Derived 클래스에서 func 함수를 구현함
    }
};

int main() {
    Derived d;
    Base* b = &d;
    b->func();
    return 0;
}
```

