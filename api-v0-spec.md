# API 사양 설명서 버전 0 #

**Korean version.**

최종수정일 : 2014년 9월 2일.

본 문서는 [sms4wp.com](http://www.sms4wp.com)의 버전 0 API 사양을 기술합니다.
* * * 

## 개괄 ##

### API 티어 소개 ###

API 측면에서 보았을 때 sms4wp.com은 2가지 계층으로 나누어 설명할 수 있습니다.

* 1차 티어 (1st-tier)
* 2차 티어 (2st-tier)

1차 티어는 문자메시지를 보내기 위한 직접적인 API 계층입니다. 이 계층의 API는 통신사, 혹은 통신사의 문자 서비스와 직접적으로 관련된 곳에서 해당 통신사를 경유하는 메시지를 보내기 위한 절차를 일컫습니다. 다시 말해, 이 곳의 API는 우리가 *수정할 수 없으며 단지 이 곳의 API를 사용할 뿐입니다*. 

2차 티어는 sms4wp가 서비스를 운용하면서 고객들에게 배포하는 우리들의 API 집합입니다. 본 문서는 이 2차 티어 API의 사양에 대해 설명합니다.


### 서비스 레이어 소개 ###

전반적인 관점에서 서비스는 3개의 계층으로 나뉘어집니다.

* 1차 계층 (1st-layer): 서비스 제공자 계층 (Service provider layer)
* 2차 계층 (2nd-layer): 서버 계층 (Server layer)
* 3차 계층 (3rd-layer): 사용자 계층 (Client layer)

서비스 제공자 계층(service provider layer)는 1차 API 티어를 제공하는 계층을 일컫습니다. 이 곳 또한 우리가 직접적으로 관리가 가능한 계층이 아닙니다.

서버 계층(server layer)은 sms4wp.com의 서비스를 제공하는 계층입니다. 서버 계층은 다시 프론트엔드(frontend)와 백엔드(backend)로 나뉩니다.

* 프론트엔드(frontend): 사용자의 전면에 나서서 서비스를 직접적으로 제공하는 인터페이스 계층입니다. 쉽게 말하면 눈에 보이는 대표 웹사이트([sms4wp.com](http://www.sms4wp.com))를 말합니다.
* 백엔드(backend): 사용자에게 직접적으로 제시되지는 않으나 API를 처리하는 서비스의 핵심 계층입니다.

사용자 계층(client layer)은 문자 서비스를 직접 이용하는 사용자를 위한 '어플리케이션 영역'입니다. 본 문서에는 '사용자(user)'와 '클라이언트(client)' 두 용어를 다른 의미로 사용합니다.

* 사용자(user): 서비스를 사용하는 사람입니다.
* 클라이언트(client): 사람이 사용하는 어플리케이션, 즉 사람이 아닌 프로그램을 말하니다.



## 데이터 전송 ##

주로 백엔드 서버 계층으로 데이터 전송이 이루어집니다. 이 때 [REST](http://en.wikipedia.org/wiki/Representational_state_transfer) 방식의 API를 사용합니다.


### Methods ###

HTTP method로 PUT, GET, POST, DELETE 이 네 가지를 주로 사용합니다. 이 네 메소드는 CRUD (Create - Retrieve - Update - Delete) 네 작업과 1:1로 대응합니다.

#### PUT ####
주로 새로운 정보를 전달(create)하는 경우에 사용합니다. 단, create와 update 작업이 둘 다 이루어져야 하는 API의 경우에만 사용합니다.

#### POST ####
주로 새로운 정보를 전달하거나, 기존 정보를 수정하는 경우에 사용합니다. 단 해당 API가 create만을 필요로 하는 경우에는 POST 메소드가 create 작업을 맡지만, API가 *create와 update를 둘 다 지원해야 하는 경우에는* PUT이 create를, POST가 update를 처리하도록 합니다.

#### GET ####
데이터를 조회할 때 사용합니다. 파라미터가 없을 경우 해당 데이터의 전부를 리턴하며, 파라미터를 통해 데이터의 범위를 조절할 수 있도록 합니다.

#### DELETE ####
데이터를 삭제할 때 사용합니다. 일부 데이터는 외래 키 제약에 의해 다음과 같은 작업이 발생할 수 있다.

 1. 삭제가 불가능한 경우가 있을 수 있다. 이 경우 외래키의 레코드를 먼저 삭제해야만 한다.
 2. 데이터가 삭제되면서 외래키의 레코드 또한 같이 동시에 삭제될 수 있다.


## 인증 ##
인증은 http(s) 헤더 "AUTHENTICATION"을 사용합니다. 헤더의 값은 'token' 이란 문자열, 그리고 사용자의 이메일과 인증 토큰를 콜론(:)으로 연결한 문자열입니다 'token' 문자열과는 1개의 공백으로 분리되어 있어야 합니다.

예를 들어 사용자의 이메일이 'bob@email.com'이고 인증 토큰가 'secretkey' 라고 하면 인증을 위해 request header에 다음과 같은 헤더를 추가해 인증합니다.

`AUTHENTICATION: token bob@email.com:secretkey`



## 리턴 값 ##
백엔드 답신은 JSON 형태로 전달되며, 작업의 성공, 혹은 실패 여부는 HTTP response code를 보고 판단합니다. 예를 들어 서버로부터 HTTP response code 200이 전달되었다면 성공적인 응답이 왔음을 의미합니다.

관례적으로 다음과 같은 HTTP response code를 문맥에 따라 사용합니다.

 응답 코드 | 문자열                | 설명                                                        
-------- | ------------------- | -----------------------------------------------------------
200      |OK                   | 성공.                                                        
400      | BAD REQUEST         | 파라미터 값의 에러 등의 요청 프로토콜이 맞지 않는 경우를 의미한다.
403      | FORBIDDEN           | 인증되지 않음. 인증 토큰을 다시 확인해 보아야 한다.
404      | NOT FOUND           | 해당 파라미터의 프로토콜은 올바르나, 서버에 자원이 없거나 찾을 수 없는 경우를 의미한다.
405      | METHOD NOT ALLOWED  | 해당 API에서는 제시된 METHOD가 지원되지 않음을 의미한다.
406      | NOT ACCEPTABLE      | 해당 API를 진행힐 수 없는 경우를 뜻한다. 일례로 SMS를 보내려고 하나, 포인트가 부족한 경우를 들 수 있다.
415      | UNSUPPORTED MEDIA TYPE |  벌크 메시지, 멀티미디어 메시지 등으로 첨부된 파일이 지원되지 않는 경우이다.
500      | INTERNAL SEVER ERROR   | 해당 API를 처리하는 중 백엔드에서 프로그램 에러가 일어난 경우.
501      | NOT IMPLEMENTED        | 해당 API는 아직 완전히 구현되지 않았음.

HTTP content로 전달되는 JSON에는 최소한 하나의 오브젝트가 포함되어 있습니다. 보통 이 오브젝트는 'detail'이란 이름의 key를 포함하고 있으며 이 키의 값은 서버에서 전달하는 보다 자세한 메시지입니다. 이외의 카-값들은 API의 종류에 따라 상이하며 아래 [API 정의](#api_def)에서 설명합니다.



## [API 정의](id:api_def) ##

### 일러 두기 ###
  1. 각 API의 명령은 URL로 구분합니다. 해당 명령의 전체 경로는 _URL_ROOT_와 해당 경로의 문자열을 합친 것입니다.
     * 현재 개발 서버의 URL_ROOT: ``http://backend.sms4wp.com/api/v0``
  2. 각 API의 경로 끝에는 반드시 슬래시(/)를 붙여야 합니다.

### Connection Check API - Who Am I ###

경로: ``/whoami/``

단순 연결 체크를 위한 API. 연결에 성공한 경우 백엔드에 저장된 자신의 유저 정보를 리턴한다.

  - method: GET
  - parameter 없음.


경로: ``/test_task/``

백엔드의 분산 처리 시스템의 정상 동작을 체크하기 위한 API. 백엔드에 단순한 곱셈 연산을 수행시켜 본다. 관리자 계정만 수행 가능한 테스트이다.

  - method: GET
  - parameter
    - x: 정수
    - y: 정수
  - return
    - value: 곱셈의 값.



### Authentication API ###

인증 토큰 관련 API. 관리자 권한으로만 이용 가능하다.

#### Granting ####
인증 토큰을 발행하는 API. scope 값이 이미 기록되어 있는 경우 기존의 인증 토큰이 삭제되고 새로운 토큰으로 갱신된다.

  - 경로: ``/auth_token/grant/``
  - method: POST
    - id:     필수. 백엔드 사용자 id.
    - scope:  필수. 인증 토큰을 구분하는 태그의 개념이다. 이 파라미터의 값으로는 255자 미만의 문자열을 받는다.
              이 문자열은 모든 데이터베이스 상에서 유일해야 하므로, 플러그인이 설치될 해당 사이트의 도메인으로 정할 것을 권장한다.

#### Revoking ####
인증된 토큰을 회수하는 API. Revoke 된 이후에는 해당 인증 토큰을 사용할 수 없다. 그러나 데이터베이스 상에서 해당 토큰의 레코드가 삭제되는 것은 아니다.

  - 경로: ``/auth_token/revoke/``
  - method: GET
    - id: 필수. 백엔드 사용자의 id.

#### Removing ####
토큰을 삭제하는 API. API는 grant, revoke 여부와는 관계 없이 무조건적으로 토큰을 삭제한다.
만일 토큰이 삭제되면 토큰과 관련된 모든 메시지 내역과 결과 자료가 사라질 수 있으니 사용에는 주의해야 한다.

  - 경로: ``/auth_token/remove/``
  - method: DELETE
    - id: 필수. 백엔드 사용자의 id.


### User Management API ###
유저의 등록 및 관리를 위한 API.

경로: ``/user/``

#### Adding a User ####
새로운 유저를 백엔드에 등록한다. 유저가 프론트 엔드 사이트에 가입 승인 되었을 때 백엔드로 호출해야 한다.
이 작업은 관리자의 인증 토큰만이 가능한다.

  - method: PUT
  - parameters:
    - membership_type:  필수. 백엔드에 등록된 프론트 엔드 사이트 명.
    - membership_id:    필수. 해당 프론트 엔드 사이트의 유저를 식별할 수 있는 primary key.
    - email:            필수. 해당 프론트 엔드 사이트로 등록된 이메일 주소.
    - group_name        필수. 해당 유저의 그룹. 이를테면, 'admin', 'user'
  - returns:
    - id: 데이터베이스에 생성된 아이디 번호.

#### Update a User ####
기존 유저의 정보를 수정한다. 유저의 프로필이 수정될 경우 백엔드로 호출해야 한다.
이 작업은 관리자의 인증 토큰만이 가능한다.

  - method: POST
  - parameters:
    - id:               필수. 백엔드의 id. 해당 프론트 엔드 사이트의 유저 id 와는 다르므로 주의한다.
    - membership_type:  선택. 교체하고 싶은 프론트 엔드 사이트 명. 생략할 경우 변경하지 않음.
    - membership_id:    선택. 변경을 원하는 프론트 엔드 유저 primary key. 생략할 경우 변경하지 않음.
    - email:            선택. 변경을 원하는 이메일 주소. 생략할 경우 변경하지 않음.
    - group_name        선택. 해당 유저의 그룹. 이를테면, 'admin', 'user'
    - status_code       선택. 해당 유저의 상태 코드. 0 이상의 정수.
    - 선택 필드 중 적어도 1개는 명시되어야 하며, 그렇지 않으면 400 BAD REQUEST를 리턴한다.

#### Remove a User ####
백엔드에 등록된 유저를 삭제한다. 유저가 프론트 엔드 사이트를 탈퇴할 때 백엔드로 호출해야 한다.
이 작업은 관리자의 인증 토큰만이 가능한다.

  - method: DELETE
  - parameters:
    - id: 필수. 백엔드의 id. 해당 프론트 엔드 사이트의 유저 id 와는 다르므로 주의한다.

#### List or Get User(s) ####
백엔드에 등록된 유저.
이 작업은 관리자의 인증 토큰이 필요하다.

  - method: GET
  - parameters:
    - id:              선택. 백엔드의 id. 해당 프론트 엔드 사이트의 유저 id 와는 다르므로 주의한다.
    - membership_type: 선택. 조회하고 싶은 프론트 엔드 사이트 이름.
    - membership_id:   선택. 조회하고 싶은 프론트 엔드 사이트의 유저 primary key.
    - email:           선택. 조회하고 싶은 이메일 주소.
    - group_name:      선택. 조회하고 싶은 백엔드 유저 그룹.
    - status_code:     선택. 조회하고 싶은 상태 코드.
    - 선택 필드 중 적어도 1개는 명시되어야 하며, 그렇지 않으면 400 BAD REQUEST를 리턴한다.
    - 파라미터는 모두 AND 조건으로 조회된다.
    - membership_id 항목이 제시되었을 때에는 반드시 membership_type 항목이 같이 제시되어야 한다. 그렇지 않으면 400 BAD REQUEST를 리턴한다.
  - returns:
    - 사용자의 목록. 단 1명일지라도 리스트로 반환됩니다.
    - id: 백엔드의 아이디 (pk)
    - membership_type: 멤버십 타입 아이디 (fk)
    - registered_timestamp: 등록일자
    - membership_id: 멤버십 아이디 (해당 프론트엔드 유저 pk)
    - email: 등록된 이메일
    - user_group: 유저 그룹 아이디 (fk)
    - user_status: 유저 상태 아이디 (fk)



### Messaging API ###

#### Sending ####
  - 경로: ``/messaging/``
  - 메시지를 전송하는 API.
  - method: POST
  - parameters
    - sender_phone:    필수. 송신자 전화번호.
    - message_body:    필수. 메시지 본문.
    - receivers:       필수. JSON 그대로 문자열화하여 보낸다 이 JSON은 'name', 'number'를 키(string)으로 하는 object의 array이다
                           예) [{"name": "john", "number": "555-1234"}, {"name": "bob", "number": "555-5678"}]

    - send_date:       선택. 전송일자. YYYY-MM-DD HH:mm:SS+TZ 형식을 지킨다. 만일 +TZ 부분이 생략되면 UTC로 간주한다.
                       생략될 경우 즉시 전송된다. 예) 2014-08-31 18:23:00+0900
    - sender_name:     선택. 송신자의 이름.
    - message_subject: 선택. 메시지 제목. 주로 SMS에서는 제목이 보이지 않으나 LMS, MMS의 경우에는 단말기에서 표시된다.
    - is_test:         선택. 실제 전송할 문자라면 'no', 단지 서버와의 문자 전송 테스트라면 'yes'를 입력한다. 값이 없으면 'no'.
    - bulk_file:       선택. SMS, LMS 벌크 메시지는 엑셀(xls, xlsx 둘 다 허용. csv는 지원되지 않음.) 파일을 이용해 전달된다. 이 때 이 파라미터로 파일을 전송한다.
                       벌크 메시지의 경우 전송에 필요한 모든 정보가 파일 안에 있으므로 필수 파라미터를 비롯한 다른 모든 파라미터는 무시된다.
    - file:            선택. MMS 파일의 경우 이 파라미터에 파일을 첨부할 수 있다. MMS 외 message_type에 file을 전송할 경우 무시된다.
                       KTH의 경우 "TXT 2000byte 미만, JPG 20Kib 이하, 최대 3개 첨부가능, 이미지파일 합 40Kib 이하, MA3 16khz 이하"라고 하나
                       아직 정확한 검증이 필요함.

#### Reporting Sent Messages ####
  - 경로: ``/messaging/report/[id]/``
  - 전송된 메시지의 결과를 확인하는 API
  - method: GET
  - parameters
    - 없음.




### Point Management API ###
유저의 포인트 관리를 위한 작업들이다.
이 작업은 관리자의 인증 토큰이 필요하며, 일반 사용자는 단지 자신의 포인트 정보만을 조회할 수 있다.

  경로: ``/point/``

#### Modify user Point ####
  - method: POST
  - parameters:
    - user_id:         필수. 포인터 조정을 원하는 유저 (백엔드 쪽 유저 id)
    - point:           필수. 증감하고픈 포인트. 음수의 경우 차감. 양수의 경우 증가.
    - timestamp:       옵션. 포인트 증가 일시. 생략했을 경우 API 호출한 일시가 입력된다.
    - description:     옵션. 내역에 대한 설명을 입력한다. 100자 미만. 이 길이를 넘으면 넘은 분량은 잘려나간다.
    - 관리자만 호출 가능하다.

  - returns:
    - value: 전체 포인트

#### Query User Point ####
  - method: GET
  - parameters:
    - 일반 사용자의 경우는 파라미터가 필요 없다.
    - 관리자의 경우 파라미터 없이 입력하면 사용자 포인트의 목록을 얻을 수 있다.
    - user_id: 관리자의 경우에는 특정 회원의 아이디를 입력할 수 있다.

  - returns:
    - user_id: 유저의 아이디.
    - value: 유저의 포인트.




### Transaction API ###
유저가 소모, 혹은 생산한 포인트 내역을 조회한다.

  경로: ``/transaction/``

#### Querying Transaction List ####
  - method: GET
  - parameters:
    - id: 옵션. 해당 사용자의 포인트 사용 내역을 출력한다.
    - 관리자의 경우에만 id를 필요로 하며, 일반 유저의 경우는 파라미터를 필요로 하지 않는다.




## API Usage ##
cURL을 이용해 간단하게 백엔드와 API 테스팅을 테스트해 볼 수 있다.

curl -H "AUTHENTICATION: token <email>:<auth_token>" -X [GET|POST|PUT|DELETE] [-d POSTDATA] [-F POSTFORMDATA] "<URL>"

예 1) whoami 체크 테스트
curl -H "AUTHENTICATION: token <email>:<auth_token>" -X GET "http://backend.sms4wp.com/api/v0/whoami/"



## API in Practice ##
