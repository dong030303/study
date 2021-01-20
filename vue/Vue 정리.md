[TOC]

# Vue

## 디렉티브 모음

- **디렉티브**: Vue에서 제공하는 특수 속성으로 *v-* 형태의 접두어가 붙어있으며 각각 특수한 반응형 동작을 한다.

| 디렉티브 이름 | 역할                                                         | 비고      |
| ------------- | ------------------------------------------------------------ | --------- |
| v-if          | 속성 값의 참, 거짓 여부에 따라 동작을 수행한다.              |           |
| v-for         | 지정한 데이터의 개수만큼 동작을 반복한다.                    |           |
| v-show        | 속성 값의 참, 거짓 여부에 따라 css의 display 값을 조절한다. (if 문의 경우 삭제와 생성을 반복함) |           |
| v-bind        | 뷰 인스턴스에서 생성한 데이터 속성과 기존 html의 속성을 연결한다. | :속성이름 |
| v-on          | 화면 요소의 이벤트를 감지하여 처리할 때 사용한다.            | @속성이름 |
| v-model       | 입력과 요소의 상태를 양방향으로 바인딩한다.                  |           |



## 선언적 렌더링

- vue는 간단한 템플릿 구문을 사용하여 DOM에서 데이터를 선언적으로 렌더링 할 수 있다.

  ```html
  <div id="app">
    {{ message }}
  </div>
  ```

  ```json
  var app = new Vue({
    el: '#app',
    data: {
      message: '안녕하세요 Vue!'
    }
  })
  ```

- 이는 html과 직접 상호작용할 필요 없이 데이터가 반응형이 된 것을 의미한다.

- 아래에서는 v-bind라는 디렉티브가 title 속성에 사용되어 span 태그에 동적인 title 속성을 부여할 수 있다.

  ```html
  <div id="app-2">
    <span v-bind:title="message">
      내 위에 잠시 마우스를 올리면 동적으로 바인딩 된 title을 볼 수 있습니다!
    </span>
  </div>
  ```

  ```js
  var app2 = new Vue({
    el: '#app-2',
    data: {
      message: '이 페이지는 ' + new Date() + ' 에 로드 되었습니다'
    }
  })
  ```

## 컴포넌트를 사용한 작성방법

- **컴포넌트 시스템**: 작고 독립적인 재사용성이 뛰어난 추상적인 개념

- 컴포넌트 등록하기: component 메소드의 이름과 태그를 인자로 받는다.

  ```js
  // todo-item 이름을 가진 컴포넌트를 정의합니다
  Vue.component('todo-item', {
    template: '<li>할일 항목 하나입니다.</li>'
  })
  
  var app = new Vue(...)
  ```

  위의 경우 <todo-item>의 형태로 컴포넌트를 사용 가능하다. 아래와 같이 사용할 수 있다.

  ```html
  <ol>
    <!-- todo-item 컴포넌트의 인스턴스 만들기 -->
    <todo-item></todo-item>
  </ol>
  ```

- props를 지정하면 부모 컴포넌트의 데이터가 자식 컴포넌트에 전달된다. 아래의 경우 todo의 값이 전달되어 리스트의 형태로 출력한다.

  ```js
  Vue.component('todo-item', {
    // 이제 todo-item 컴포넌트는 "prop" 이라고 하는
    // 사용자 정의 속성 같은 것을 입력받을 수 있습니다.
    // 이 prop은 todo라는 이름으로 정의했습니다.
    props: ['todo'],
    template: '<li>{{ todo.text }}</li>'
  })
  ```

- ```html
  <div id="app-7">
    <ol>
      <!--
        이제 각 todo-item 에 todo 객체를 제공합니다.
        화면에 나오므로, 각 항목의 컨텐츠는 동적으로 바뀔 수 있습니다.
        또한 각 구성 요소에 "키"를 제공해야합니다 (나중에 설명 됨).
       -->
      <todo-item
        v-for="item in groceryList"
        v-bind:todo="item"
        v-bind:key="item.id"
      ></todo-item>
    </ol>
  </div>
  ```

  ```js
  Vue.component('todo-item', {
    props: ['todo'],
    template: '<li>{{ todo.text }}</li>'
  })
  
  var app7 = new Vue({
    el: '#app-7',
    data: {
      groceryList: [
        { id: 0, text: 'Vegetables' },
        { id: 1, text: 'Cheese' },
        { id: 2, text: 'Whatever else humans are supposed to eat' }
      ]
    }
  })
  ```

  todo-item 컴포넌트를 활용하여 위와 같이 리스트에 각각 다른 아이디와 텍스트를 바인딩할 수 있다.

- 컴포넌트를 활용하면 앱을 관리하기 쉬워진다. 이는 코드에 직관성이 개선되기 때문이다.

  ```html
  <div id="app">
    <app-nav></app-nav>
    <app-view>
      <app-sidebar></app-sidebar>
      <app-content></app-content>
    </app-view>
  </div>
  ```





## Vew 인스턴스

- 뷰 인스턴스는 다음과 같은 형태로 생성할 수 있다.

  ```js
  var vm = new Vue({
    // 옵션
  })
  ```

  이 때 Vew 인스턴스를 참조하기 위한 객체의 이름은 관례적으로 vm으로 이름 지어진다.

  

## 데이터와 메소드

- Vew 인스턴스에서 data 객체는 반응성을 지니게 된다.

  ```js
  var data = { a: 1 }
  var vm = new Vue({
  	data: data
  })
  // vm.a === data.a
  ```

  즉, 인스턴스에서의 값 변경이 곧 data 객체의 값 변경으로 이어진다.

- 단, 인스턴스가 생성될 때 정의되어 있지 않은 데이터는 반응성을 지니지 않는다.

  ```html
  <!DOCTYPE html>
  <html lang="ko">
  <head>
      <meta charset="UTF-8">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <title>VUE</title>
      <script src="./vue.js"></script>
  </head>
  <body>
      <div id="app">
          <div>{{ msgA }}</div>
          <div>{{ msgB }}</div>
          <button v-on:click="changeMessage">Change!</button>
      </div>
  
      <script>
          const vm = new Vue({
              el: '#app',
              data: {
                  msgA: 'Message A.'
              },
              methods: {
                  changeMessage: function (){ //: function 생략 가능
                      this.msgA = 'Changed message A!'
                      this.msgB = 'Changed message B!'
                  }
              }
          })
  
          console.log(vm)
      </script>
  </body>
  </html>
  ```

  위 코드에서 msgA는 인스턴스 생성 시 정의가 되어있기 때문에 콘솔 창에 새 데이터를 입력할 시 변경이 가능하다. 그러나 msgB의 경우는 changeMessage 사용 후에 생성되는, 인스턴스 생성 시 정의되어 있지 않은 데이터이기 때문에 콘솔창에 데이터를 입력하여도 값이 변경되지 않는다.

  ```html
  data: {
                  msgA: 'Message A.',
                  msgB: ''
              }
  ```

  위와 같이 data 객체를 수정 시 msgB 또한 반응성을 지니게 된다.

- 이는 데이터 객체에 Object.freeze()를 적용하여 반응성이 부여되지 않도록 할 수 있다.

  ```js
  var obj = {
    foo: 'bar'
  }
  
  Object.freeze(obj)
  
  new Vue({
    el: '#app',
    data: obj
  })
  ```

  

## vue lifecycle hook

- vue 인스턴스 생성 후 적용되는 라이프 사이클의 동작 과정 및 순서![The Vue Instance Lifecycle](https://kr.vuejs.org/images/lifecycle.png)

- beforeCreate와 Create: 인스턴스 생성 전과 후
- beforeMount와 mounted: DOM에 부여되기 전과 후
- beforeUpdate와 updated: 수정 전과 후
- beforeDestroy와 destroyed: 인스턴스 파괴 전과 후

## 템플릿

- Vue는 기본적으로 Vue 인스턴스의 데이터 값을 랜더링 된 DOM에 선언적으로 바인딩 할 수 있는 HTML 기반의 템플릿 문법을 사용한다.

- 이 템플릿은 HTML 파서로 구문 분석이 가능한 유효한 HTML이다.

- 데이터 바인딩의 가장 기본적인 문법은 아래와 같은 이중 중괄호(Mustaches 구문)을 사용한다.

  ```html
  <span>{{msg}}</span>
  ```

  위의 {{msg}}는 데이터 객체의 속성 'msg'의 속성 값으로 대체된다. 이는 속성 값의 변경마다 갱신된다.

- Mustaches 구문은 HTML 태그의 속성에서는 사용할 수 없다. 대신 v-bind 디렉티브를 통해서 속성에 데이터를 바인딩 할 수 있다.

  ```html
  <div :id="msg"></div>
  ```

- HTML 태그의 속성 값이 참과 거짓으로 나뉘는 속성이라면 (ex: disabled) 데이터를 바인딩 하였을 때, 데이터 값이 null, undefined, false의 값일 경우 속성 값이 거짓이 된다.

- 데이터를 바인딩 할 때 역시 JS 표현식 기능을 지원한다.

  ```html
  <div :id="'list-' + id"></div>
  ```

  단, 각 바인딩 당 하나의 단일 표현식만 포함될 수 있다. (ex: if 문이나 선언문 사용 불가)

## computed

## 리스트 랜더링

## 이벤트 핸들링

### 이벤트 수식어

| 수식어 | 기능 |
| ------ | ---- |
|        |      |
|        |      |
|        |      |
|        |      |
|        |      |
|        |      |

### 키 수식어

## 폼 입력 바인딩

## 컴포넌트

- 컴포넌트 등록
- 