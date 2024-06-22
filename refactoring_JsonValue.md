# Json:Value

## key값은 알고 있으나 value를 모를 때 처리하는 기본방식
```cpp
#include <iostream>
#include <json/json.h>

void processJsonValue(const Json::Value& value) {
    if (value.isNull()) {
        std::cout << "Value is null" << std::endl;
    } else if (value.isBool()) {
        std::cout << "Value is boolean: " << value.asBool() << std::endl;
    } else if (value.isInt()) {
        std::cout << "Value is int: " << value.asInt() << std::endl;
    } else if (value.isUInt()) {
        std::cout << "Value is unsigned int: " << value.asUInt() << std::endl;
    } else if (value.isDouble()) {
        std::cout << "Value is double: " << value.asDouble() << std::endl;
    } else if (value.isString()) {
        std::cout << "Value is string: " << value.asString() << std::endl;
    } else if (value.isArray()) {
        std::cout << "Value is array: " << std::endl;
        for (const auto& item : value) {
            processJsonValue(item);
        }
    } else if (value.isObject()) {
        std::cout << "Value is object: " << std::endl;
        for (const auto& key : value.getMemberNames()) {
            std::cout << "Key: " << key << ", ";
            processJsonValue(value[key]);
        }
    } else {
        std::cout << "Unknown value type" << std::endl;
    }
}

int main() {
    Json::Value root;

    // 다양한 타입의 값 추가
    root["nullValue"] = Json::Value::null;
    root["boolValue"] = true;
    root["intValue"] = 42;
    root["uintValue"] = 123u;
    root["doubleValue"] = 3.14;
    root["stringValue"] = "hello";
    root["arrayValue"].append(1);
    root["arrayValue"].append("two");
    root["objectValue"]["nestedKey"] = "nestedValue";

    // 모든 키-값 쌍을 순회하며 타입 확인 및 처리
    for (const auto& key : root.getMemberNames()) {
        std::cout << "Key: " << key << ", ";
        processJsonValue(root[key]);
    }

    return 0;
}
```

## refactoring 후 (다형성)
```cpp
#include <iostream>
#include <json/json.h>
#include <memory>
#include <vector>

// JSON 값을 처리하는 추상 기본 클래스
class JsonValueHandler {
public:
    virtual ~JsonValueHandler() = default;
    virtual void handle(const Json::Value& value) const = 0;
};

// JSON Null 값 처리 클래스
class NullValueHandler : public JsonValueHandler {
public:
    void handle(const Json::Value& value) const override {
        std::cout << "Value is null" << std::endl;
    }
};

// JSON Boolean 값 처리 클래스
class BoolValueHandler : public JsonValueHandler {
public:
    void handle(const Json::Value& value) const override {
        std::cout << "Value is boolean: " << value.asBool() << std::endl;
    }
};

// JSON Integer 값 처리 클래스
class IntValueHandler : public JsonValueHandler {
public:
    void handle(const Json::Value& value) const override {
        std::cout << "Value is int: " << value.asInt() << std::endl;
    }
};

// JSON Unsigned Integer 값 처리 클래스
class UIntValueHandler : public JsonValueHandler {
public:
    void handle(const Json::Value& value) const override {
        std::cout << "Value is unsigned int: " << value.asUInt() << std::endl;
    }
};

// JSON Double 값 처리 클래스
class DoubleValueHandler : public JsonValueHandler {
public:
    void handle(const Json::Value& value) const override {
        std::cout << "Value is double: " << value.asDouble() << std::endl;
    }
};

// JSON String 값 처리 클래스
class StringValueHandler : public JsonValueHandler {
public:
    void handle(const Json::Value& value) const override {
        std::cout << "Value is string: " << value.asString() << std::endl;
    }
};

// JSON Array 값 처리 클래스
class ArrayValueHandler : public JsonValueHandler {
public:
    void handle(const Json::Value& value) const override {
        std::cout << "Value is array: " << std::endl;
        for (const auto& item : value) {
            JsonValueProcessor::process(item);  // 재귀 호출
        }
    }
};

// JSON Object 값 처리 클래스
class ObjectValueHandler : public JsonValueHandler {
public:
    void handle(const Json::Value& value) const override {
        std::cout << "Value is object: " << std::endl;
        for (const auto& key : value.getMemberNames()) {
            std::cout << "Key: " << key << ", ";
            JsonValueProcessor::process(value[key]);  // 재귀 호출
        }
    }
};

// JSON 값 처리기 클래스
class JsonValueProcessor {
public:
    static void process(const Json::Value& value) {
        if (value.isNull()) {
            nullHandler.handle(value);
        } else if (value.isBool()) {
            boolHandler.handle(value);
        } else if (value.isInt()) {
            intHandler.handle(value);
        } else if (value.isUInt()) {
            uintHandler.handle(value);
        } else if (value.isDouble()) {
            doubleHandler.handle(value);
        } else if (value.isString()) {
            stringHandler.handle(value);
        } else if (value.isArray()) {
            arrayHandler.handle(value);
        } else if (value.isObject()) {
            objectHandler.handle(value);
        } else {
            std::cout << "Unknown value type" << std::endl;
        }
    }

private:
    static NullValueHandler nullHandler;
    static BoolValueHandler boolHandler;
    static IntValueHandler intHandler;
    static UIntValueHandler uintHandler;
    static DoubleValueHandler doubleHandler;
    static StringValueHandler stringHandler;
    static ArrayValueHandler arrayHandler;
    static ObjectValueHandler objectHandler;
};

// Static 멤버 초기화
NullValueHandler JsonValueProcessor::nullHandler;
BoolValueHandler JsonValueProcessor::boolHandler;
IntValueHandler JsonValueProcessor::intHandler;
UIntValueHandler JsonValueProcessor::uintHandler;
DoubleValueHandler JsonValueProcessor::doubleHandler;
StringValueHandler JsonValueProcessor::stringHandler;
ArrayValueHandler JsonValueProcessor::arrayHandler;
ObjectValueHandler JsonValueProcessor::objectHandler;

int main() {
    Json::Value root;

    // 다양한 타입의 값 추가
    root["nullValue"] = Json::Value::null;
    root["boolValue"] = true;
    root["intValue"] = 42;
    root["uintValue"] = 123u;
    root["doubleValue"] = 3.14;
    root["stringValue"] = "hello";
    root["arrayValue"].append(1);
    root["arrayValue"].append("two");
    root["objectValue"]["nestedKey"] = "nestedValue";

    // 모든 키-값 쌍을 순회하며 타입 확인 및 처리
    for (const auto& key : root.getMemberNames()) {
        std::cout << "Key: " << key << ", ";
        JsonValueProcessor::process(root[key]);
    }

    return 0;
}
```

## 장점
- 단일 책임 원칙 준수
  - 각 타입별로 별도의 핸들러 클래스를 정의하여 processJsonValue 함수의 책임을 분산시켰습니다.
- 확장성 향상
  - 새로운 타입을 추가하려면 단순히 새로운 핸들러 클래스를 추가하고 JsonValueProcessor에 등록하면 됩니다.
- 가독성 향상
  - 조건문이 제거되고, 각 타입별 처리 로직이 별도의 클래스에 캡슐화되어 코드의 가독성이 크게 향상되었습니다.
- 다형성 활용
  - 다형성을 활용하여 각 타입별 처리 로직을 캡슐화하고, JsonValueProcessor가 일관된 인터페이스를 통해 이를 처리하도록 했습니다.
- 클린코드 원칙
  - 단일 책임 원칙 (Single Responsibility Principle)
    - 리팩토링 이후 각 클래스는 한 가지 책임만 가지며, 이는 코드의 변경이 한 곳에서만 일어나도록 합니다.
  - 개방-폐쇄 원칙 (Open/Closed Principle)
    - 리팩토링 후 코드가 확장에는 열려 있고 수정에는 닫혀 있습니다.
    - 새로운 타입의 핸들러를 추가할 때 기존 코드를 수정할 필요가 없습니다.
  - 캡슐화 (Encapsulation)
    - 각 타입별 처리 로직이 별도의 클래스로 캡슐화되어, 코드의 모듈화가 잘 이루어졌습니다.
  - 다형성 (Polymorphism)
    - 다형성을 활용하여 각 타입별 처리 로직을 공통 인터페이스로 처리할 수 있게 되었습니다.
  - 가독성 (Readability)
    - 중복 코드가 제거되고, 코드 구조가 명확해져 가독성이 크게 향상되었습니다.

## 단점
- 01 복잡성 증가:
  - 코드의 구조가 복잡해질 수 있으며, 특히 작은 프로젝트나 간단한 작업에서는 불필요하게 복잡해 보일 수 있습니다.
- 02 추가 클래스 및 파일 관리 필요:
  - 각 타입에 대해 별도의 클래스를 정의해야 하므로 파일 수가 늘어나고, 이를 관리해야 하는 부담이 증가할 수 있습니다.
- 03 성능 저하 가능성:
 - 다형성과 추상화를 사용하는 것은 추가적인 함수 호출을 발생시켜 성능 저하를 일으킬 수 있습니다.
 - 특히 성능이 중요한 애플리케이션에서는 문제가 될 수 있습니다.
- 04 메모리 사용 증가:
  - 각 핸들러 클래스가 생성되고 유지되어야 하므로 메모리 사용량이 증가할 수 있습니다.
- 05 학습 곡선:
  - 다형성, 인터페이스, 추상 클래스 등의 개념에 익숙하지 않은 개발자들에게는 학습 곡선이 존재할 수 있습니다.
- 06 디버깅 어려움:
  - 추상화 계층이 많아지면 디버깅이 더 어려워질 수 있습니다.
  - 문제가 발생했을 때 실제 로직을 찾는 데 시간이 더 걸릴 수 있습니다.
- 07 초기 개발 시간 증가:
  - 처음 구현할 때 더 많은 시간이 걸릴 수 있습니다.
  - 간단한 코드에 비해 설계와 구현에 더 많은 시간이 필요합니다.
- 08 오버엔지니어링:
  - 필요 이상으로 복잡한 구조를 설계하는 것은 작은 프로젝트에서는 과도한 설계로 간주될 수 있습니다.
- 09 테스트 복잡성:
  - 각 핸들러 클래스에 대한 개별적인 테스트가 필요하여, 테스트 코드도 복잡해질 수 있습니다.
- 10 코드 일관성 문제:
  - 여러 클래스로 분리된 코드가 일관성을 유지하기 어렵고, 프로젝트 내 다른 부분과의 일관성 유지가 어려울 수 있습니다.
