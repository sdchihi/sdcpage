---
layout: post
title: "Unreal 기초"
categories: Unreal
tags: [GameProgramming]
image:
  feature: atoz.jpg
  teaser: atoz_teaser.jpg
  credit:
  creditlink:
---

## UPROPERTY 선언

- BlueprintReadWrite  : 블루프린트에서 값 조정 가능
- BlueprintReadOnly : 블루프린트에서 값 확인만 가능
- BlueprintCallable : 블루프린트에서 코드 호출이 가능해짐.
- Const : 에디터에서 편집이 불가능하게 됨.
- Category  : 디테일 탭에 설정한 카테고리로 나타난다. ex ) Category = " Damage "
- EditAnywhere  : 해당 프로퍼티를 에디터에 노출시키능 기능
- Transient : 휘발성으로 저장이 안되고 로드시 0으로 채워짐.
- VisibleAnywhere : 프로퍼티가 보이기는 하나 에디터에서 편집이 불가함.

`런타임 변경사항은 프로퍼티값에 반영이 안되는 일이 생기는데 메소드를 #if WITH_EDITOR   -  #endif로 묶었을때 해결된다.`

<br>


## UFUNCTION 선언

- BlueprintImplementableEvent : c++ 코드에서 블루프린트에 정의된 함수를 호출 할 수 있게 함.  // 함수 내용을 블루프린트 내에서 이벤트를 호출해 작성
- BlueprintNativeEvent  : 블루프린트와  c++ 둘 다 이용해서 함수를 작성.
-

> BlueprintNativeEvent 실행 핀이 따로 없을때 쓰고, Spawn Projectile 처럼 실행핀 있는걸 구현할때는
BlueprintImplementableEvent를 사용하면됨.


## 클래스 유형
- UObject
- AActor
- UActorComponent
- UStruct

<br><br>

#### 언리얼 오브젝트 ( UObject )
기본 구성요소가 되는 클래스로 제공하는 서비스는 아래와 같다.
- 프로퍼티와 메소드의 반영
- 프로퍼티의 연동
- 가비지 컬렉션
- 이름으로 오브젝트 찾기
- 프로퍼티에 환경 설정 가능값
- 프로퍼티, 메소드에 네트워크 기능 제공

- 위의 기능을 제공하는것을 리플렉션 시스템이라고 한다.

UObject 에서 파생된 UClass 들은 인스턴스에 대한 모든 메타 데이터가 포함됨.
UObject에서 곧바로 파생한 클래스를 사용할 일은 많지 않다.

#### AActor
- 레벨에 배치할 수 있는 오브젝트는 모두 AActor에 포함된다.
- AActor는 UObject에서 파생된 클래스이다. ( 당연히 UObject가 지원하는 기능이 수행된다. )
- AActor가 소유하고있는 컴포넌트에 대한 정보도 포함하고있기때문에 접근이 가능하다.
- AActor에는 반드시 최소 하나의 `USceneComponent(위치, 회전, 스케일)`가 필요함.

- UWorld::SpawnActor()를 통해 생성되면 BeginPlay  - Tick - Destroy (EndPlay) tns
<br>

#### UActorComponent
- AActor 에 필요한 기능들을 담당하고있음
- 컴포넌트는 다른 컴포넌트에 붙어 계층화시킬수 있음.

<br>
#### UStruct
- 가비지 컬렉션의 대상이 아니라는 점에서 Object와 구분된다.
- 따라서 수명을 직접 관리해줘야 한다.
- 블루프린트에서 만든 구조체는 c++에서 참조가 되지 않는 반면 C++에서 만든 구조체는 블루프린트에서
참조 및 사용이 가능하다.

<hr>
<br>
#### 리플렉션 시스템
리플렉션의 기능을 마크업(프로퍼티)을 통해 선택적으로 사용할 수 있다.
- UCLASS()
- USTRUCT()
- GENERATED_BODY() : 생성되는 전체 필수 표준 코드로 대체
- UPROPERTY() : 기능 선택 가능
- UFUNCTION() : 기능 선택 가능 + RPC(원격 프로시저 호출) 로 사용할 수 있도록 도움. (네트워크 개념)


##### 가비지 컬렉터에 관한 얘기
C++에는 기본적으로 가비지 컬렉터가 존재하지 않지만, 위에서 언급했듯이 UE4의 리플렉션 시스템을
통해 가비지 컬렉터 기능을 제공하고있다. 가비지 컬렉터엔 `루트 세트`라는 것이 있는데 루트 세트에 포함된 오브젝트나 레퍼런스된 오브젝트들은 가비지 컬렉터의 대상이 되지 않습니다. <br>
레퍼런스 되었다 -> UPROPERTY에 저장된 UObject 포인터를 말함.. <br>
- 액터는 가비지 컬렉터의 대상이 되지않는다. 따라서 Destroy를 호출해야함


```c++
//  UE4 공식 홈페이지에 나와있는 예제
UCLASS()
class AMyActor : public AActor
{
    GENERATED_BODY()

public:
    // 루트 세트 오브젝트에 도달 가능
    UPROPERTY()
    MyGCType* SafeObject;

    // 가비지 컬렉터 대상
    MyGCType* DoomedObject;

};
```
가비지 컬렉팅이 이뤄지면 널 포인터로 설정이 된다.<br>
> 주의할 점  : Actor를 제거하기 위해 Destroy()를 호출하면
다음 가비지 컬렉터가 실행될때 제거가 된다. 호출 직후엔 삭제 대기 상태에 들어가는데 이를 확인하기 위한 메소드로 IsPendingKill()메소드가 있으니 확인을 하도록 하자.

가비지 컬렉터를 피하기위한 오브젝트로 FGCObject가 있다.
<br>


####클래스 이름 접두사
지켜주지 않으면 경고, 에러 발생시키니까 지키도록하자.
- Actor : 액터 파생 클래스 접두사는 A
- Object : 오브젝트 파생 클래스 접두사는 U
- Enum : 열거형 접두사는 E
- Interface : 인터페이스 클래스 접두사는 I
- Template : 템플릿 클래스 접두사는 T
- SWidget : 슬레이트 UI 파생 클래스 접두사는 S
- 그외에는 F

#### ETC

<br>
##### FString
- 스트링 작업을 수월하게 하기 위한 메소드가 포함되어 있다.
- TEXT 매크로 사용해야 함.
