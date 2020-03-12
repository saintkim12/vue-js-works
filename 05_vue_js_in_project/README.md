# 개요
kihasa-manager 프로젝트 작업 방법을 설명
# 백엔드
dataportal과 동일하나, dataportal을 sub 경로로 넣어야 하는 것들이 많을 것임
# 프론트엔드
## 사전작업
기존 dataportal jsp 소스
기존 dataportal miplatform 소스 + 화면을 보기 위한 개발 도구

## 작업 방법
1. 구글 엑셀 시트에 작업할 페이지 확인
    - 메뉴를 현재 사용자 기준으로 변경함
1. 필요한 페이지 확인
    - 기존 JSP 및 사용자 페이지를 주로 참조하기
    - 마이플랫폼 페이지는 옛날에 작업한거라 안쓰는 것도 있고 바뀐 것도 있어서, 참고용으로만 보는게 좋음
1. 필요한 페이지 작업 방향 설정
    - 페이지 유형 확인(목록/상세/등록,수정/삭제/커스텀...)
    - 필요한 기능들 확인
    - 페이지 설계??
1. 페이지 작업(백엔드는 생략)
    1. 공통 부분을 제외한 페이지(*.vue)는 frontend/src/views/dataportal 내에 있음
        - Dataportal.메뉴.기능(.유형).vue 형태로 구성
    1. 목록 페이지는 base-table, 등록/수정 페이지는 form 및 vulidate(유효성체크, 나중에 해도 됨) 부분 참고...

## 목록 페이지 작업
공통컴포넌트 > base-table 참고

## 등록/수정, 조회 페이지 작업



# 공통 컴포넌트
## router, routeTo
기존 dataportal의 constants의 menuId 처럼, 페이지의 경로를 직접 입력하지 않고 상수로 처리하기 위한 방안으로 사용하였다.
### const Route
frontend/src/entry/dataportal/router.js 파일을 보면, const ROUTE로 사용하는 메뉴들에 대한 이름이 정의되어있다.
```js
// frontend/src/entry/dataportal/router.js
const ROUTE = {
  LOGIN: 'login',
  MAIN: 'main',
  PROJECT: {
    DATA_BANK: { LIST: 'project.databank.list', WRITE: 'project.databank.write' },
  // ..
  }
}
```
### ROUTE
이 value들은 vue-router의 각 페이지의 이름으로 매핑되어 라우트 페이지로 이동하는데, 이렇게 구성한 주요 이유는 ROUTE라는 값을 사용해 페이지의 경로를 직접 입력하지 않도록 하기 위해서이다.

### routeTo
- 이렇게 생성된 ROUTE 값을 routeTo라는 함수를 이용해 페이지를 이동할 때 사용할 수 있다.
(frontend/src/entry/dataportal/RouteHelper.vue 안에 있음)
```js
({
  // ..
  async save() {
    // ..
    // after save
    // ROUTE라는 변수를 include하지 않고도 사용하기 위해 함수 형태로 전달한다
    this.routeTo((ROUTE) => ROUTE.PROJECT.DATA_BANK.LIST)
  }
  // ..
})
```
```html
<!-- ROUTE 값은 html(template) 내에서는 routeTo의 파라미터를 함수형태로 선언하지 않고 바로 사용할 수 있다. -->
<button @click="routeTo(ROUTE.PROJECT.DATA_BANK.LIST)">목록으로</button>
```

## pageNavHelper
![pageNavHelper](/img/2020-03-10-14-41-19.png)
page nav를 생성해주는 함수
```js
export default {
  // ..
  data() {
    return {
      // ..
      ui: {
        // 전개 구문(es6)을 사용하여 값을 리턴시킨다
        ...this.pageNavHelper((ROUTE) => [
          // ROUTE를 함수형태로 제공하고, 보여줄 메뉴를 순서대로 ['명칭', ROUTE값]으로 출력한다.
          // ROUTE값이 최종 메뉴값이 아니라면, 그 하위 메뉴 중 최상단을 자동으로 선택한다(ROUTE.PROJECT => ROUTE.PROJECT.DATA_BANK.LIST)
          // 배열 형태가 아니라면 ROUTE할 값이 아니므로 비활성화함
          ['연구과제', ROUTE.PROJECT], ['DataBank', ROUTE.PROJECT.DATA_BANK.LIST], '목록'
        ]),
      // ..
}}}}
```


## base-table
일반적인 게시판 형태(테이블, 검색폼, 페이징) 처리를 하는 컴포넌트
### 사용법
1. 컴포넌트 import 및 components에 연결
    ```js
    import BaseTable from '@/m114-common/components/base-table'
    export default {
      // ..
      components: {
        BaseTable
      },
      // ..
    }
    ```
1. 필요한 data, computed 생성
    ```js
    // ..
    import { apiUrl } from '@/m114-common/util/env'
    // ..
    export default {
      // ..
      data() {
        return {
          init: {
            pagination: {}
          },
          ui: {
            // ..
            table: {
              apiUrl: `${apiUrl}/...`, // 백엔드 API URL
              fields: [
                // 출력할 데이터 설정부
              ].map((field, cellIndex) => /* global-settings */ Object.assign({ cellIndex, thClass: ['text-center'], tdClass: ['text-center'], sortable: false }, field))
            }
          },
          form: {
            table: {
              // 테이블과 연결된 검색 조건부
              searchText: ''
            }
          },
          // ..
        }
      },
      computed: {
        // 테이블 설정용 값
        tableSettings() {
          return {
            apiUrl: this.ui.table.apiUrl, // 백엔드 API URL
            fields: this.ui.table.fields, // 테이블 헤더 + 출력할 데이터 설정
            form: this.form.table         // 검색 조건부 설정
          }
        }
      },
    }
    ```

1. html(template)부 작성
    ```html
    <template>
      <!-- .. -->
      <base-table ref="baseTable" :table="tableSettings" :init-pagination="init.pagination" @row-clicked="onRowClicked">
        <template v-slot:search>
          <!-- 검색 폼 부분 -->
          <!--
          <b-row>
            <b-col offset-lg="8" lg="4" class="my-1">
              <b-form-group class="mb-0">
                <b-input-group size="sm">
                  <b-form-input
                    v-model="form.table.searchText"
                    type="search"
                    placeholder="검색"
                    @keypress.enter="search"
                  ></b-form-input>
                  <b-input-group-append>
                    <b-button @click="search"><i class="fe-search"/></b-button>
                  </b-input-group-append>
                </b-input-group>
              </b-form-group>
            </b-col>
          </b-row>
          -->
        </template>
      </base-table>
      <!-- .. -->
    </template>
    ```

1. 테이블 출력 항목 작성
    ```js
    // ..
    export default {
      // ..
      data() {
        return {
          // ..
          ui: {
            // ..
            table: {
              // ..
              fields: [
                {
                  key: '..',          /* 컬럼의 구분값, 일반적으로 컬럼명의 camelCase */
                  label: '..',        /* thead에 출력할 텍스트 */
                  thClass: '',        /* th에 적용할 클래스(text-center, text-left, clickable...) */
                  thStyle: '',        /* th에 적용할 style */
                  tdClass: '',        /* td에 적용할 클래스(text-center, text-left, clickable...) */
                  tdStyle: '',        /* td에 적용할 style */
                  formatter: () => {} /* 출력되는 데이터를 변경 및 조합하고 싶을 때 사용
                                      * (value, key, item) => {} 형태의 함수로 사용 가능하며,
                                      * 자주 사용되는 formatter들이 @/m114-common/util/formatter 에 정의되어있음 */
                },
                {
                  /* .. */
                }
              ].map((field, cellIndex) => /* global-settings */ Object.assign({ cellIndex, thClass: ['text-center'], tdClass: ['text-center'], sortable: false }, field))
              /* 기타 공통부분 작업, cellIndex로 클릭 여부를 판단하고, 기타 공통 클래스(center로) 및 정렬(false)값 추가 */
            }
          }
          // ..
        }
      }
      // ..
    }
    ```

1. 필요한 함수들 설정
    ```js
    export default {
      // ..
      methods: {
        // ..
        // 검색 이벤트
        search() {
          this.$refs.baseTable.search()
        },
        // 행을 클릭했을 때 이벤트
        onRowClicked({ seq /* item의 key */ }, row, { srcElement: { cellIndex = -1 } }) {
          // item의 key로 클릭한 열의 인덱스를 알 수 있는 map 호출 
          const cellIndexMap = this.$refs.baseTable.getCellIndexMap()
          // 클릭 위치(cellIndex) 확인
          if (cellIndexMap[cellIndex] === 'surveyNm' /* 클릭한 열의 위치가 'surveyNm'(위의 fields의 key값)열이라면 */) {
            // routeTo 함수를 사용해 페이지 이동, 필요한 데이터(key 등)는 params에 담아 전달
            this.routeTo((ROUTE) => ROUTE.PROJECT.DATA_BANK.WRITE, { params: { seq } })
          }
        }
        // ..
      }
    }
    ```

1. 기타 lifecycle 함수 설정
    ```js
    export default {
      // ..
      // 파라미터(querystring)를 init.pagination에 설정
      created() {
        const param = this.$param.get()
        this.$set(this.init, 'pagination', param)
      },
      // 초기 생성 시 기본값으로 검색 기능 수행
      mounted() {
        this.$refs.baseTable.initSearch()
      },
      // ..
    }
    ```

### base-table/raw
페이징 등 다른 정보가 빠진 테이블

## bootstrap 관련
[bootstrap](https://getbootstrap.com/)
[bootstrap-vue](https://bootstrap-vue.js.org/)
- 기존 class로 컨트롤하는 기능 외에 추가적으로 컴포넌트 형태로 제공하는 vue용 library를 생성함
- 각 화면은 이 bootstrap-vue의 컴포넌트들의 객체를 써서 제공하고 있음
- 만약 bootstrap이 처음이라면 기본 bootstrap부터 보고 bootstrap-vue를 보는 방법으로 하는게 좋을듯
- 퍼블리싱 작업까지 하는 것이기 때문에 이부분은 막히는거 같으면 그냥 넘어가고, 나중에 한꺼번에 작업해도 될 거 같음

## vuelidate 관련
[vuelidate](https://vuelidate.js.org/)
- vue에서 유효성 체크하는 라이브러리
- frontend/src/m114-common/components/bootstrap-customize/vuelidate-wrapper 안에 bootstrap의 form과 결합한 공통 컴포넌트를 작업함
- 이것도 일단 그냥 넘어가고, 나중에 한꺼번에 작업해도 될 거 같음

## 파일업로드
- frontend/src/m114-common/components/base-upload/index.vue 참고
- 미완성? 확인 다 못함

## axios
[axios](https://github.com/axios/axios) - ajax 처리용 라이브러리
### 사용법
```js
// 기본 사용법(Vue plugin으로 인스톨하여, Vue 함수 내에서 this에 바인딩됨)
this.$http.get(/* .. */).then(/* .. */)

// frontend/src/plugin/axios.js 에서 커스터마이징 진행함
// 커스터마이징된 함수 사용
// progress(최상단에 프로그래스 바 생김) 모드
this.$http.p.post(/* .. */).then(/* .. */)
// loading(뺑글이) 모드
this.$http.l.put(/* .. */).then(/* .. */)
// token(로그인 토큰 검사 로직이 추가된) 모드
this.$http.t.delete(/* .. */).then(/* .. */)

// token + progress
this.$http.tp.option(/* .. */).then(/* .. */)
// token + loading
this.$http.tl.get(/* .. */).then(/* .. */)
```