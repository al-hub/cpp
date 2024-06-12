# cpp

## std::

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


