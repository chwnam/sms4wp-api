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
  *  

### 메시지 내역 조회
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

#### 내역 조회
인증 토큰으로 전송한 메시지 내역을 조회합니다.

* 요청 주소: ``/message/``
* 메소드: GET
* 요청 파라미터
  * since
  * until
  * limit
* 응답 파라미터 
  * 




### 포인트 관리

      
#### 포인트 조회
인증 토큰 소유자의 포인트 내역을 조회합니다.

* 요청 주소: ``/user_point/``
* 메소드: GET
* 요청 파라미터: 없음
* 응답 파라미터
  * id
  * user_id
  * value




    
### 포인트 증감 내역 조회

#### 포인트 증감 내역
인증 토큰 소유자의 포인트 변동 사항을 조회합니다.

* 요청 주소: ``/transaction/``
* 메소드: GET
* 요청 파라미터
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

