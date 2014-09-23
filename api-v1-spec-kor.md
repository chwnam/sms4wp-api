# API version 1 사양 설명서
**Korean Version.**

* * *

## 일러두기





### API 인증 키
#### 발급
발급은 현재 클라이언트 플러그인에서만 가능합니다.

#### 사용
인증은 http(s) 헤더 "AUTHENTICATION"을 사용합니다. 헤더의 값은 'token' 이란 문자열, 그리고 사용자의 이메일과 인증 토큰를 콜론(:)으로 연결한 문자열입니다 'token' 문자열과는 1개의 공백으로 분리되어 있어야 합니다.

예를 들어 사용자의 이메일이 'bob@email.com'이고 인증 토큰이 'secretkey' 라고 하면 인증을 위해 request header에 다음과 같은 헤더를 추가합니다.

``AUTHENTICATION: token bob@email.com:secretkey``

 
### API 입력 파라미터와 응답
#### 메소드
HTTP 메소드로 PUT, GET, POST, DELETE 이 네 가지를 주로 사용합니다. 이 네 메소드는 CRUD (Create - Retrieve - Update - Delete) 네 작업과 1:1로 대응합니다.

##### PUT
주로 새로운 정보를 전달(create)하는 경우에 사용합니다. 단, create와 update 작업이 둘 다 이루어져야 하는 API의 경우에만 사용합니다.

##### POST
주로 새로운 정보를 전달하거나, 기존 정보를 수정하는 경우에 사용합니다. 단 해당 API가 create만을 필요로 하는 경우에는 POST 메소드가 create 작업을 맡지만, API가 *create와 update를 둘 다 지원해야 하는 경우에는* PUT이 create를, POST가 update를 처리하도록 합니다.

##### GET
데이터를 조회할 때 사용합니다. 파라미터가 없을 경우 해당 데이터의 전부를 리턴하며, 파라미터를 통해 데이터의 범위를 조절할 수 있도록 합니다.

##### DELETE
데이터를 삭제할 때 사용합니다.


#### 날짜 표기
파라미터에서 사용되는 일시 표기는 모두 [ISO8601](http://www.w3.org/TR/NOTE-datetime)을 준수합니다. 또한 백엔드 서버는 기본적으로 UTC를 사용합니다. 시간대 표기가 없는 경우 UTC로 간주됩니다만, 시간대 표기를 항상 명시할 것을 권고합니다.

* 예
  - **2014-09-01T12:00:30Z**: UTC 2014년 09월 01일 12시 00분 30초. 참고로 이 때 서울은 21시 00분 30초.
  - **2014-09-01T23:30:12+09:00**: Offset +9h, 즉 한국 현지시각으로 2014년 09월 01일 오후 11시 30분 12초.
  - **2014-10-22T07:30:00**: 시간대가 정해지지 않았습니다. 이 경우 UTC로 간주합니다.

#### Content-Type

입력 파라미터를 전송할 때 content type으로는 ``multipart/form-data``, ``application/x-www-form-urlencoded`` 둘 다 구분없이 사용 가능합니다.

#### 입력 파라미터
일부 API에서는 한 파라미터에 여러 값을 순차적으로 입력할 수 있습니다. 가령 파라미터 'param'의 값으로 1, 2, 3, 4, 5를 차례로 입력해야 한다고 하면, 
```
param[]=1&param[]=2&param[]=3&param[]=4&param[]5
```
과 같이 목록으로 입력 가능합니다.

#### 응답 값
백엔드 답신은 JSON 형태로 전달되며, 작업의 성공, 혹은 실패 여부는 HTTP response code를 보고 판단합니다. 예를 들어 서버로부터 HTTP response code 200이 전달되었다면 성공적인 응답이 왔음을 의미합니다.

관례적으로 다음과 같은 HTTP response code를 문맥에 따라 사용합니다.

 응답 코드 | 문자열                | 설명                                                        
-------- | ------------------- | -----------------------------------------------------------
200      |OK                   | 성공.                                                        
400      | BAD REQUEST         | 파라미터 값의 에러 등의 요청 프로토콜이 맞지 않는 경우를 의미한다.
403      | FORBIDDEN           | 인증되지 않음. 인증 토큰을 다시 확인해 보아야 한다.
404      | NOT FOUND           | 해당 파라미터의 프로토콜은 올바르나, 서버에 자원이 없거나 찾을 수 없는 경우를 의미한다.
405      | METHOD NOT ALLOWED  | 해당 API에서는 제시된 METHOD가 지원되지 않음을 의미한다.
406      | NOT ACCEPTABLE      | 해당 API를 진행할 수 없는 경우를 뜻한다. 일례로 SMS를 보내려고 하나, 포인트가 부족한 경우를 들 수 있다.
415      | UNSUPPORTED MEDIA TYPE |  벌크 메시지, 멀티미디어 메시지 등으로 첨부된 파일이 지원되지 않는 경우이다.
500      | INTERNAL SEVER ERROR   | 해당 API를 처리하는 중 백엔드에서 프로그램 에러가 일어난 경우.
501      | NOT IMPLEMENTED        | 해당 API는 아직 완전히 구현되지 않았음.

단, GET으로 질의하는 '검색'에 해당하는 API의 경우에는 검색 결과가 없어도 200을 반환합니다. 이 때 결과로는 빈 리스트가 전달되니다.


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
    "value": "<auth_token_value>",
    "scope": "client.develop",
    "digest": "<digest_value>",
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
  * 요청 주소의 sms, lms, mms 부분을 생략할 경우 SMS 메시지로 간주합니다.
  * 세 메시지 방법 보두 POST 메소드를 사용합니다.

#### SMS/LMS 전송 요청 파라미터
SMS와 LMS는 전송 파라미터가 동일합니다. 단, LMS가 보다 많은 내용의 메시지를 보낼 수 있습니다. '동보 전송'이란 같은 내용을 복수의 수신자에게 전달하는 것을 말합니다.
   
* 필수 요청 파라미터: 다음 항목은 반드시 포함되어야 합니다.
  * sender_phone: 송신자의 전화번호이니다.
    * body: 메시지 본문입니다.
    * receiver_number: 수신자의 전화번호입니다. 동보 전송의 경우 name 파라미터와 순서를 동일하게 맞추어 주어야 합니다. 여러 전화번호를 지정할 경우 ``number[]=XXXXXX&number[]=YYYYYY&...`` 와 같이 number 뒤에 대괄호를 붙여 주세요.
    * receiver_name: 수신자의 이름이니다. 동보 전송의 경우 number 파라미터와 순서를 맞추어야 합니다. 여러 전화번호를 지정할 경우 ``name[]=XXXXXX&name[]=YYYYYY&...`` 처럼 name 뒤에 대괄호를 붙여 주세요.
* 선택 요청 파라미터: 다음 항목은 선택적으로 지정할 수 있습니다.
  * sender_name: 송신자의 이름을 지정할 수 있습니다.
  * subject: 메시지 제목을 설정할 수 있습니다.
  * is_test: 테스트 목적으로 메시지를 보낼 경우 'yes', 실제로 메시지를 보낼 경우에는 'no'를 선택합니다. 생략하면 'no'입니다.
  * send_timestamp: 예약 문자의 경우 지정합니다. 지정하지 않으면 
    
##### 벌크 전송
엑셀 파일을 이용해 여러 명에게 각각 다른 내용의 SMS, LMS 메시지를 전송할 때 사용합니다.

* 요청 파라미터:
  * bulk_file: 이 파라미터는 다른 파라미터보다 우선적으로 인식되며, 선언될 경우 다른 파라미터는 무시되어 벌크 전송으로만 동작합니다.

벌크 전송을 할 때 엑셀 파일은 다음과 같은 규칙을 지켜야 합니다.

  1. 파일은 xls, xlsx를 지원합니다. csv는 지원하지 않습니다. 
  2. 메시지 내용은 모두 첫번째 시트에 위치해야 합니다.
  3. 시트의 데이터는 2행부터이며, 1행은 헤더를 위한 곳입니다. 헤더의 텍스트는 임의로 정할 수 있으나 각 필드의 순서는 다음과 같이 정해져 있습니다.
     * 발송 시간
     * 예약 전송을 하지 않을 경우에는 비워 둡니다.
     * **주의!** 다른 API와는 달리 여기서는 날짜 형식을 'YYYYMMDDHHMMSS' 형식으로 맞추어 주십시오. 가령 2014년 09월 29일 오후 09시 33분 00초에 문자를 보낸다면, '20140929093300'입니다. 또한 여기서는 KST(대한민국) 지역 시간대를 사용합니다.
     * 수신자 번호
     * 수신자 이름
     * 발신자 번호
     * 발신자 이름
     * 제목
     * 내용


#### MMS 전송 요청 파라미터
SMS와 LMS 전송과 동일하나 다음 파라미터를 추가로 받습니다.
  * file: JPG 20KiB 미만의 파일 하나를 전송할 수 있습니다.
  * MMS 전송은 벌크 전송을 지원하지 않습니다.

#### 응답 파라미터
* 세 방식 모두 같은 응답 형태를 취합니다.
* id: 메시지 아이디.
* user: 백엔드 유저 아이디.
* auth_token: 유저의 인증 토큰 아이디.
* timestamp: 메시지 전송 요청 시각.
* message_type: 송신한 메시지 형태에 대한 정보
  * id: 메시지 아이디 타입 아이디
  * type: 메시지 타입 (SMS, LMS, MMS)
  * point_cost: 메시지가 소모하는 포인트
* message_detail: 메시지 상세 내역
  * message: 상세 내역 아아디
  * send_timestamp: 예약 설정 일시. 즉시 전송일 경우 null.
  * sender_phone: 발신자 전화번호.
  * sender_name: 발신자 이름.
  * subject: 메시지 제목.
  * body: 메시지 본문.
  * num_recipients: 수신자 수.
  * is_test: 테스트 여부
  * request_status: 송신 요청 상태
    * id: 상태 아이디
    * code: 코드
    * description: 코드의 설명
  * transaction: 포인트 소모 내역
    * id: 내역 아이디
    * user: 사용자 아이디
    * point: 포인트 아이디
    * value: 소모한 포인트
    * description: 소모 내역 설명
  * timestamp: 소모가 일어난 시각
  * message_results: 메시지 결과 정보
    * id: 결과 아이디
    * report_status: 메시지 전송 상태 보고
      * id: 전송 상태 아이디
      * code: 전송 상태 코드
      * description: 상태 설명
    * transaction: 상위 노드의 transaction과 동일합니다. 이 값이 null이 아닌 경우는 해당 메시지가 정상적으로 전송되지 않아 포인트가 회수된 것입니다.
    * report_timestamp: 보고 확인 시각
    * receiver_phone: 수신자 전화번호
    * receiver_name: 수신자 이름
  * message_resources: 메시지 첨부 파일 내역. 벌크 메시지, 혹은 MMS 이미지 첨부의 경우 null이 아닌 값으로 채워집니다.
    * id: 파일 내역 아이디
    * message: 메시지 아이디.
    * mime_type: 파일의 MIME type.
  
메시지 응답의 예
```
{
	"message_type": {
		"id": 1,
		"type": "SMS",
		"point_cost": 1
	},
	"message_detail": {
		"request_status": {
			"id": 3,
			"code": 200,
			"description": "OK"
		},
		"transaction": {
			"id": 13,
			"user": 5,
			"point": 6,
			"value": -1,
			"description": "Messaging transaction by user id=5",
			"timestamp": "2014-09-23T00:29:35"
		},
		"message_results": [
			{
				"report_status": {
					"id": 2,
					"code": 4100,
					"description": "전달"
				},
				"transaction": null,
				"id": 10,
				"report_timestamp": "2014-09-23T00:29:38",
				"receiver_phone": "010XXXXYYYY",
				"receiver_name": ""
			}
		],
		"message": 10,
		"send_timestamp": null,
		"sender_phone": "010XXXXYYYY",
		"sender_name": "",
		"subject": "",
		"body": "API Test",
		"num_recipients": 1,
		"is_test": false
	},
	"message_resources": [],
	"id": 10,
	"user": 5,
	"auth_token": 7,
	"timestamp": "2014-09-23T00:29:30"
}
```

#### 내역 조회
인증 토큰으로 전송한 메시지 내역을 조회합니다. 내역은 항상 시간을 기준으로 정렬되며 최근 시간 순으로 출력됩니다.

* 요청 주소: ``/message/``
* 메소드: GET
* 요청 파라미터
  * message_id: 메시지 아이디를 지정 가능합니다. 여러 메시지 아이디를 지정할 수 있습니다. 단, API 호출 헤더에 지정된 인증 토큰으로 보내지 않은 메시지에 대해서는 조회할 수 없습니다. 생략하면 모든 message_id를 대상으로 합니다.
  * since: 이 일시보다 지난 내역을 검색합니다. 생략 가능합니다.
  * until: 이 일시보다 전의 내역을 검색합니다. 생략 가능합니다.
  * limit: 한 번에 가져올 개수를 지정합니다. 생략 가능하며 지정하지 않을 시 서버에서 지정한 최대 개수를 지정합니다.
* 응답 파라미터
  * 메시지 전송의 응답 파라미터와 동일한 형태의 JSON 오브젝트가 리스트 형태로 출력됩니다.  
  
  


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
  * since: 이 일시보다 지난 내역을 검색합니다. 생략 가능합니다.
  * until: 이 일시보다 전의 내역을 검색합니다. 생략 가능합니다.
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


## cURL을 이용한 API 호출 예제 