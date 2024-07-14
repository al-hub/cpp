C++11과 C++20의 주요 기능을 상세하게 설명하고, 각각의 예시 코드를 제공하겠습니다. 이 예제들은 난이도를 높여 심화된 내용을 다루도록 구성했습니다.

### C++11의 주요 기능 학습

#### 자동 타입 추론(auto)
**내용:** 
자동 타입 추론(auto)은 변수 선언 시 컴파일러가 변수의 타입을 자동으로 추론하는 기능입니다. 이를 통해 코드의 가독성이 향상되고, 복잡한 타입 선언을 피할 수 있습니다.

**예시:**
```
auto x = 42; // int 타입으로 자동 추론
auto y = 3.14; // double 타입으로 자동 추론
auto str = "Hello, World!"; // const char* 타입으로 자동 추론

std::vector<int> vec = {1, 2, 3, 4, 5};
auto it = vec.begin(); // std::vector<int>::iterator 타입으로 자동 추론
for (auto& elem : vec) { // 각 요소에 대한 참조로 자동 추론
    elem *= 2;
}
```

#### 람다 표현식(lambda expressions)
**내용:**
람다 표현식은 간결한 방식으로 함수 객체를 표현할 수 있게 해줍니다. 이를 통해 간단한 일회용 함수나 콜백 함수를 쉽게 작성할 수 있습니다.

**예시:**
```
auto add = [](int a, int b) { return a + b; };
std::cout << add(2, 3) << std::endl; // 5 출력

std::vector<int> vec = {1, 2, 3, 4, 5};
std::for_each(vec.begin(), vec.end(), [](int n) { std::cout << n << " "; }); // 1 2 3 4 5 출력

auto lambda_with_capture = [vec](int n) -> int {
    int sum = 0;
    for (auto v : vec) {
        sum += v * n;
    }
    return sum;
};
std::cout << lambda_with_capture(2) << std::endl; // 2배한 값들의 합 출력
```

#### 범위 기반 for 루프(range-based for loops)
**내용:**
범위 기반 for 루프는 컨테이너의 모든 요소를 쉽게 순회할 수 있도록 해줍니다. 이를 통해 코드가 간결하고 명확해집니다.

**예시:**
```
std::vector<int> vec = {1, 2, 3, 4, 5};
for (int x : vec) {
    std::cout << x << " ";
}
// 1 2 3 4 5 출력

std::map<std::string, int> map = {{"one", 1}, {"two", 2}, {"three", 3}};
for (const auto& [key, value] : map) {
    std::cout << key << ": " << value << std::endl;
}
// key-value 쌍 출력
```

#### 스마트 포인터(smart pointers)
**내용:**
스마트 포인터는 메모리 관리가 자동으로 이루어지도록 도와주는 객체입니다. 이를 통해 메모리 누수를 방지하고, 코드의 안전성을 높일 수 있습니다. C++11에서는 `std::unique_ptr`, `std::shared_ptr`, `std::weak_ptr` 등이 도입되었습니다.

**예시:**
```
std::unique_ptr<int> p1 = std::make_unique<int>(42);
std::cout << *p1 << std::endl; // 42 출력

std::shared_ptr<int> p2 = std::make_shared<int>(100);
std::shared_ptr<int> p3 = p2; // p2와 p3는 같은 메모리를 공유
std::cout << *p3 << std::endl; // 100 출력

std::weak_ptr<int> wp = p2; // p2의 참조를 약하게 보관
if (auto sp = wp.lock()) { // shared_ptr로 승격 가능 여부 확인
    std::cout << *sp << std::endl; // 100 출력
}
```

#### 스레딩(threading)
**내용:**
C++11에서는 표준 라이브러리에 스레드 지원이 추가되었습니다. 이를 통해 멀티스레드 프로그래밍이 가능해지고, 병렬 처리를 쉽게 구현할 수 있습니다.

**예시:**
```
#include <thread>
#include <vector>
#include <iostream>
#include <numeric>

void print_hello() {
    std::cout << "Hello, World!" << std::endl;
}

int main() {
    std::thread t(print_hello);
    t.join(); // 스레드가 종료될 때까지 대기

    std::vector<int> data(1000, 1);
    auto sum = [](const std::vector<int>& v) {
        return std::accumulate(v.begin(), v.end(), 0);
    };

    std::thread t1(sum, std::ref(data));
    std::thread t2(sum, std::ref(data));

    t1.join();
    t2.join();

    return 0;
}
```

### C++20의 주요 개념 학습

#### 코루틴(Coroutines)
**내용:**
코루틴은 비동기 프로그래밍을 지원하는 기능으로, 함수 실행을 일시 중단하고 재개할 수 있습니다. 이를 통해 비동기 작업을 효율적으로 처리할 수 있습니다.

**예시:**
```
#include <iostream>
#include <coroutine>
#include <future>
#include <thread>

std::future<void> example() {
    co_await std::async(std::launch::async, [] { std::cout << "Hello from coroutine\n"; });
    std::cout << "Resumed after async call\n";
}

int main() {
    auto future = example();
    future.get();
    return 0;
}
```

#### 개념(Concepts)
**내용:**
개념은 템플릿의 타입 요구사항을 명확히 정의할 수 있는 기능입니다. 이를 통해 템플릿 인수의 타입 제약을 명확히 하고, 더 나은 컴파일 에러 메시지를 제공합니다.

**예시:**
```
#include <concepts>
#include <iostream>
#include <type_traits>

template<typename T>
concept Addable = requires(T a, T b) {
    { a + b } -> std::same_as<T>;
};

template<Addable T>
T add(T a, T b) {
    return a + b;
}

template<typename T>
concept Integral = std::is_integral_v<T>;

template<Integral T>
T factorial(T n) {
    T result = 1;
    for (T i = 1; i <= n; ++i) {
        result *= i;
    }
    return result;
}

int main() {
    std::cout << add(1, 2) << std::endl; // 3 출력
    std::cout << factorial(5) << std::endl; // 120 출력
    // std::cout << add(1, "2") << std::endl; // 컴파일 에러 발생
    return 0;
}
```

#### 범위 라이브러리(Ranges Library)
**내용:**
범위 라이브러리는 STL 알고리즘과 컨테이너를 더 쉽게 조합할 수 있도록 해줍니다. 이를 통해 데이터 필터링, 변환 등의 작업을 간결하게 표현할 수 있습니다.

**예시:**
```
#include <iostream>
#include <vector>
#include <ranges>
#include <algorithm>

int main() {
    std::vector<int> vec = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
    auto even_numbers = vec | std::views::filter([](int i) { return i % 2 == 0; });
    auto squared = even_numbers | std::views::transform([](int i) { return i * i; });

    for (int x : squared) {
        std::cout << x << " "; // 4 16 36 64 100 출력
    }

    return 0;
}
```

#### 모듈(Modules)
**내용:**
모듈은 대규모 프로젝트에서 빌드 시간을 단축하고 컴파일 속도를 향상시키기 위한 기능입니다. 모듈 시스템을 사용하면 헤더 파일의 복잡성을 줄일 수 있습니다.

**예시:**
```
// my_module.ixx (모듈 인터페이스 파일)
export module my_module;

export void hello() {
    std::cout << "Hello, World!\n";
}

// main.cpp
import my_module;

int main() {
    hello();
    return 0;
}
```

#### 삼중 비교 연산자(Three-way Comparison Operator, <=>)
**내용:**
삼중 비교 연산자는 전체 순서 비교를 위한 새로운 연산자입니다. 이를 통해 두 값을 비교하고, 결과를 하나의 표현식으로 얻을 수 있습니다.

**예시:**
```
#include <iostream>
#include <compare>
#include <vector>
#include <algorithm>

struct Person {
    std::string name;
    int age;

    auto operator<=>(const Person& other) const = default;
};

int main() {
    Person alice{"Alice", 30};
    Person bob{"Bob", 25};

    if (auto result = alice <=> bob; result > 0) {
        std::cout << "Alice is older than Bob\n";
    } else if (result < 0) {
        std::cout << "Alice is younger than Bob\n";
    } else {
        std::cout << "Alice and Bob are the same age\n";
    }

    std::vector<Person> people = {alice, bob, {"Charlie", 35}};
    std::sort(people.begin(), people.end());

    for (const auto& person : people) {
        std::cout << person.name << " (" << person.age << ")\n";
    }

    return 0;
}
```

이러한 C++11과 C++20의 주요 기능들을 이해하고 실습해보면, C++의 최신 기능들을 효과적으로 활용할 수 있게 될 것입니다. 꾸준한 실습과 프로젝트 적용을 통해 프로그래밍 실력을 향상시킬 수 있습니다.
