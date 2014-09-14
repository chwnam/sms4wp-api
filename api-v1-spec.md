# API version 1 사양 설명서
**Korean Version.**

* * *
 
## 일반 API

## 관리자 전용 API
주소 접두: ``http://backend.sms4wp.com/api/v1/admin/``

### 사용자 관리

#### 멤버 등록
* 요청 주소: ``/user/``
* 메소드: PUT
* 요청 파라미터
  * membership_id
  * membership_type_name
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
  * membersip_id
  * membership_type_name
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
  * membership_type_name
  * registered_timestamp
  * membership_id
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





### 메시지 내역 조회

#### 내역 조회
* 요청 주소: ``/message/``
* 메소드: GET
* 요청 파라미터
  * auth_token_id
  * since
  * until
  * limit
* 응답 파라미터 
  * 




### 포인트 관리

#### 포인트 조정
* 요청 주소: ``/user_point/``
* 메소드: POST
* 요청 파라미터
  * user_id
  * value
  * description
  * timestamp
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
      
#### 포인트 조회
* 요청 주소: ``/user_point/``
* 메소드: GET
* 요청 파라미터
  * user_id
* 응답 파라미터
  * id
  * user_id
  * value




    
### 포인트 증감 내역 조회

#### 포인트 증감 내역
* 요청 주소: ``/transaction/``
* 메소드: GET

요청 파라미터

파라미터  | 필수   | 설명
--------|-------|------
user_id | 필수   | 백엔드 유저 아이디
since   | 옵션   | 
until   | 옵션   |
limit   | 옵션   |

응답 파라미터

파라미터      | 설명
------------|----------
id          | 증감 내역 아이디          |
user_id     | 백엔드 유저 아이디        |
point_id    | 포인트 아이디            |
value       | 증감값                 |
description | 증감 내역 설명           |
timestamp   | 기록 시간               |



