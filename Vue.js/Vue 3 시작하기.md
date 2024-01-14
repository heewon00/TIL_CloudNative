# ****Vue 3 시작하기****

[Vue 3 시작하기 강의 - 인프런](https://www.inflearn.com/course/vue-3-시작하기)

[Introduction | Cracking Vue.js](https://joshua1988.github.io/vue-camp/textbook.html)

## Vue.js 소개

- 라우팅 : 페이지 간의 이동
- SSR : 서버 사이드 랜더링
    - https://joshua1988.github.io/vue-camp/nuxt/ssr.html
    - 서버에서 페이지를 그려 클라이언트(브라우저)로 보낸 후 화면에 표시하는 기법을 의미합니다.
    - ****서버 사이드 렌더링과 클라이언트 사이드 렌더링****
        - 어디서 화면에 보일 페이지의 내용을 그리느냐의 차이
        - 클라이언트 사이드 렌더링 - 페이지의 내용을 브라우저에서 그림
        - 서버 사이드 렌더링 -  서버에서 페이지의 내용을 다 그려서 브라우저로 던짐
            
            !https://joshua1988.github.io/vue-camp/assets/img/ssr-vs-csr.588d1b49.png
            
    - **서버 사이드 렌더링을 왜 쓸까?**
        - 1) 검색 엔진 최적화
            - 같은 검색 사이트에서 검색했을 때 결과가 사용자에게 많이 노출될 수 있도록 최적화 하는 기법
            - SNS에서 링크를 공유했을 때 해당 웹 사이트의 정보를 이미지와 설명으로 표시해주는 OG(Open Graph) Tag를 페이지 별로 적용하기 위해서는 서버 사이드 렌더링이 효율적입니다.
                
        - 2) 빠른 페이지 렌더링
- Nuxt : Vue.js로 SSR을 구현할 때 사용하는 기술
    - [https://joshua1988.github.io/vue-camp/nuxt/intro.html#nuxt의-장점](https://joshua1988.github.io/vue-camp/nuxt/intro.html#nuxt%E1%84%8B%E1%85%B4-%E1%84%8C%E1%85%A1%E1%86%BC%E1%84%8C%E1%85%A5%E1%86%B7)
    - Vue.js로 빠르게 웹을 제작할 수 있게 도와주는 프레임워크
- 코드 작성 방식
    - **옵션 API (for 초보)**
        
    - 컴포지션 API
        - setup + ref
        
        

## **Vue.js 핵심 동작 원리 : Reactivity**

### Reactivity 개념 / Reactivity 차이점 - Vue 2 & Vue 3

- https://v2.vuejs.org/v2/guide/reactivity.html

- vue2 : 속성마다 defineProperty의 API를 돌려야 한다는 단점
    - data에 각 속성이 미리 정의되지 않으면 reactivity가 주입되지 않았음

        
- vue3 : 객체를 통으로 모방하기 때문에 객체에 어떤 속성이 들어오든 간에 하나의 Proxy 동작으로 엮어낼 수 있음
    
    ```jsx
      var app = new Proxy(data, {
        get() {
          // app.num
          console.log('값 접근')
        },
        //target:객체의, prop:속성값에, newValue:새로운 값 부여
        set(target, prop, newValue) {
          // app.num=20
          console.log('값 갱신')
          target[prop] = newValue;
          //변경된 값이 반영됨
          render(newValue);
        }
      })
    ```
    

### proxy : 객체의 기본 동작을 재정의할 수 있음

- https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Proxy
- const & let
    - const는 재할당 불가하지만(but 내부 요소는 변경 가능) let은 재할당 가능

```jsx
<div id="app">
  <!-- 여기에 뭐가 렌더링 된다. -->
</div>

<script>
  var data = {
    message: 10,
    num: 20,
  }

  function render(sth) {
    // DOM API : HTML 태그 정보를 접근할 수 있음. 여기서는 id=app에 접근
    var div = document.querySelector('#app');
    // sth: div 태그 안에 출력될 내용
    div.innerHTML = sth;
  }

  **// app = data라는 객체에 proxy를 건 결과
  // Proxy(data, {data의 동작 정의}): new Proxy가 data라는 객체를 모방한 후, 동작을 추가함
  // [접근] get(): 객체(data)의 속성을 접근할 때마다 출력할 내용
  // [변경] set(): 객체의 속성을 변경할 때마다 출력할 내용**
  var app = new Proxy(data, {
    get() {
      // app.num
      console.log('값 접근')
    },
    //target:객체의, prop:속성값에, newValue:새로운 값 부여
    set(target, prop, newValue) {
      // app.num=20
      console.log('값 갱신')
      target[prop] = newValue;
      //변경된 값이 반영됨
      render(newValue);
    }
  })
</script>
```

## Vue.js 기본 개념과 문법

- Vue.createApp(): 애플리케이션 인스턴스 생성
    - mount(): 인스턴스를 유효하게 다룰 수 있는 화면의 영역 지정
        - = 어느 부분에 Vue 인스턴스를 적용할지 지정
- Vue 인스턴스의 속성 (=Vue 컴포넌트의 옵션 속성 = 옵션 API)
    - **인스턴스에서 사용할 수 있는 속성**
        
        ```jsx
        Vue.createApp({
          template: , //화면에 표시할 요소 (HTML, CSS 등)
          data: , //뷰의 반응성(Reactivity)이 반영된 데이터 속성
          methods: , //화면의 동작과 이벤트 로직을 제어하는 메서드
          created: , //뷰의 라이프사이클과 관련된 속성
          watch: , //data에서 정의한 속성이 변화했을 때 추가 동작을 수행할 수 있게 정의하는 속성
        });
        ```
        
        - methods code
            
            ```html
            <!-- HTML -->
            <div id="app">
              <p>{{ count }}</p>
              <!-- Vue Directive : v- 접두사가 있는 속성으로, button을 클릭할 때마다 addCount 실행 -->
              <button v-on:click="addCount">+</button>
            </div>
            
            <!-- JavaScript -->
            <script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
            <script>
              Vue.createApp({
                // data: 뷰의 반응성(Reactivity)이 반영된 데이터 속성
                // 속성명: function() = 속성명()
                data() {
                  return {
                    count: 0
                  }
                },
                //methods: 화면의 동작과 이벤트 로직을 제어하는 메서드
                methods: {
                  //addCount가 실행될 때마다 적용되는 이벤트
                  addCount() {
                    //createApp에 있는, reactivity가 적용된 count라는 값에 접근하겠다.
                    this.count++;
                  }
                }
              }).mount('#app');
            </script>
            ```
            
- [](https://y-chyachya.tistory.com/11)$ : Vue 인스턴스의 특별한 속성이나 메서드에 접근할 때 사용
    - 일반적으로 **`$`**는 전역 Vue 인스턴스에 대한 참조를 의미합니다. 전역 Vue 인스턴스는 **`new Vue()`**로 생성된 첫 번째 인스턴스를 가리킵니다. 다음은 몇 가지 **`$`**로 시작하는 Vue 인스턴스 속성 및 메서드의 예입니다:
        1. **`$el`**: 현재 Vue 인스턴스가 마운트된 엘리먼트를 나타냅니다.
        2. **`$data`**: 현재 Vue 인스턴스의 데이터 객체를 나타냅니다.
        3. **`$props`**: 현재 Vue 인스턴스의 프롭스를 나타냅니다.
        4. **`$emit()`**: 커스텀 이벤트를 발생시키는 메서드입니다.
        5. **`$on()`**: 이벤트 리스너를 등록하는 메서드입니다.
        6. **`$watch()`**: 데이터의 변경을 감시하는 메서드입니다.
- this : 현재 작업 중인 Vue 컴포넌트의 인스턴스

## Vue.js Component

### Vue Component 소개

- Vue Component
    - https://joshua1988.github.io/vue-camp/vue/components.html
    - 화면의 영역을 구분하여 개발할 수 있는 뷰의 기능
        - 유지보수 up : 컴포넌트 기반으로 화면을 개발하게 되면 **코드의 재사용성이 올라가고 빠르게 화면을 제작**할 수 있음
        - Root Component(createAPP)를 기준으로 뻗어나가는 구조임

### Vue Component 등록과 표시

```html
<!-- HTML -->
<div id="app">
  <!-- 컴포넌트 표시 : 태그로 작성-->
  <app-header></app-header>
</div>

<!-- JavaScript -->
<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
<script>
  Vue.createApp({
    // 인스턴스 옵션 속성 - 옵션 API
    components: {
      // '컴포넌트 이름': 컴포넌트 내용
      'app-header': {
        //화면에 표시할 요소 (HTML, CSS 등)
        template: '<h1>컴포넌트 등록</h1>'
      }
    }
  }).mount('#app');
</script>
```

### Vue Component 통신 방식

- 통신 방식 : 여러 컴포넌트 간에 어떤 식으로 메세지를 주고 받을지 정의하는 것
    - 상위 컴포넌트(=등록 컴포넌트)
    - 하위 컴포넌트(=자식 컴포넌트)

- **props: 데이터는 항상 위에서 아래로 흐르고, (상위→하위)**
    - https://joshua1988.github.io/vue-camp/vue/props.html
    - v-bind → `v-bind:`의 축약어 = `:`
        
        ```jsx
        <!-- HTML -->
        <div id="app">
          <!-- appTitle을 app-header 컴포넌트에 props로 넘겨주기 -->
          <!-- <app-header v-bind:프롭스이름="상위컴포넌트의 데이터이름(여기서는 root component)"></app-header> -->
          <app-header v-bind:title="appTitle"></app-header>
        </div>
        
        <!-- JavaScript -->
        <script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
        <script>
          //root component
          Vue.createApp({
            data() {
              return {
                appTitle: '프롭스 넘기기'
              }
            },
            components: {
              'app-header': {
                template: '<h1>{{ title }}</h1>',
                props: ['title']
              }
            },
          }).mount('#app');
        </script>
        ```
        
- **event emit: 아래에서 위로 이벤트가 올라간다. (하위→상위)**
    - https://joshua1988.github.io/vue-camp/vue/event-emit.html
    - 이벤트: UI 이벤트(일반적 키보드, 마우스 이벤트)가 아닌, 
    **컴포넌트 간에 메세지를 주고받기 위한 하나의 장치 
    (컴포넌트 통신을 위한 별도의 이벤트)**
    - v-on → `v-on:`의 축약어 = `@`
        
        ```jsx
        <!-- 
        갱신 button 클릭 -> sendEvent() 메소드 발생 -> 
        루트 컴퍼넌트에 refresh라는 custom event 전달 -> 
        refresh event는 루트 컴퍼넌트의 showAlert 메소드를 발생시킴 -> 
        새로고침 알람 발생
        -->
        
        <!-- HTML -->
        <div id="app">
          <!-- <app-contents v-on:이벤트이름="상위 컴포넌트의 메서드 이름"></app-contents> -->
          <app-contents v-on:refresh="showAlert"></app-contents>
        </div>
        
        <!-- JavaScript -->
        <script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
        <script>
          var appContents = {
            template: `
              <p>
                <button v-on:click="sendEvent">갱신</button>
              </p>
            `,
            methods: {
              sendEvent() {
                // emit: 커스텀 이벤트를 발생시키는 메서드
                // refresh event를 하위 컴포넌트에서 위쪽 컴퍼넌트로 쏘아올림
                this.$emit('refresh');
              }
            }
          }
        
          // 루트 컴포넌트
          Vue.createApp({
            methods: {
              showAlert() {
                alert('새로고침');
              }
            },
            components: {
              // '컴포넌트 이름': 컴포넌트 내용
              'app-contents': appContents
            }  
          }).mount('#app');
        </script>
        ```
        
- 둘 다 사용 : 같은 레벨의 컴포넌트 간 데이터 전달 방법
    - ex) AppContents 로그인 버튼을 클릭했을 때, 
    AppHeader의 안내 메세지를 변경하고 싶을 때
        

        
        ```jsx
        <!-- 로그인 button 클릭 -> sendEvent 발생 -> login이라는 custom event 발생 ->
        login은 상위 컴퍼넌트의 receive 메서드를 발생시킴 -> message가 로그인 됨으로 변경 ->
        app-header 하위 컴퍼넌트의 appTitle이라는 props는 상위 컴퍼넌트의 message를 참조함 ->
        로그인 됨으로 문구가 변경됨을 확인할 수 있음 -->
        
        <!-- HTML -->
        <div id="app">
          <app-header v-bind:app-title="message"></app-header>
          <app-contents v-on:login="receive"></app-contents>
        </div>
        
        <!-- JavaScript -->
        <script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
        <script>
          var appHeader = {
            props: ['appTitle'],
            template: '<h1>{{ appTitle }}</h1>',
          }
        
          var appContents = {
            template: `
              <p>
                <button v-on:click="sendEvent">로그인</button>
              </p>
            `,
            methods: {
              sendEvent() {
                this.$emit('login');
              }
            }
          }
        
          // 루트 컴포넌트
          Vue.createApp({
            data() {
              return {
                message: ''
              }
            },
            methods: {
              receive() {
                console.log('받았다');
                this.message = '로그인 됨'
              }
            },
            components: {
              // '컴포넌트 이름': 컴포넌트 내용
              'app-header': appHeader,
              'app-contents': appContents
            }  
          }).mount('#app');
        </script>
        ```
        

## **Vue.js 템플릿 문법 : Directive**

- https://joshua1988.github.io/vue-camp/vue/template.html
- 데이터 바인딩 : 뷰 인스턴스에서 정의한 속성들을 화면에 표시하는 방법
    - 클래스 바인딩 및 아이디 바인딩
        - `v-bind:` = `:`

        
        ```jsx
        <style>
        .primary {
          color: coral;
        }
        </style>
        
        <!-- HTML -->
        <div id="app">
          <!-- class 바인딩 -->
          <h1>클래스 바인딩</h1>
          <!-- <div class='primary'>데이터 바인딩 예제</div> -->
          <div v-bind:class="textClass">데이터 바인딩 예제</div>
          <div :class="textClass">데이터 바인딩 예제</div>
        
          <!-- id 바인딩 -->
          <h1>아이디 바인딩</h1>
          <section :id="sectionId" :style="sectionStyle">
            반갑습니다.
          </section>
        </div>
        
        <!-- JavaScript -->
        <script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
        <script>
          Vue.createApp({
            data() {
              return {
                textClass: 'primary',
                sectionId: 'tab1',
                sectionStyle: { color: 'red' }
              }
            }
          }).mount('#app');
        </script>
        ```
        
- 디렉티브 : `v-` 형태로, 뷰로 화면의 요소를 더 쉽게 조작하기 위한 문법
    - Vue Directive 종류
        
        
        | 디렉티브 | 설명 |
        | --- | --- |
        | v-text | innerText와 동일한 기능을 수행하며태그를 인코딩하여 문자그대로 보여준다. |
        | v-html | v-html innerHTML과 동일한 기능을 수행하며 태그를 파싱하여 화면에 구현한다. |
        | v-bind | 뷰 인스턴스의 데이터 속성을 해당 HTML 요소에 연결할 때 사용|
        | v-on | 해당 HTML 요소의 이벤트를 뷰 인스턴스의 로직과 연결할 때 사용 |
        | v-model | 양방향 데이터를 공유할 때 사용한다.
        (v-bind와 v-on의 조합) |
        | v-show | css의 display 와 동일한 기능을 수행한다. |
        | v-if | if 조건문 구현 |
        | v-else | else 조건문 구현 |
        | v-else-if | else if 조건문 구현 |
        | v-for | for 반복문 구현 |
        | v-pre | vue.js가 컴파일되지 않게하며{{머쉬태시}}도 그대로 보여진다 |
        | v-once | vue.js가 처음 한번만 렌더링을 수행하며데이터가 변경되어도 처음값만 보여준다. |
        | v-clock | 컴파일 되지않는 값이 순간 나오는 것을 막고 결과값만 보여준다. |
    - `v-for` : 배열 순회
        
        ```html
        <!-- HTML -->
        <div id="app">
          <ul>
            <!-- <li>{{ items[0] }} </li> ... 으로 하면 코드 더러워짐-->
            <!-- v-for: 배열을 순회할 수 있는 속성 -->
            <li v-for="item in items">
              {{ item }}
            </li>
          </ul>
        </div>
        
        <!-- JavaScript -->
        <script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
        <script>
          Vue.createApp({
            data() {
              return {
                items: ['삼성', '네이버', '인프런']
              }
            },
          }).mount('#app');
        </script>
        ```
        
    - `v-if` `v-else`, `v-show`
        
        ```jsx
        <!-- HTML -->
        <div id="app">
          <!-- 1. v-if -->
          <p v-if="login">로그인 되었습니다</p>
          <p v-else>로그인 하세요</p>
          <!-- 로그인을 클릭하면 loginUser 메서드 발생 -->
          <button v-on:click="loginUser">로그인</button>
        
          <hr>
        
          <!-- 2. v-show -->
          <p v-show="login">로그인 되었습니다</p>
          <button v-on:click="loginUser">로그인</button>
        </div>
        
        <!-- JavaScript -->
        <script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
        <script>
          Vue.createApp({
            data() {
              return {
                login: false
              }
            },
            methods: {
              loginUser() {
                // 로그인 값을 변경
                this.login = !this.login;
              }
            }
          }).mount('#app');
        </script>
        ```
        

## **Vue CLI: 뷰 프로젝트 생성 도구**

- CLI : command line interface
- 프로젝트 실행
    - vue cli 설치 : (sudo)`npm install -g @vue/cli`
        - -g : global (다른 터미널, 프로젝트에서도 vue/cli 설치한다는 뜻)
    - 프로젝트 생성: `vue create <progect-name>`
    - `cd <project-name>`
    - 실행: `(sudo) yarn serve`
        - 오류 발생 시 : `npm update`
- 프로젝트 폴더 내용
    - main.js의 `createApp(App).mount('#app')` 을 통해 <div id=”app”> 안의 내용이 채워짐

        

## **Vue 싱글 파일 컴포넌트 (.vue)**

- https://joshua1988.github.io/vue-camp/vue/sfc.html
- 싱글 파일 컴포넌트 : 화면의 특정 영역에 대한 HTML, CSS, JS 코드를 한 파일에서 관리하는 방법입니다.
    - 뷰 CLI로 프로젝트를 생성하고 나면 App.vue라는 파일을 확인할 수 있습니다. 이처럼 **vue 확장자를 가진 파일**을 모두 싱글 파일 컴포넌트라고 합니다.
    - 브라우저에서 .vue파일을 인식할 수 없기 때문에 뷰 로더라는 별도의 변환과정을 거침
    - [VSC Extensions] ****Vue VSCode Snippets****
        - `vbc` → 기본 템플릿 작성
        - `vda` → data key: value 템플릿 작성
    - 구조
        
        ```jsx
        // import 라이브러리 from '라이브러리이름'
        // import 파일이름 from './파일경로'
        
        <!-- .vue 파일 구조 -->
        <!-- HTML -->
        <template>
          <!-- html (뷰 컴포넌트의 표현단, 템플릿 문법) -->
          <!-- 컴퍼넌트 명명규칙은 Pascal Case 권장 -->
          <HelloWorld msg="Welcome to Your Vue.js APP"></HelloWorld>
        </template>
        
        <!-- JavaScript -->
        <script>
          // 자바스크립트 (뷰 컴포넌트 내용)
        </script>
        
        <!-- CSS -->
        <style>
          /* CSS (뷰 템플릿의 스타일링) */
        </style>
        ```
        
    - code
        
        ```jsx
        <!-- App.vue -->
        <template>
          <!-- 컴퍼넌트 명명규칙은 Pascal Case 권장 -->
          <AppHeader 
            :appTitle="message"
            @change="changeMessage">
          </AppHeader>
        </template>
        
        <script>
        // import 컴포넌트이름 from './컴포넌트 경로'
        import AppHeader from './components/AppHeader.vue'
        
        export default {
          components: {
            // '컴포넌트이름': 컴포넌트 내용
            // 'app-header': AppHeader,
            // 'AppHeader': AppHeader,
            // AppHeader: AppHeader,
            // key=value이면 value값만 써도 됨
            AppHeader
          },
          data() {
            return {
              message: '앱 헤더 컴포넌트'
            }
          },
          methods: {
            changeMessage() {
              this.message = '변경됨'
            }
          }
        }
        </script>
        
        <style scoped>
        
        </style>
        ```
        
        ```jsx
        <!-- AppHeader.vue -->
        <template>
          <h1>{{ appTitle }}</h1>
          <button @click="changeTitle">변경</button>
        </template>
        
        <script>
          export default {
            props: ['appTitle'],
            methods: {
              changeTitle() {
                this.$emit('change');
              }
            }
          }
        </script>
        
        <style scoped>
        
        </style>
        ```
        

## **Vue.js 최종 프로젝트**
- post 요청
    - `yarn add axios`
    - https://jsonplaceholder.typicode.com/
    - https://joshua1988.github.io/web-development/http-part1/
- Form Prevent
    
    ```jsx
    <!-- from의 기본적인 action은 새로고침됨 -> 기본적 동작 막기위해 .prevent 이용 -->
    <template>
    	<form action="" @submit="submitForm">
    		<button type="submit">로그인</button>
    	</form>
    </template>
    .
    .
    .
    <script>
    	export default {
    		methods: {
    				**// submitForm의 첫번째 파라미터로 이벤트를 들고올 수 있음**
    	      submitForm(event) {
    	        **event.preventDefault();**
    	        console.log('제출됨')
    	}
    </script>
    ```
    
    ```jsx
    <!-- from의 기본적인 action은 새로고침됨 -> 기본적 동작 막기위해 .prevent 이용 -->
    <template>
    	<form action="" @submit.prevent="submitForm">
    		<button type="submit">로그인</button>
    	</form>
    </template>
    .
    .
    .
    <script>
    	export default {
    		methods: {
    	      submitForm() {
    	        console.log('제출됨')
    	}
    </script>
    ```
    
- Composition API
    
    ```jsx
    <template>
      <!-- submit 이벤트가 발생하면 submitForm 매서드가 발생함 -->
      <!-- form의 기본적인 action은 새로고침 -> 기본적 동작 막기위해 .prevent 이용 -->
      <form action="" @submit.prevent="submitForm">
        <div>
          <label for="userId">ID:</label>
          <input id="userId" type="text" v-model="username">
        </div>
        <div>
          <label for="password">PW:</label>
          <input type="text" v-model="password">
        </div>
        <button type="submit">로그인</button>
      </form>
    </template>
    
    <script>
    import axios from 'axios'
    import { ref } from 'vue';
    
      export default {
        // <<composition API>>
        setup() {
          // data
          var username = ref('');
          var password = ref('');
    
          // methods
          var submitForm = () => {
            axios.post('https://jsonplaceholder.typicode.com/users/', {
              //username, password: 서버로 보낼 속성 이름
              username: username.value,
              password: password.value
            }).then(response => {
              console.log(response);
            })
          }
          
          return { username, password, submitForm }
        },
        // 위에서 return했던 것은 method, template 표현식에서도 동일하게 쓸 수 있음
        // methods: {
        //   logText() {
        //     this.password
        //   }
        // }
    
        // <<option API>>
        // data() {
        //   return {
        //     username: '',
        //     password: '',
        //   }
        // },
        // methods: {
        //   submitForm() {
        //     // event.preventDefault();
        //     const data = {
        //       username: this.username,
        //       password: this.password,
        //     }
        //     axios.post('https://jsonplaceholder.typicode.com/users/', data)
        //       .then(response => {
        //         console.log(response)
        //       });
        //     // console.log('제출됨')
        //   }
        // }
      }
    </script>
    
    <style scoped>
    
    </style>
    ```