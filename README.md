
# <div align="center">Project : 캠핑을떠나보자</div>
### <div align="center">캠핑 초심자를 위한 데이터 분석 및 추천 서비스 기능 구현</div>

### <div align="right">Team Name <br> 저오늘떠나조</div>
#### <div align="right">Team Member <br> 김건우(조장), 계은서, 박수진, 유제영, 정가영 </div>
___

### <div align="center">발표자료 및 자세한 내용은 아래 프레젠테이션 PDF 참고
<div align="center">https://github.com/rjs6418/KDT_DataEngineeringProject_3/blob/master/3%EC%A1%B0_%EC%A0%80%EC%98%A4%EB%8A%98%EB%96%A0%EB%82%98%EC%A1%B0.pdf</div>
<br>

<br><br>
## <div align="center">프로젝트 기획 배경 및 목표</div>
> <div align="center">코로나가 끝나간다는 소식이 들려오고,</div> 
> <div align="center">거리두기 규제도 점차 완화 되면서, </div> 
> <div align="center">사람들이 점차 떠나기 시작했다.</div>
> <br>
> <div align="center">캠핑을 떠나고 싶다면 뭐부터 준비하고 알아봐야하는지 막막한 사람들에게</div>
> <div align="center">캠퍼가 될 수 있는 정보를 제공하기 위해 기획하였다.</div>
<br><br>


### 전체 파이프라인 구성도
![image](https://user-images.githubusercontent.com/101792115/190812822-e438f99b-d0d6-4c17-8347-002c220b620f.png)
- 캠핑장 데이터를 위한 네이버 플레이스 데이터 수집
- 캠핑용품 데이터를 위한 쿠팡 캠핑관 데이터 수집
- 캠핑용품 중고거래 데이터를 위한 초캠(캠핑1위커뮤니티/네이버카페) 데이터 수집
- 현 사용자가 크롬(네이버, 구글, 유튜브 등)에서 검색하는 키워드를 추출하고, 수집된 네이버플레이스 캠핑장 데이터를 활용하여 캠핑장을 슬랙으로 추천 

### 데이터분석에선 데이터 처리는 SparkSQL을 활용하고, 시각화는 matplotlib을 활용

## 캠핑장 데이터 분석
![image](https://user-images.githubusercontent.com/114065168/191510216-16957760-7d84-4cf0-af9c-aff3a8f2addf.png)<br>
캠핑장 검색 시 나오는 해당 페이지 정보를 수집하였고, 아래와 같이 정리하였다. <br>
![image](https://user-images.githubusercontent.com/114065168/191510398-233eb79f-e4b2-4f50-8cd1-02bdf88d2102.png)<br>
여기서 네이버 플레이스 정보 중 가장 하단에 있는 방문자리뷰 특성 데이터를 적극 활용하여 분석하였다.<br>

![image](https://user-images.githubusercontent.com/114065168/191511113-51ae88eb-8404-4db7-80b4-5ec8c778b05d.png)<br>
리뷰 특성별 선호 지표가 캠핑장마다 달랐는데 전반적으로 위와 같은 분포를 보였다.<br>
'깨끗해요', '온수가 잘 나와요', '조용히 쉬기 좋아요', '화장실이 잘 되어있어요'의 지표가 높은 수치를 보이고 있다.<br>

![image](https://user-images.githubusercontent.com/114065168/191708964-1df7966d-3a78-4cc4-a50f-aae530456b2b.png)

해당 데이터를 지역별로 구분하여 나타낼 경우, 진한색의 지표가 전국적으로 높은 분포의 지표보다 낮게 형성되어있다.<br>
해당 지역에서는 캠핑장 방문의 추가적인 고려가 필요하다.<br>

## 캠핑장 추천 기능
### 현 사용자가 크롬(네이버,구글,유튜브 등)에서 검색하는 키워드를 기반으로, 캠핑장을 슬랙으로 추천하는 기능이다.<br>

- 먼저 아래의 명령어를 실행입력하여, 크롬을 실행시킨다.<br>
- /Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome --enable-logging=stderr --v=1 2>&1 | tee -a /Users/kuno/code/pipeline/chrlog.txt<br>
> 위의 명령어를 실행 시켜 크롬을 돌리는 이유는<br>
> 크롬을 사용하면서 데이터가 여러 파일에 저장되는데(쿠키, 히스토리 등), <br>
> 그때 생기는 데이터 파일의 변화를 지속적으로 수집하기 위해, <br>
> 로그스태시의 JDBC, SQLite, file 등 여러 플러그인을 시도 해보았으나,<br>
> 보안 문제로 사용이 매우 안되거나, <br>
> 사용 할 수 있다고 해도 해당파일의 업로드가 지속적이지 않아 용성에 큰 문제가 있거나, <br>
> 플러그인이 해당파일에 접근이 안되는 상황이었다.<br>
> <br>
> 끝내 찾은 방법이 크롬 실행 시, 디버깅 로그를 1레벨로 발생 시키고, 출력된 로그를 txt 파일로 옮겨 저장시킨 후,<br>
> 해당 파일에 플러그인을 연결하는 방법이었다.<br>
> 이렇게 할 경우, 데이터가 지속적으로 밀려들어오며 검색어를 포함한 로그들을 수집할 수 있다.<br>
- 그렇게 동작시킨 크롬 위에서 찾고자 하는 캠핑장을 검색하면, 그에 대한 로그가 발생한다.  
- 크롬 로그 데이터들을 Filebeats로 수집하고, <br>
- logstash에서 mutate와 grok 함수를 통해 검색키워드를 포함한 uri와 타임스탬프를 필터링 한다. <br>
- 그 후 필터링된 데이터를 카프카에 전송하고 시간과 키워드만을 추출한다.<br>
- 그렇게 추출된 캠핑장 이름을, 수집한 데이터에 검색한 후 리뷰, 가격, 위치 데이터를 누적하고,<br>
  누적된 데이터를 종합하여 비슷한 수준의 리뷰, 가격, 위치를 가진 캠핑장을 다시 찾아내어 추천한다.<br>
![image](https://user-images.githubusercontent.com/114065168/191709900-915343a1-3bee-4c04-82fc-73559f64efd4.png)<br>
![image](https://user-images.githubusercontent.com/114065168/191709957-7f859396-5de4-4c8c-87b1-fdbc7be53c5b.png)
- 같은 캠핑장을 세번 이상 검색 시 선호 키워드로 등록되게 설정하였다.<br>
  
> kafka에서 가공 가능한 부분일 수 있으나, Filebeats와 logstash를 선택한 이유는 <br>
> 지속적으로 데이터가 생성되어 변하는 파일을 모니터링 할 수 있는 기능이 필요했다. <br>
> 이와 같은 스트림 처리엔 위의 플랫폼이 가장 우수하다 판단하었다. <br>

## 캠핑용품 데이터 분석

캠핑용품 데이터는 쿠팡에서 크롤링하였다<br>
![image](https://user-images.githubusercontent.com/114065168/191520328-7ba7a7dd-ec3c-4fae-a5df-682f6cc76f5e.png)<br>
다만, 동일한 상품의 중복 노출로 데이터를 가려낼 필요가 있었다.<br>
이름의 앞부분과 리뷰 수가 같다는 특이점을 발견하였고,<br>
상품명 앞 세 단어가 같고 가격도 같은 상품은 동일 제품이라고 간주하고 삭제하였다.<br>

![image](https://user-images.githubusercontent.com/114065168/191522851-07e83ae8-110f-4a59-af5e-122b85a756af.png)<br>
![image](https://user-images.githubusercontent.com/114065168/191523013-b976eced-b2a3-4b5c-8c0f-b262ef526704.png)<br>
![image](https://user-images.githubusercontent.com/114065168/191523049-9625b036-9ec7-4f44-853e-3046c42890c7.png)<br>


카테고리별 가격과 판매량 순위를 비교한 그래프이다.<br>
판매순위와 가격에 대한 상관성이 각각 다르다<br>
위의 4개 카테고리는 전반적으로 판매 순위와 관계없이 저렴한 상품이 인기이므로 저렴한 상품 위주로 구매해도 무방하며,<br>
아래 6개 카테고리는 가격보다도 개인의 선호나 브랜드에 따라 다를 것으로 판단된다.<br>

![image](https://user-images.githubusercontent.com/114065168/191523842-93d18ec1-e328-452a-b654-f9f6af0de03d.png)<br>
카테고리별 브랜드를 키워드로 추출하여 상위 순으로 나타낸 그래프이다. 구매 시 참고하자<br>

![image](https://user-images.githubusercontent.com/114065168/191524241-8d6d6638-8f47-41cf-9cb3-ed4f5b32fe59.png)<br>
위의 데이터는 판매량과 리뷰수의 상관관계를 나타낸 그래프이다.<br>
위의 카테고리에서는 유독 강한 상관관계의 모습을 보이므로 해당 카테고리 상품 구매 시 리뷰를 참고하면 좋다.
<br>
 
## 중고거래 데이터 수집

![image](https://user-images.githubusercontent.com/114065168/191524550-914d23cc-fd17-49f7-a114-a52d69d106f9.png)<br>
중고거래 특성상 1회성 거래이므로, AWS상에서 Airflow를 활용하여 주기 별 데이터 수집을 통해 높은 데이터 품질 유지하였다.
<br>

## 중고거래 데이터 분석

![image](https://user-images.githubusercontent.com/114065168/191524860-8e17bcea-370d-4deb-9b00-b16e5ac9d096.png)<br>
기본적으로 가격대가 높은 텐트/타프의 거래가 가장 많고, 상위권 게시글의 판매완료 비율이 높아 거래가 활발하단 것을 알 수 있다.<br>
![image](https://user-images.githubusercontent.com/114065168/191525383-9e3efe82-99f8-4237-9a46-cef253ced7b4.png)<br>
그중 노스피크와 스노우피크 브랜드가 전체, 변화량 기준에서 거래가 활발하다.<br>
위의 브랜드로 구매한다면 향후 중고거래 가격 방어에 유리할 것으로 판단된다.

### 추가 개선 방향
- 추천에 대한 로직을 구체화하여 정확도를 향상하거나 머신러닝까지 활용 목표
- 쿠팡 데이터의 경우 상품별 세분화된 속성까지 수집하여 데이터 및 정보 활용 극대화
- 단일 플랫폼이 아닌 여러 중고거래 플랫폼 활용하여 데이터 활용도를 향상

### 관리미흡으로 인해 캠핑장 데이터 처리 SparkSQL 활용 분석 및 시각화 코드 분실
