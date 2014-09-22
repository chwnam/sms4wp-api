# API version 1 사양 설명서
**Korean Version.**

* * *
 
## 일반 API
주소 접두: http://backend.sms4wp.com/api/v1

### 사용자 관리
#### 사용자 정보
* 요청 주소: ``/user/``
* 메소드: GET
* 요청 파라미터: 없음
* 응답 파라미터
  * id: 유저의 백엔드 아이디
  * membership_type: 멤버십 타입 외래 키
    * id: 멤버십 타입의 아이디
    * name: 멤버십 타입 이름
    * description: 멤버십 타입 설명
  * registered_timestamp: 유저 등록 시간
  * membership_id: 해당 사이트의 멤버 id
  * email: 이메일 주소
  * user_group: 유저 그룹의 외래 키
    * id: 유저 그룹의 아이디
    * name: 유저 그룹 이름
    * description: 그룹 설명
  * user_status: 유저 상태의 외래 키
    * id: 유저 상태의 아이디
    * code: 상태 코드
    * description: 상태 설명

메시지 응답 예
```
{
    "id": 5,
    "membership_type": {
        "id": 1,
        "name": "sms4wp.com",
        "description": "sms4wp.com membership"
    },
    "registered_timestamp": "2014-09-14T22:02:15",
    "membership_id": 11,
    "email": "tester1@ibnet.co.kr",
    "user_group": {
        "id": 2,
        "name": "user",
        "description": "General user group"
    },
    "user_status": {
        "id": 1,
        "code": 0,
        "description": "GOOD"
    }
}
```
  
### 인증 토큰

일반 사용자는 인증 토큰의 회수만 가능합니다. 또한 일반 사용자가 자신의 계정에 부여된 모든 인증 토큰의 내역을 확인하기 위해서는 프론트엔드 사이트를 방문해야 합니다.
 
#### 인증 토큰 회수
* 요청 주소: ``/auth_token/revoke/``
* 메소드: POST
* 요청 파라미터: 없음
* 응답 파라미터
  * id: 인증 토큰의 아이디.
  * user: 사용자 아이디.
  * value: 토큰 값.
  * scope: 범위.
  * digest: 현재는 사용하지 않습니다.
  * grant_timestampe: 인증된 시간. UTC입니다.
  * revoked_timestamp: 회수된 시간. UTC입니다.
  * token_status: 토큰 상태를 나타내는 외래 키
    * id: 토큰 상태의 id.
    * code: 상태 코드.
    * description: 상태 코드 설명.
* 한 번 회수된 인증 토큰으로는 API 호출이 불가능합니다. 클라이언트 플러그인에서 다시 인증 토큰을 발급받아야 합니다.

메시지 응답의 예
```
{
    "token_status": {
        "id": 2,
        "code": 400,
        "description": "REVOKED"
    },
    "id": 7,
    "user": 5,
    "value": "8569aad1d6a0c8d298d2c677906c68f2c5719de1ebfa4e9b8ea6c79452e8d579",
    "scope": "client.develop",
    "digest": "08de4e78dc008c77ef7aa4db50427d94",
    "granted_timestamp": "2014-09-14T22:18:04",
    "revoked_timestamp": "2014-09-18T12:11:17.684Z"
}
```
 

### 메시징
#### 메시지 전송
* 요청 주소
  * SMS: ``/message/sms/``
  * LMS: ``/message/lms/``
  * MMS: ``/message/mms/``
* SMS 전송 요청 파라미터
  * 단보 전송
  * 동보 전송
  * 벌크 전송
* LMS 전송 요청 파라미터
  * 단보 전송
  * 동보 전송
  * 벌크 전송 
* MMS 전송 요청 파라미터
  * 단보 전송
  * 동보 전송
  * 벌크 전송: MMS는 벌크 전송을 지원하지 않는다.
  
* 응답 파라미터
  *   

#### 내역 조회
인증 토큰으로 전송한 메시지 내역을 조회합니다. 내역은 항상 시간을 기준으로 정렬되며 최근 시간 순으로 출력됩니다.

* 요청 주소: ``/message/``
* 메소드: GET
* 요청 파라미터
  * since: ISO8601 UTC 형태입니다. 이 일시보다 지난 내역을 검색합니다. 생략 가능합니다.
  * until: ISO8601 UTC 형태입니다. 이 일시보다 전의 내역을 검색합니다. 생략 가능합니다.
  * limit: 한 번에 가져올 개수를 지정합니다. 생략 가능하며 지정하지 않을 시 서버에서 지정한 최대 개수를 지정합니다.
* 응답 파라미터 
  
  





### 포인트 관리

      
#### 포인트 조회
인증 토큰 소유자의 포인트 내역을 조회합니다.

* 요청 주소: ``/user_point/``
* 메소드: GET
* 요청 파라미터: 없음
* 응답 파라미터
  * id: 유저 포인트 주 키.
  * user_id: 유저 아이디.
  * value: 포인트 값.

메시지 응답의 예
```
{
    "id": 6,
    "user": 5,
    "value": 350
}
```



    
### 포인트 증감 내역 조회

#### 포인트 증감 내역
인증 토큰 소유자의 포인트 변동 사항을 조회합니다.

* 요청 주소: ``/transaction/``
* 메소드: GET
* 요청 파라미터
  * since: ISO8601 UTC 형태입니다. 이 일시보다 지난 내역을 검색합니다. 생략 가능합니다.
  * until: ISO8601 UTC 형태입니다. 이 일시보다 전의 내역을 검색합니다. 생략 가능합니다.
  * limit: 한 번에 가져올 개수를 지정합니다. 생략 가능하며 지정하지 않을 시 서버에서 지정한 최대 개수만큼 가져옵니다.
* 응답 파라미터
  * id: 각 내역의 아이디.
  * user: 백엔드 사용자 아이디.
  * value: 증감값. 포인트 획득일 경우 양수, 포인트 소모의 경우 음수.
  * description: 포인트 변화에 대한 설명.
  * timestamp: 증감이 일어난 일시.

메시지 응답의 예
```
[
    {
        "id": 5,
        "user": 5,
        "point": 6,
        "value": 150,
        "description": "sample 3"",
        "timestamp": "2014-09-15T22:42:42"
    },
    {
        "id": 4,
        "user": 5,
        "point": 6,
        "value": 100,
        "description": "sample 2",
        "timestamp": "2014-09-15T22:39:22"
    },
    {
        "id": 3,
        "user": 5,
        "point": 6,
        "value": 100,
        "description": "sample 1",
        "timestamp": "2014-09-15T22:38:45"
    }
]
```






## 관리자 전용 API
주소 접두: ``http://backend.sms4wp.com/api/v1/admin``



### 사용자 관리

#### 멤버 등록
* 요청 주소: ``/user/``
* 메소드: PUT
* 요청 파라미터
  * membership_id: 복수 지정 가능
  * membership_type_name
  * email: 복수 지정 가능
  * user_group_name
* 응답 파라미터 
  * id
  * membership_type
  * registered_timestamp
  * membership_id
  * email
  * user_group
  * user_status

#### 멤버 조회
* 요청 주소: ``/user/``
* 메소드: GET
* 요청 파라미터
  * id
  * membersip_id
  * membership_type_name
  * email
  * user_group_name
  * user_status_name
* 응답 파라미터 
  * id
  * membership_type
  * registered_timestamp
  * membership_id
  * email
  * user_group
  * user_status 

#### 멤버 수정
* 요청 주소: ``/user/``
* 메소드: POST
* 요청 파라미터
  * id
  * membership_id
  * membership_type_name
  * email
  * user_group_name
  * user_status_name
* 응답 파라미터 
  * id
  * membership_type
  * registered_timestamp
  * membership_id
  * email
  * user_group
  * user_status  

#### 멤버 삭제 
* 요청 주소: ``/user/``
* 메소드: DELETE
* 요청 파라미터
  * id
* 응답 파라미터 
  * id
  * membership_type
  * registered_timestamp
  * membership_id
  * email
  * user_group
  * user_status 




### 인증 토큰 관리

#### 토큰 조회
* 요청 주소: ``/auth_token/``
* 메소드: GET
* 요청 파라미터
  * id
  * user_id
  * token_status_description
* 응답 파라미터 
  * id
  * user_id
  * value
  * scope
  * digest
  * granted_timestamp
  * revoked_timestamp
  * token_status

#### 토큰 삭제
* 요청 주소: ``/auth_token/``
* 메소드: DELETE
* 요청 파라미터
  * id
* 응답 파라미터 
  * id
  * user_id
  * value
  * scope
  * digest
  * granted_timestamp
  * revoked_timestamp
  * token_status 

#### 토큰 발급
* 요청 주소: ``/auth_token/grant/``
* 메소드: POST
* 요청 파라미터
  * user_id
  * scope
* 응답 파라미터 
  * id
  * user_id
  * value
  * scope
  * digest
  * granted_timestamp
  * revoked_timestamp
  * token_status  

#### 토큰 회수
* 요청 주소: ``/auth_token/revoke/``
* 메소드: POST
* 요청 파라미터
  * id
* 응답 파라미터 
  * id
  * user_id
  * value
  * scope
  * digest
  * granted_timestamp
  * revoked_timestamp
  * token_status




### 포인트 관리

#### 포인트 조회
* 요청 주소: ``/user_point/``
* 메소드: GET
* 요청 파라미터
  * user_id
* 응답 파라미터
  * id
  * user_id
  * value


#### 포인트 조정
* 요청 주소: ``/user_point/``
* 메소드: POST
* 요청 파라미터
  * user_id
  * value
  * description
* 응답 파라미터
  * user_point
    * id
    * user_id
    * value
  * point_transaction
    * id
    * user_id
    * point_id
    * value
    * description
    * timestamp





### 메시지 내역 조회

#### 내역 조회
* 요청 주소: ``/message/``
* 메소드: GET
* 요청 파라미터
  * user_id 
  * auth_token_id
  * message_id
  * since
  * until
  * limit
* 응답 파라미터 
  * 



    
### 포인트 증감 내역 조회

#### 포인트 증감 내역
* 요청 주소: ``/transaction/``
* 메소드: GET
* 요청 파라미터
  * user_id
  * since
  * until
  * limit
* 응답 파라미터
  * id
  * user_id
  * point_id
  * value
  * description
  * timestamp




### 시스템 체크

#### 분산처리 서버 체크
* 요청 주소: ``/test_task/``
* 메소드: GET
  * x
  * y
* 응답 파라미터 

