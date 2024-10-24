### 1. 프로젝트 소개
#### 1.1. 배경 및 필요성
> 현대 사회에서 공공장소나 기업, 주거 지역 등에 설치된 CCTV들은 실종자 탐색에 중요한 도구로 활용된다. 하지만 이러한 영상 데이터를 효과적으로 분석하고 활용하는 것은 여전히 어려운 과제이다. 최근 컴퓨터 비전 기술은 빅 데이터와 인공지능의 발전으로 급속히 발전하고 있다. 객체 감지, 얼굴 인식, 특징 추출 등의 기술을 활용하여 비디오 데이터를 자동으로 분석하고 해석할 수 있는 기술이 발전되고 있다. 영상 데이터의 분석이 텍스트 기반의 검색으로 가능하다면 많은 시간과 인력 비용을 감소시키는 효과가 있을 것이다. 특히, 실종자를 이른 시간 안에 찾는 것이 중요하기 때문에 이 과정을 자동화하고 최적화하는 것은 사회적 안전을 강화하는 데 필수적이다.
>
> 기존의 영상 데이터 분석은 주로 사람이 직접 영상을 확인하며 실종자를 찾는 방식으로 이루어졌다. 이는 시간과 비용이 많이 들고, 많은 인력을 요구한다. 또한 사람이 실수하거나 놓칠 수 있는 부분이 많다. 본 시스템은 컴퓨터 비전 기술을 통해 자동으로 영상 데이터를 분석하고 용의자를 탐색함으로써 수작업 분석의 비효율 문제를 해소할 수 있을 것이다.

#### 1.2. 목표 및 주요 내용
> 컴퓨터 비전 기술을 활용하여 영상 데이터를 자동으로 분석하고, 특정 쿼리에 따라 실종자를 탐색하는 시스템을 개발한다. 이를 통해 수작업 분석에 필요한 시간과 비용을 절약하고 효율성을 높일 수 있다. 시스템은 다양한 특징 쿼리를 지원하여 사용자가 원하는 조건에 따라 용의자를 검색할 수 있도록 한다. 시스템은 높은 정확도로 용의자를 식별할 수 있어야 하며, 동시에 영상 데이터를 빠르게 처리할 수 있어야 한다. 사용자가 쉽게 특정 쿼리를 입력하고 결과를 확인할 수 있도록 사용자 친화적인 인터페이스를 제공한다.

### 2. 상세설계
#### 2.1. 시스템 구성도
> ![구성도](https://github.com/user-attachments/assets/6a8f1057-b0ba-45b7-b218-a444a2a95f85)


#### 2.1. 사용 기술
> Backend
> - Python 3.10.9

> - MongoDB 7.0.12
> - fastAPI 0.110.3  
> - pip 23.0.1
> - pipenv 2024.0.1
>
> Frontend
> - Svelte 4.2.18
> - Node.js 10.19.0
> - npm 6.14.4

### 3. 설치 및 사용 방법
> 1. 저장소 클론
> ```bash
> git clone https://github.com/pnucse-capstone-2024/Capstone-2024-team-10.git
> cd Capstone-2024-team-10
> ```
> 2. dependency 설치
> ```bash
> cd wanted_frontend
> npm install
> pip install pipenv
> pipenv --python 3.10
> pipenv install
> ```
> 3. DB 실행
> ```bash
> sudo -u mongodb mongod --config /etc/mongod.conf
> ```
> 4. backend 실행
> ```bash
> pipenv shell
> pipenv run start
> ```
> 3. front 실행
> ```bash
> npm run dev
> ```
> 4. https://localhost:8080 접속

### 4. 소개 및 시연 영상
> [원티드 시연영상](https://www.youtube.com/watch?v=dQw4w9WgXcQ)
수정필요

### 5. 팀 소개
>  김정민
>> 모델 튜닝, DB구축, fronted 개발
>>
>> Email: jmk445@pusan.ac.kr
>>
>  이영민
>> DB 테스트, CCTV 영상 탐색, 시스템 테스트
>>
>> Email: ahdzl126@pusan.ac.kr
>>
>  이창욱
>> 추론 코드 개발, backend 개발
>>
>> Email: ckddnr5527@pusan.ac.kr
