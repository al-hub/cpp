## std:map 기초
- 입출력
```cpp
#include <iostream>
#include <map>
#include <string>

int main() {
    std::map<std::string, int> myMap;

    // 대입 연산자를 사용한 데이터 추가 (업데이트 동작)
    myMap["Alice"] = 25;  // 새로운 삽입
    myMap["Alice"] = 30;  // 값 업데이트

    // insert 함수를 사용한 데이터 추가 (삽입 무시)
    auto result1 = myMap.insert(std::make_pair("Bob", 40));
    auto result2 = myMap.insert(std::make_pair("Bob", 50)); // 무시됨

    // emplace 함수를 사용한 데이터 추가 (삽입 무시)
    auto result3 = myMap.emplace("Charlie", 60);
    auto result4 = myMap.emplace("Charlie", 70); // 무시됨

    // 데이터 출력
    for (const auto& entry : myMap) {
        std::cout << entry.first << ": " << entry.second << std::endl;
    }

    // 삽입 결과 확인
    std::cout << "Insert Bob first time: " << (result1.second ? "Success" : "Failed") << std::endl;
    std::cout << "Insert Bob second time: " << (result2.second ? "Success" : "Failed") << std::endl;
    std::cout << "Emplace Charlie first time: " << (result3.second ? "Success" : "Failed") << std::endl;
    std::cout << "Emplace Charlie second time: " << (result4.second ? "Success" : "Failed") << std::endl;

    return 0;
}
```
- 결과
```shell
Alice: 30
Bob: 40
Charlie: 60
Insert Bob first time: Success
Insert Bob second time: Failed
Emplace Charlie first time: Success
Emplace Charlie second time: Failed
```
