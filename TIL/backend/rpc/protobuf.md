# 메시지 타입 정의

## 필드 타입

- int와 string과 같은 scalar type을 지정할 수 도 있고,
- enum 타입을 포함한 composite type도 지정가능하다.
- 날짜 데이터의 경우는 int64로 정의하여 Unix time으로 통신하면 좋다.
    - 4바이트밖에 되지 않아 효율적이다.
    - 2바이트만 할당하지 않는 이유는 2038년 이후로 2바이트를 넘어서기 때문이다.

## 필드 숫자 할당하기

- Message Type을 정할 때에 각 필드마다 고유한 숫자를 정의한다. 이것은 바이너리 형태 메시지에서 필드를 식별하기 위해 사용되며, 메시지가 한 번이라도 사용되었다면 바꾸어서는 안된다.
- 1부터 15까지는 필드 번호와 타입을 포함하여 1바이트를 사용하고, 16부터 2047까지는 2바이트를 사용하므로, 자주 사용하는 메시지에는 1과 15를 할당해서 미리 예약 걸어두는 것이 좋다.
    - 자주 사용하는 메시지가 나중에 추가 될 수 있으니, 미리 남겨두는 것이 좋다.
- 식별자는 1부터 536,870,911까지 사용할 수 있지만, 19000부터 19999까지는 프로토콜 버퍼 구현을 위해 예약되어 있다. (FieldDescriptor::kFirstReservedNumbet ~ FieldDescriptor::kLastReservedNumber)
    - 만약 예약된 식별자를 사용하면 컴파일러에 의해 걸러진다.
- reserved fields를 지정할 수 있는데 여기 지정된 field 들도 마찬가지로 동작한다. ([resverved field](https://developers.google.com/protocol-buffers/docs/proto3#reserved))

## 주석

- 주석은 `/**/` 혹은 `//`로 정의할 수 있다.

## 예약된 필드

- 메시지 타입이 업데이트 되면서 필드가 사라지게 되는 경우, 미래 사용자가 이 필드 번호를 재사용하게 될 수 있다. 이것은 레거시 버전을 불러와 데이터 충돌, 프라이버시 버그 등이 발생할 수 있게 된다.
- 필드의 이름 역시 Json을 직렬화 하는 과정에서 문제가 발생할 수 있다.
- 따라서 삭제된 필드에 대해서는 reserved 를 이용하여 이런 문제가 발생하지 않도록 방지해야한다.
- 참고로 하나의 `reserved` 키워드에 필드 번호와 이름을 섞어 쓸 수 없다.

```go
message Foo {
  reserved 2, 15, 9 to 11;
  reserved "foo", "bar";
}
```

# Type

- 기본형에는 여러가지의 숫자 (double, float, int32, int64, uint32, unit64 ..), bool, string, bytes를 제공한다.
- 메시지가 파싱되었을 때, 만약 인코드된 메시지가 특정 값을 가지고 있지 않다면 해당 필드는 기본 값으로 채워진다.
    - 문자열의 경우 empty string
    - 바이트의 경우 empty bytes
    - boolean의 경우 false
    - 숫자 타입의 경우 0
    - enum의 경우 첫번째 이넘 값 (반드시 0이어야 함)
    - message field의 경우 언어에 따라 달라짐 ([참고](https://developers.google.com/protocol-buffers/docs/reference/overview))
- repeated 필드의 경우 대부분의 언어에서는 빈 배열로 설정된다.
- scalar 타입의 경우 default 값인지 명시적으로 설정된 것인지 알 수 없다.
    - (메시지 타입을 지정할 때 염두해야함)
- 그리고 scalar 타입이 기본값으로 설정된 경우 값은 wire(?)에서 직렬화 되지 않는다.

## enum type

- 예시

    ```
    message SearchRequest {
      string query = 1;
      int32 page_number = 2;
      int32 result_per_page = 3;
      enum Corpus {
        UNIVERSAL = 0;
        WEB = 1;
        IMAGES = 2;
        LOCAL = 3;
        NEWS = 4;
        PRODUCTS = 5;
        VIDEO = 6;
      }
      Corpus corpus = 4;
    }
    ```

- enum 타입에서는 반드시 첫 번째 요소가 0으로 설정되어야 한다.
    - numeric 의 default value이기 때문에 0을 가진 값이 반드시 있어야 한다.
    - 첫번째 이넘 값이 항상 기본값인 proto2와의 호환을 위해 0은 첫번째 요소가 가져야 한다.
- allow_alias 설정을 true로 하면 동일한 값에 대해 다른 별칭을 붙일 수 있다.
    - allow_alias 설정을 false로 한채 동일한 숫자에 대해 다른 별칭을 붙이는 경우 컴파일 에러가 발생한다.

    ```go
    message MyMessage1 {
      enum EnumAllowingAlias {
        option allow_alias = true;
        UNKNOWN = 0;
        STARTED = 1;
        RUNNING = 1;
      }
    }
    message MyMessage2 {
      enum EnumNotAllowingAlias {
        UNKNOWN = 0;
        STARTED = 1;
        // RUNNING = 1;  // Uncommenting this line will cause a compile error inside Google and a warning message outside.
      }
    }
    ```

- enum의 값은 반드시 32bit int 타입이어야 한다.
    - enum의 값은 varint encoding을 사용하기 때문에 음수는 비효율적이라 추천하지 않는다.
- enum은 message 정의와 함께 정의될 수도, message 정의 밖에서 정의 될 수도 있다.
    - 메시지 안에 정의된 enum도 `_MessageType_._EnumType_` 과 같은 문법으로 다른 메시지에서 재사용 될 수 있다.
    - 메시지 밖에 정의된 enum도 다른 message에서 재사용할 수 있다.
- 역직렬화중 인식되지 않은 값은 언어에 따라 다르게 동작한다.
- 다른 타입과 마찬가지로 enum 도 지워져야 한다면 예약을 걸어 후에 재사용되지 않도록 해야한다.
    - to를 통해 범위를 지정할 수 있다. `9 to 11`
    - max를 이용해 최댓값을 나타낼 수 있다. `40 to max`

👆 [https://developers.google.com/protocol-buffers/docs/proto3#reserved_values](https://developers.google.com/protocol-buffers/docs/proto3#reserved_values) 여기까지 정리한 내용

# 서비스

- RPC 시스템을 통해 메시지를 사용하고 싶다면 RPC service 인터페이스를 정의하면 된다.
- protobuf 컴파일러가 서비스 인터페이스 코드와 stubs를 선택한 언어로 생성해준다.

```go
service SearchService {
  rpc Search(SearchRequest) returns (SearchResponse);
}
```