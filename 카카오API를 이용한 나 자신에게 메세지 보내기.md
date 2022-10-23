# 카카오API를 이용한 나 자신에게 메세지 보내기

## 방법

> 1. Kakao Developers들어가기
>
> 2. 앱 만들기
>
>    1. 적당한 이름으로 애플리케이션 제작
>
>       1. Rest API 키값 확인
>
>    2. 좌측에서 카카오로그인 찾기
>
>       1. 로그인 활성화
>       2. Redirect URL 지정
>          - `https://example.com/oauth` 보통 요거로 하는듯
>       3. 동의항목에서 `카카오톡 메시지 전송` 선택동의 설정하기
>
>    3. 카카오로그인(2번) 완료시 해당 URL로 접속
>
>       1. ```
>          https://kauth.kakao.com/oauth/authorize?client_id=자신의 REST 키값&redirect_uri=REDIRECT로 설정했던값&response_type=code 
>          ```
>
>          1. 1번 제작시 주는 Rest API 키 확인 후 넣기
>          2. Redirect지정 주소(2-2번) 맞춰서 바꾸기
>
>    4. URL로 접속한 주소의 `code=` 해당 뒷부분 복사
>
>    5. 파이썬 파일 만들기
>
>       1. import부분입니다
>
>          * ```
>            import sys
>            import datetime
>            import requests
>            import json
>            import schedule
>            import time
>            ```
>
>            * 필요한것들입니다 없으면 아시져?pip install 해주세요 ㅎㅎ
>
>       2. 원하는 시간에 실행할 함수
>
>          * ```
>            def job():
>                url = 'https://kauth.kakao.com/oauth/token'
>                client_id = 'Rest API 키'
>                redirect_uri = '2-2번에 지정한 Redirect_url'
>                code = '위의 4번에서 복사한 code=뒷부분'
>                data = {
>                    'grant_type':'authorization_code',
>                    'client_id':client_id,
>                    'redirect_uri':redirect_uri,
>                    'code': code,
>                    }
>
>                response = requests.post(url, data=data)
>                tokens = response.json()
>
>                url = "https://kapi.kakao.com/v2/api/talk/memo/default/send"
>
>                headers = {
>                    "Authorization": "Bearer " + tokens["access_token"]
>                }
>
>                data = {
>                    'object_type': 'text',
>                    'text': '실험용',
>                    'link': {
>                        'web_url': 'https://developers.kakao.com',
>                        'mobile_web_url': 'https://developers.kakao.com'
>                    },
>                    'button_title': '키워드'
>                }
>
>                data = {'template_object': json.dumps(data)}
>                response = requests.post(url, headers=headers, data=data)
>                response.status_code
>                print(response.status_code)
>                sys.exit()
>            ```
>
>            * text부분에 보내고 싶은 내용으로 바꾸시면 됩니다('실험용'➡'코린이등장')
>            * 개인적으로 status_code는 출력해주는게 좋습니다(실행 안되면 몇번오류인지 봐야함)
>            * sys.exit()로 run되는거 끝내기
>
>       3. schedule 작동을 계속하기 위한 코드(겸사겸사 현재시간)
>
>          * ```
>            def NowTime():
>                now = datetime.datetime.now()
>                print("현재 시간은? ",now)
>            ```
>
>            * datetime으로 계속 불러주면서 이어가기
>
>       4. 실제 함수 호출 및 사용
>
>          * ```
>            schedule.every(1).minutes.do(NowTime)
>            schedule.every().day.at("13:14").do(job)
>            while True:
>                    schedule.run_pending()
>                    time.sleep(60)
>            ```
>
>            * schedule.every(1).minutes.do(NowTime)에 주기를 1로 지정해주었습니다(1분마다)
>            * schedule.every().day.at("13:14").do(job)
>              * day.at("해당부분에 원하는 시간 적기") 그럼 do한다는거 아까 적은 함수
>              * 주기를 지정안했기 때문에 만약 job함수 마지막에 sys.exit()가 없으면 무한루프로 매번 그 시간에 보내는겁니다.
>            * while문을 써서  호출합니다
>            * schedule.run_pending은 자신이 정해둔 schedule내용을 실행합니다(위에2개)
>            * time.sleep(60)으로 해서 호출후 1분 쉽니다
>              * 위에 every(1).minutes.do 즉 1분마다이기 때문에 통일
>
>

## 단점

> 1. 토큰 유효시간이 12시간이다(이건 아직 확인 안해봄)
> 2. code가 1회성이다(code한번쓰면 다시 호출해서 받아와야함)

