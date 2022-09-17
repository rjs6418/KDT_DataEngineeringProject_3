# 캠핑을떠나보자
## 저오늘떠나조
김건우, 계은서, 박수진, 유제영, 정가영


### 캠핑 초심자를 위한 데이터 분석 및 추천 서비스 기능 구현



### 자세한 내용은 프레젠테이션 PDF 참조
https://github.com/rjs6418/KDT_DataEngineeringProject_3/blob/master/3%EC%A1%B0_%EC%A0%80%EC%98%A4%EB%8A%98%EB%96%A0%EB%82%98%EC%A1%B0.pdf

### 서비스 중심의 전체 파이프라인 구성도
![image](https://user-images.githubusercontent.com/101792115/190812822-e438f99b-d0d6-4c17-8347-002c220b620f.png)



### 임의의 가 데이터를 활용하기 보다 실제로 발생하는 데이터를 활용하고자, 크롬 디버깅 로그를 출력하여 해당 데이터를 스트림 처리하여 추천 시스템에 활용
/Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome --enable-logging=stderr --v=1 2>&1 | tee -a /Users/kuno/code/pipeline/chrlog.txt
### (크롬로그 파일, 쿠키 파일 등에 JDBC, SQLite 외 여러 Logstash의 플러그인들을 사용 해보았으나, 원하는 수준의 속도가 퍼포먼스가 발생하지않아 위와 같은 방법 사용)

### SparkSQL 쿼리를 활용하여 데이터 분석 실시
### 관리미흡으로 인한 SparkSQL 활용 분석 및 시각화 코드 일부 분실
