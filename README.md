
# mini-project (web)

System Programming, Cloud Computing (2nd Semester 202i0. Integrated Mini Projec#)

개발 환경 : 우분투 리눅스 20.04

사용한 클라우드 서비스 : Amazon AWS (Rekognition, Cognito, EC2)

유저가 인물 이미지를 업로드하면 그 인물의 이름과 인물에 대한 imdb 혹은 구글 검색 결 과 링크를 반환 해 주는 웹사이트

A flask app deployed with nginx using gunicorn, amazon EC2

----------------------------------------------------------------------------------------------------------

## 설명


#### A. 프로젝트 명

Celebrity finder

#### B. 프로젝트 멤버 이름 및 멤버 별로 담당한 파트에 대한 소개

프로젝트 멤버 이름 :박지원
 담당한 파트 : 전체
#### C. 프로젝트 소개 및 개발 내용 소개

프로젝트 목적 : 한 학기 동안 시스템 프로그래밍과 클라우드 컴퓨팅 수업을 들으면서 배웠던 개념들을 이용해서 웹사이트를 만들어 보면서 배운 내용을 활용할 수 있는 능력을 기른다. 리 눅스 개발환경에서 AWS 가 제공하는 클라우드 서비스를 활용하여 실제로 현실에서 사용 가 능한 애플리케이션을 제작한다.

프로젝트 개발 내용

1. 유저가 인물 이미지를 업로드하면 그 인물의 이름과 인물에 대한 imdb 혹은 구글 검색 결 과 링크를 반환 해 주는 웹사이트

2. 우분투 20.04 버전 에서 개발을 진행 하였다.

3. 사진 속 유명인사를 인지 하는 api 로 아마존 AWS 클라우드 서비스 Rekognition를 사용 한다. ; 업로드한 사진 속 인물을 분석하여 유명인사임을 인식하고 관련 정보가 있는 imdb 웹페이지 혹은 구글검색결과 링크를 보여준다. (RecognizeCelebrities 의 Request 에 대하여 반환되는 Response의 포맷을 보면 Urls 가 있는데, 인물에 대한 내용이 있는 Imdb 웹사이트 페이지 링크이다. 이 링크도 웹페이지에 표시된다.

4. 익명 게스트 사용자 (유저) 가 AWS 서비스에 액세스하기 위한 임시 자격 증명을 얻기 위 해서 Amazon Cognito 를 사용한다. ; (Manage Identity Pools 에서 새로운 identity pool 을 생성하고 생성 시 인증되지 않은 ID 에 대한 액세스를 활성화한다. 그런 후, IAM 으로 가서 생성한 identity pool 에 대한 Unauthenticated role 정책을 추가시킨다.)

5. html 파일에 jquery 와 aws sdk javascript 라이브러리를 포함시키고, aws 자격 증명을 얻기 위한 자바스크립트 코드를 추가한다. 사진 확인 버튼 클릭 이벤트에서 선택한 파일을 가져온 후, 로컬 파일 리더 개체를 만들고 선택한 이미지를 읽는다. 완료되면 rekognized celebrity API 에 대한 매개 변수 개체를 만들어서 선택한 이미지의 이진 표현을 가져온 다. API 를 호출하고 반환된 결과에서 감지된 각 유명인의 얼굴에 대해 이름과 첫번째 url 링크를 가져온다. url ㅇ이 비어 있으면 유명인 이름을 검색 매개 변수로 사용하여 맞춤 google 검색 url 을 만든다. 유명인 이름과 url 이 포함 된 html snippet 을 만들고 album-list div 에 추가시킨다.

6. 백엔드 ; 웹 프레임워크로 flask 사용함

7. 배포를 위해서 AWS ec2와 nginx, gunicorn 을 사용한다. 우분투 AWS ec2 instance 를 생성하고 nginx 를 설치. gunicorn 을 이용해서 nginx 이 받은 요청을 flask app 이 처리할 수 있는 형식으로 변환 하고, 필요 시 코드가 실행되도록 해줌.

8. wsgi entry point 생성(애플리케이션의 진입 점 역할. gunicorn 서버와 애플리케이션이 상호작용할 수 있도록 해줌), systemd uni file 생성 ( 부팅 시 서비스가 시작 될 수 있도 록 함. 어떤 서비스가 실행될 것인지 지정(작업 디렉토리 매핑, path 환경변수 설정)) -> 자동으로 app.sock 파일이 생성됨, nginx 구성 (/etc/nginx/ 구성파일이 있는 곳. sites-available 과 sites-enabled ; site-avaiable 에 애플리케이션에 대한 파일을 생성 함. nginx 포트(80)를 수신 할 서버 블록을 열고 요청에 대한 블록을 추가함. proxy_pass 를 사용하여 정의한 소켓 (app.sock ---> flaskapp) 에 요청을 전달함. 그런 후 방금 만 든 서버 블록을 활성화 하기위하여 app 파일을 site-enabled 디렉토리(사용할 디렉터리) 로 연결함 (ln -s /etc/nginx/sites-available/app /etc/nginx/sites-enabled). nginx

프로세스를 다시 시작하고, nginx 서버에 대한 액세스를 허용하도록 방화벽을 조정함 (sudo ufw enable 'Nginx Full')* 웹 브라우저에서 서버의 도메인 이름으로 이동하면 웹 사이트를 볼 수 있다.

D. 프로젝트 개발 결과물 소개 (+ 다이어그램)

 클라이언트 측
 
![alt text](https://i.ibb.co/MVrdnMP/4.png)


• Cognito : 익명 게스트 사용자(유저) 가 aws 서비스(rekognition) 에 액세스하기 위한 임시 자격 부여

• AWS Rekognition : 유명인사 인지 API 서비스 제공

• HTML 파일 : 사용자가 접속했을 때 rendering 되는 웹페이지 서버 측

![alt text](https://i.ibb.co/HYXz1FJ/5.png)

• flask : 사용한 웹 앱 프레임워크

• AWS EC2 : 서버용 인스턴스

• AWS Elastic IP : 서버의 고정 dns 와 아이피 할당

• nginx : 리버스 프록시, 캐시 및 로드 밸런서, 웹서비스

• gunicorn : 웹 애플리케이션 코드를 실행하는 Python WSGI 서버. nginx 와 flask 의 중간자 역할 수행

E. 개발 결과물을 사용하는 방법 소개 (+ 프로그램 구동 화면 스 크린 샷 첨부)

**서버 구동**

![alt text](https://i.ibb.co/sKSsbJr/1.png)






aws ec2 인스턴스 구동 ---> system unit file 의 설정에 의해서 자동적으로 guniconr, flaskapp 서비스 실행.

![alt text](https://i.ibb.co/yPt14GL/2.png)

site-available 서버블록에 저장해준 서버 dns 이름으로 웹사이트에 접속 가능

**클라이언트 측**
 	![alt text](https://i.ibb.co/MfLq6Vn/3.png)


F. 개발 결과물의 필요성 및 활용방안

사진 속의 인물을 보고 그 사람이 유명인사인지 아닌지, 만약 유명인사라면 누구인지 알고 싶은 경우가 있다. 그럴 때, 이 앱을 사용하면 궁금증을 해소 할 수 있다.
