0. 주제
<전자책 텍스트 분석을 통해 분위기에 맞는 배경음악을 제공해주는 백그라운드 앱>



0.1. 사용 기술
사용자의 화면을 캡쳐하여 텍스트를 인식하는 ocr 기술
텍스트를 다중감성분류하는 nlp 기술
음악을 다중감성분류하는 딥러닝 기술 


0.2. 기능
메인기능: 실시간 ocr 수행을 기반으로 한 텍스트 분위기에 맞는 배경음악 제공
부가기능: - on/off, - 음악 장르 취향 설정,  -음악의 fade효과, -예민도 등...


0.3. 진행
 23년 1학기 캡스톤디자인과창업프로젝트 그로스팀
 현재 개발 완료 단계에 있으며, 저자는 '딥러닝-mysql을 연동하는 aws 서버' 을 담당했다. 
 postman, putty, filezilla, vscode, mysql, aws, firebase등을 사용했다.



1. 데이터 관리
1.1 음악 데이터

firebase web 개발, firebase storage sdk를 사용했다

![image](https://github.com/BGM-BookGroundMusic/BGM_Server/assets/96529879/12941586-bd4b-4ca9-ad66-fae19e3859f5)





음성 데이터 다중 감성 분류기를 통해 분류하였다.

위 사진은 '슬픔'으로 판단된 감성을 저장한 firebase storage 모습이다.



![image](https://github.com/BGM-BookGroundMusic/BGM_Server/assets/96529879/6fcf2c74-9fb2-4a41-b6ac-12dab28a1113)



중립음악의 경우, 분류기의 결과가 '중립'이다.

위 사진은 '중립'으로 판단된 로파이 장르의 음악을 저장한 firebase storage 모습이다.





1.2. 음악 메타(meta)데이터 
1.2.1. mysql 연동
https://poiemaweb.com/nodejs-mysql

https://myinfrabox.tistory.com/213




위의 사이트를 참고하여 mysql을 연동하였다.

![image](https://github.com/BGM-BookGroundMusic/BGM_Server/assets/96529879/9126865b-5301-4d5b-9768-42c3e8edb441)







1.2.2. test relation

![image](https://github.com/BGM-BookGroundMusic/BGM_Server/assets/96529879/27951da1-ab68-4bcb-bebb-53059fd39f62)


test relation은 다음과 같은 구조(schema)를 가진다.
![image](https://github.com/BGM-BookGroundMusic/BGM_Server/assets/96529879/3f3d4bdd-f51d-4f52-8b4c-694253e07323)

type	emotion	name	locate	genre
int	varchar	varchar	varchar	varchar


type : 1 감성음악/ 2중립음악 (not null)
emotion: sad, happy등과 같은 감성, 중립음악의 경우 '중립'을 가짐 (not null)
name : 음악 파일명 (not null)
locate: 음악이 위치한 firebase storage 위치 (not null)
genre: 음악장르



2. 개발 환경 설정
2.1. aws ec2
![image](https://github.com/BGM-BookGroundMusic/BGM_Server/assets/96529879/e5b23214-efc7-4aea-9cb1-b7fcce353efa)



aws ec2를 사용하여 api를 배포했다.

무료티어를 사용했으며, ubuntu 개발로 설정했다.

보안 그룹은 다음과 같다. 

nodejs 코드는 3001포트에서 실행되며, ssh는 22포트에서 실행하도록 설정했다.

![image](https://github.com/BGM-BookGroundMusic/BGM_Server/assets/96529879/839a2f08-16be-48d3-a2ed-278758df62a7)


2.2. putty

![image](https://github.com/BGM-BookGroundMusic/BGM_Server/assets/96529879/0ccab409-a256-495c-ad09-dc4dc8d37555)



putty ubuntu를 사용했다.

hostname, port number, SSH-auth를 입력해주면 된다.

이때, hostname은 개발 인스턴스의 public ipv4, 

port number은 앞서 언급했던 22,

SSH-auth은 인스턴스 생성시 사용했던 private key를 넣어준다.









2.3. filezilla


![image](https://github.com/BGM-BookGroundMusic/BGM_Server/assets/96529879/ae98edfc-56bc-43fa-9a1c-52ced65abad8)




filezilla를 통해 코드 파일을 전송했다.

이때 호스트명을 인스턴스의 public ipv4로 설정해야 한다.






2.4. aws, putty, filezilla 연동
https://copycoding.tistory.com/424

위 사이트를 참고했다


2.5. postman

![image](https://github.com/BGM-BookGroundMusic/BGM_Server/assets/96529879/d0bd4d2a-a1c5-4163-86be-f8619b8ccea1)



postman을 사용하여 params와 body request에 대한 api 작동을 확인했다.








3. api 코드
3.1. setting
![image](https://github.com/BGM-BookGroundMusic/BGM_Server/assets/96529879/1fbc4419-ff15-4d70-b22f-c44b6917de45)

상단


require을 통해 필요한 라이브러리를 불러왔다.

express는 app을 위해, child_process는 nlp코드 연동을 위해,

body-parser는 body request를 받기 위해 불러왔다. 

port=3001로, aws 설정 포트와 맞췄다.





![image](https://github.com/BGM-BookGroundMusic/BGM_Server/assets/96529879/2409f10e-a636-430f-a942-189dd4aeca59)

하단
코드의 가장 하단에는 위와 같은 모습이다.

app.listen을 통해 대기중인 모습이다.

api를 app이라는 이름의 module로 저장했다.







3.2. (nlp)딥러닝 모델과 연동 & 감성분석 결과


![image](https://github.com/BGM-BookGroundMusic/BGM_Server/assets/96529879/2b6c8cb7-bb15-4699-94e0-d0b44346cb34)



datatext를 process.py(nlp모델) 의 input 값으로 줬을때, 그 결과값(print문)을 저장하는 코드이다.



이때, getemotion코드는 async function으로 정의해야 한다.

또한 이 전체 코드를 async func를 처리하기 위해 Promise로 묶었다.



spawn을 통해 python 컴파일을 실행했고, 'close', 'error'문을 작성하여, 

컴파일 성공과 실패에 대한 핸들링 로직을 만들었다.



예측 결과는 prediction에 저장했다.





3.3. request params, body


![image](https://github.com/BGM-BookGroundMusic/BGM_Server/assets/96529879/728c6db4-9e44-4a99-b661-8b4b0ac9fd9e)



request params, body를 받아와야 한다.

root url에 대한 코드를 작성함으로써, 항상 초기에 실행되도록 만들었다.



genreId, modeId는 params이고, datatext는 body이다.

이때 genreId, modeId는 sql query를 위해 사용된다.

datatext는 3.1.의 딥러닝 모델 input으로 사용된다.



try catch문을 통해 datatext-딥러닝 모델 연동을 체크했다.



request value	의미	사용처
genreId	음악 장르(=genre)	sql query 속 attribute
modeId	재생모드(예민도)	sql query를 위한 조건문(if,elif,else) 속 조건
datatext	OCR 결과 텍스트	nlp 모델의 input


![image](https://github.com/BGM-BookGroundMusic/BGM_Server/assets/96529879/76d40201-fc4a-4824-8abe-59eb220516ae)




3.4. sql 연동 & 음악 위치 데이터 


![image](https://github.com/BGM-BookGroundMusic/BGM_Server/assets/96529879/8a4e7dae-ac94-433a-b3c0-a1d937ad684a)

![image](https://github.com/BGM-BookGroundMusic/BGM_Server/assets/96529879/a9d3d884-55e9-4102-b26b-98600f689b4f)

![image](https://github.com/BGM-BookGroundMusic/BGM_Server/assets/96529879/8e20b26c-a76c-4998-ac1f-23fa29aa31f2)





my sql와 연동하여, 음악 메타데이터를 반환하는 async func get_path 코드다.

 이 코드를 async func를 처리하기 위해 Promise로 묶었다.

modeId에 따라 세가지 조건문으로 나뉜다.

if modeId=='1'
else if modeId=='3'
else






modeId가 1일때는 감성음악만을 재생해야 한다. 

nlp모델 예측 결과인 prediction에 해당하는 emotion을 가진 row의 locate attribute를 가져와야 한다.

따라서 sql query를 다음과 같이 작성된다.

SELECT locate FROM test WHERE emotion='${prediction}




modeId가 3일때는 중립음악만을 재생해야 한다.

nlp예측 결과와 상관없이, 음악종류(type)이 2이고, emotion='중립' 을 가진 row의 locate attribute를 가져와야 한다.

또한 이때, 사용자가 설정한 취향 장르인 genre까지 고려해야 한다.

따라서 sql query를 다음과 같이 작성된다.

 SELECT locate FROM test WHERE type='2' AND genre='${genreId}' AND emotion='중립'




modeId가 2일때는 감성+중립음악을 재생해야 한다.

감성음악은 prediction에 의존하고, 중립음악은 genreId에 의존한다.

두 조건을 or로 묶어서 사용했다.

따라서 sql query를 다음과 같이 작성된다.

SELECT locate FROM test WHERE emotion='${prediction}' or (emotion='중립' and genre='${genreId}'





![image](https://github.com/BGM-BookGroundMusic/BGM_Server/assets/96529879/c244d8b9-4487-45af-851a-9df2bac0615e)




최종적으로 음악 위치인 locate 데이터가 담긴 리스트가 반환된다.

path 리스트는 json 형식으로 변환했다.









3.5. 최종코드1 : 음악 위치 반환

![image](https://github.com/BGM-BookGroundMusic/BGM_Server/assets/96529879/0805d6a9-56a3-44c2-b5e8-b1c8056be7cc)




/play url에 해당하는 코드이다.

async function으로 작성했다. 

왜냐하면, 일부 함수가 async func이기 때문이다.

1. 가장먼저 root url에 의해, genreId, modeId, datatext가 생성된 상태에서, 진행된다.

2. getemotion 함수를 통해 nlp 모델결과로 감성prediction을 얻는다.

3. get_path 함수를 통해 sql 결과로 원하는 음악의 위치 데이터를 얻는다.

4. 테이블 형식(row)의 데이터를 형변환한다.

5. finalPath를 json형태로 반환하여 응답한다.





3.6. 최종코드2 : 감성 반환

![image](https://github.com/BGM-BookGroundMusic/BGM_Server/assets/96529879/c3cf1968-c1c5-46f8-aa80-e85c91aeca49)


/emotion url에 해당하는 코드이다.

async function으로 작성했다. 

왜냐하면, 일부 함수가 async func이기 때문이다.

1. 가장먼저 root url에 의해, genreId, modeId, datatext가 생성된 상태에서, 진행된다.

2. getemotion 함수를 통해 nlp 모델결과로 감성prediction을 얻는다.

3. prediction을 json형태로 반환하여 응답한다.









4. 제언
저자는 firebase, flask를 이용한 api를 개발하려고 했지만, api 배포에 실패하였다.

원인은 firebase hosting에 있었으며, 독자들에게 fibase를 통한 api배포를 추천하지 않는다.



두번째로 시도한 것이 nodejs, aws, filezilla, putty, sql을 이용한 api개발이다.

연동해야 할 기술이 많아 더 복잡하지만, 한번 익히면 그 이후에는 쉬운 방법이다.

api 배포시 방화벽에 막혔는데, 저자는 방화벽을 뚫었지만, 이것이 싫다면, 보안 설정을 잘 하는 것이 좋다.







5. 참고문헌
https://poiemaweb.com/nodejs-mysql
https://myinfrabox.tistory.com/213
https://copycoding.tistory.com/424
https://velog.io/@kws60000/AWS-EC2-Node.js-%EC%84%9C%EB%B2%84-%EC%97%B0%EA%B2%B0-%EC%95%88%EB%90%A0-%EB%95%8C-%EC%82%AC%EC%9D%B4%ED%8A%B8%EC%97%90-%EC%97%B0%EA%B2%B0%ED%95%A0-%EC%88%98-%EC%97%86%EC%9D%8C








