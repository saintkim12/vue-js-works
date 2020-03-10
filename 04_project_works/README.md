# 개요
kihasa-manager 프로젝트가 어떻게 구성되어 있는지 설명함

# 구성 설명(dataportal 프로젝트와 비교해서)
| |dataportal|kihasa-manager|
|:--|:--|:--|
|구성|app과 web 두개의 서버로 구성|상동|
|백엔드(app) 작동방식|spring boot의 api 방식|상동|
|프론트엔드(web) 작동방식|thymeleaf를 엔진으로 사용한 spring boot 방식|Vue CLI로 작업된 정적 페이지를 웹서버(spring boot)에 띄우는 방식|
|프론트엔드 페이지 작업폴더|webpages|frontend|
|프론트엔드 개발방식|spring-boot dashboard에서 web서버 실행하고 작업, 확인|Vue CLI 개발서버를 띄워 작업 후 확인|
|프론트엔드 배포방식|작업된 파일을 (gradle을 통해) web에 복사하여 jar 생성|작업된 파일을 (Vue CLI을 통해) 빌드 후 빌드된 파일을 (gradle을 통해) web에 복사하여 jar 생성|
|디자인 작업폴더|webpages_design|없음(개발자가 직접 작업)|

# 기타 구성사항
- 프로젝트 명에서 보듯이, m114의 보사연 서비스들을 통합 관리하는 페이지로 구상중인데, 일단은 데이터포털 관리자 서비스만 작업하여 띄울 거임
(아직 OK받은게 아니어서)
- (통합) 관리자 메인 - (각 서비스) 관리자 페이지의 형태로 구성 중
- 각 서비스 관리자 페이지는 SPA로 구성중(메인 + 여러 개의 SPA들로 이루어진 MPA로 생각하면 됨)
- 관리자 페이지이다 보니까, 로그인 외의 페이지는 token 정보가 있어야 작동이 가능함