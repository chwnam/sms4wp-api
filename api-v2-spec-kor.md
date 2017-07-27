# API Version 2

본 문서는 [sms4wp.com](https://sms4wp.com)의 API Version 2를 설명합니다.

SMS4WP의 REST API는 [API Version 1](api-v1-spec-kor.md)(APIv1)과 Version 2(APIv2)를 둘다 지원합니다. APIv2는 APIv1의 일부를 대체합니다. 그리고 전화번호 표기, 일시 표기 등 API 전반적인 사용법은 APIv1과 유사합니다.



## 일반 API

주소 접두: ``https://backend.sms4wp.com/api/v2``



### 발신번호 등록 

요청 주소: ``/sendnumber/register``

2017년 8월 7일 17시 이후로는 APIv2의 방식만 유효합니다. 등록 요청, 인증 2단계를 거쳐 진행됩니다.



#### 등록 요청

* 메소드: POST
* 파라미터
  * sendnumber: 발신번호
  * comment: 주석 (200자)
  * pintype: 인증 방법. SMS, VMS 중 1개를 선택할 수 있습니다. SMS는 6자리 인증코드를 문자로 전달받는 방식이며, VMS는 요청한 전화번호로 직접 2자리 인증 코드를 음성으로 통지하는 방식입니다.



#### 인증 단계

* 메소드: POST
* 파라미터
  * sendnumber, comment, pintype: 등록 요청 단계의 값을 그대로 적습니다.
  * pincode: 전달받은 6자리, 혹은 2자리의 인증 코드



#### 반환값

* result_code: 처리 결과 코드
  * 200: 성공
  * 300: 파라미터 에러
  * 400: 인증 업데이트 중 에러
  * 500: 이미 등록된 번호
  * 600: 인증번호 일치하지 않음
  * 700: 핀코드 입력시간 만료 (3분 이후 만료되므로, 재등록 요청해야 함)
* sendnumber: 등록한 번호



### 수동 수신확인

메시지를 보내고 난 후 수신 확인은 자동으로 이루어지나, 수동으로 수신을 요청할 수 있습니다.

요청주소: ``/message/report/<id>``

* 메소드: GET
* 요청 파라미터: 없음
* 응답: 메시지 전송 요청 후 응답과 동일합니다.



### 메시지

#### 메시지 목록

관리자 API와 형태는 동일하나, 자신의 메시지만 볼 수 있습니다.

- 요청 주소: ``/messages``
- 메소드: GET
- 파라미터: 모든 파라미터는 옵션입니다.
  - message_type: SMS, LMS, MMS 타입만을 필터합니다. 대소문자를 구분하지 않습니다.
  - sender_phone: 송신번호로 필터합니다.
  - receiver_phone: 수신번호로 필터합니다.
  - since: 송신요청 시점을 기준으로 필터합니다.
  - until: 송신요청 시점을 기준으로 필터합니다.
  - page_size: 1페이지당 가져올 데이터의 수를 조정 가능.
  - page: 페이지 번호. 기본값은 1입니다.
- 응답
  - count: 응답이 가진 데이터의 총 수
  - next: 다음 페이지의 요청 URL
  - previous: 이전 페이지의 요청 수
  - results: 메시지 전송 요청의 응답 파라미터 참고
  - query_params: 입력한 쿼리를 그대로 돌려 준다.
  - total_count: 일반 사용자가 보낸 메시지의 총 수.



## 관리 API

주소 접두: ``https://backend.sms4wp.com/api/v2/admin``

관리 API는 관리자 토큰만 호출 가능합니다.

### 사용자

#### 사용자 목록

* 요청 주소: ``/users``
* 메소드: GET
* 파라미터: 모든 파라미터는 옵션입니다.
  * email: 문자열을 포함한 이메일만 필터. 대소문자 구별.
  * group: 그룹 문자열로 사용자 필터 (e.g. ADMIN, USER). 대소문자 구분하지 않음.
  * status: 상태 표현 문자열로 사용자 필터 (e.g. GOOD, BANNED, DELETED). 대소문자 구분하지 않음.
  * since: 등록 일시 필터. ISO-8601 표기법을 사용.
  * until: 등록 일시 필터. ISO-8601 표기법을 사용.
  * order_by: 기본은 등록 일시의 내림차순이다. 본 필드 이름을 주어 정렬 기준을 변경할 수 있다. 앞에 '-'를 붙이면 내림차순 정렬이 가능하다. 예. -id (아이디 역순)
  * page_size: 1페이지당 가져올 데이터의 수를 조정 가능.
  * page: 페이지 번호. 기본값은 1입니다.
* 응답
  * count: 응답이 가진 데이터의 총 수
  * next: 다음 페이지의 요청 URL
  * previous: 이전 페이지의 요청 수
  * results: 데이터
    * id
    * name
    * description
    * user_group
      * id
      * name
      * description
    * user_status
      * id
      * code
      * description
  * query_params: 사용자가 입력한 쿼리를 그대로 돌려 준다.
  * total_count: 총 사용자의 수



### 메시지

#### 메시지 목록

* 요청 주소: ``/messages``
* 메소드: GET
* 파라미터: 모든 파라미터는 옵션입니다.
  * user_id: 특정 사용자의 메시지 목록만을 필터합니다.
  * message_type: SMS, LMS, MMS 타입만을 필터합니다. 대소문자를 구분하지 않습니다.
  * sender_phone: 송신번호로 필터합니다.
  * receiver_phone: 수신번호로 필터합니다.
  * since: 송신요청 시점을 기준으로 필터합니다.
  * until: 송신요청 시점을 기준으로 필터합니다.
  * page_size
  * page
* 응답
  - count: 응답이 가진 데이터의 총 수
  - next: 다음 페이지의 요청 URL
  - previous: 이전 페이지의 요청 수
  - results: 메시지 전송 요청의 응답 파라미터 참고
  - query_params: 입력한 쿼리를 그대로 돌려 준다.
  - total_count: 데이터베이스의 총 메시지 수.



### 거래내역

#### 거래내역 목록

* 요청 주소: ``/transactions``
* 메소드: GET
* 파라미터: 모든 파라미터는 옵션입니다.
  * user_id: 특정 사용자의 거래내역만을 필터합니다.
  * since: 특정시점 이후의 거래내역만을 필터합니다.
  * until: 특정시점 이전의 거래내역만을 필터합니다.
  * order_by: 기본은 거래내역시점 내림차순이나, 별도의 필드 이름을 주어 정렬 가능합니다. 앞에 '-'를 붙이면 내림차순 정렬이 가능합니다.
  * page_size: 한 페이지의 크기를 조정할 수 있습니다.
  * page: 페이지 번호를 지정할 수 있습니다. 기본값은 1입니다.
* 응답
  * count
  * next
  * previous
  * results
    * id
    * value
    * description
    * timestamp
    * point
      * id
      * type
      * point_cost
    * user_id
  * query_params
  * total_count

