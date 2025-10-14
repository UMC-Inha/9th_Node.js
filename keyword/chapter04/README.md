- ES
    
    ECMAScript는 자바스크립트의 기반이 되는 스크립팅 언어 명세이다.
    
    https://www.ecma-international.org/ 에서 표준화를 담당하고있다.
    
    ECMAScript는 다음을 정의한다.
    
    - 언어 구문 (구문 분석 규칙, 키워드, 흐름 제어, 객체 리터럴 초기화 등)
    - 오류 처리 원리 ([`throw`](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Statements/throw), [`try...catch`](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Statements/try...catch), 사용자 정의 [`Error`](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Error) 타입 등)
    - 자료형 (불리언, 숫자, 문자열, 함수, 객체, ...)
    - 프로토타입 기반 상속 원리
    - 내장 객체 및 함수 ([`JSON`](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/JSON), [`Math`](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Math), `Array.prototype` 메서드, [`Object`](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object) 내성검사 메서드 등)
    - [엄격 모드](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Strict_mode)
    - [모듈 시스템](https://developer.mozilla.org/ko/docs/Web/JavaScript/Guide/Modules)
    - 기본 메모리 모델
    
    자바스크립트 모듈화 방식은 다양한 패러다임을 거쳐 발전됨
    
    그 중 CommonJS, ESModule이 대표적이며, 현재 가장 많이 사용되는 모듈 시스템이다
    
    - CommonJS는 주로 서버 환경에서 널리 사용
    - ESModule은 자바스크립트 표준 모듈 시스템으로, 브라우저 환경에서의 모듈화를 지원
    
    # 자바스크립트 모듈화의 역사
    
    모듈: 프로그램을 작은 기능 단위로 나누어 구성하는 중요한 요소
    
    모듈 시스템: 프로그램을 여러 모듈로 나누고, 이를 체계적으로 관리하는 데 필요한 규칙, 방법 제공
    
    ## 모듈화 배경
    
    - 1997 ECMAScript: 브라우저마다 다른 스크립트 언어 등장 > 호환성 문제 대두 > ECMA에서 자바스크립트 표준인 ECMAScript 제정
    - 1999 Ajax 등장
        - 이전: 서버에서 전달된 HTML에 동적 요소를 추가하는 보조적 역할에 머묾. 페이지 전체 렌더링 필요
        - 이후: 브라우저/서버가 비동기적 데이터 교환 가능 > 서버로부터 필요한 데이터만 요청 > 일부만 새로고침
    - 2008 구글 V8 JS 엔진
        - 클라이언트 측에서 수행하는 작업량 증가 > JS V8엔진 > JS 코드 속도 개선
        - 브라우저 외부에서도 사용하도록 개발 > Node.js 개발 > 모듈 시스템 발전 및 표준화에 기여
    
    ## 모듈화 이전
    
    - 하나의 파일에 모든 기능 구현 / 필요한 파일 순서대로 불러오기
    - 문제점
        - 전역 변수 문제
        - 복잡한 의존성 관리 (로든 순서 관리)
        - 렌더링 지연 문제 (스크립트 다운로드하는 동안 렌더링 블로킹)
    
    ## 모듈화 시도들
    
    - 즉시 호출 함수 표현식
        - 이 스코프 내에서 정의된 변수와 함수는 외부 스코프에 접근 X > 네임 스페이스 충돌 방지
        
        ```jsx
        // 정의하자마자 호출
        ;(function() {
          // 모듈 코드
        })()
        ```
        
        - 단점
            - 네임스페이스 충돌 - 동일한 이름을 가진 모듈을 중복 선언하면 에러 발생
            
            ```jsx
            const ModuleA = (function() {})()
            const ModuleB = (function() {})()
            
            ModuleA.method()
            ModuleB.method()
            
            const ModuleA = (function() {})()
            // Uncaught SyntaxError: Identifier 'ModuleA' has already been declared
            ```
            
            - 모듈 간의 의존성이 있다면 모듈 로드 순서를 수동으로 관리해야함
        
        https://blog.naver.com/rbdus0715/223163460618?trackingCode=blog_bloghome_searchlist
        
    - AMD와 RequireJS
        - AMD: 모듈을 비동기적으로 로드/정의하는 JS 모듈 정의 표준 중 하나
        - RequireJS는 AMD를 구현한 대표 예시
        - 여러 모듈을 병렬로 로드 가능, 모듈 간 의존성 명시적으로 정의하게 함
        - 주로 브라우저 환경에서 사용, 대규모 애플리케이션에서 의존성 명시/비동기 로딩을 통해 효율성 up
        
        ```jsx
        // moduleA.js
        define(
          ['dependency1', 'dependency2'], function (dependency1, dependency2) {
            // 모듈 코드
            rerturn {
        	    units: ['A', 'B'];
            }
          }
        )
        // main.js
        require(['moduleA.js'], function(moduleA) {
          console.log(moduleA.units);
        }
        ```
        
        - 단점
            - 복잡 문법(가독성 down)
            - 정적 분석의 어려움 - 의존성을 동적 함수 호출로 정의하기 때문
            - 서버 환경에 대한 제약 - 브라우저 환경 중심으로 설계됨
        
        <aside>
        
        ### 비동기적 로드 vs 동기적 로드
        
        비동기적: 파일 실행과 모듈 로드가 동시에 진행
        
        동기적: 스크립트를 불러오는 동안 다른 작업이 중단됨
        
        </aside>
        
    - CommonJS
        - 브라우저 이외의 환경에서 JS를 사용하기 위한 표준 모듈 시스템
        - 동기적 모듈 로드 > 서버 사이드 환경에서 파일 시스템 접근 or 네트워크 요청과 같은 I/O 작업에 적합
        
        ```jsx
        // sum.js
        exports.sum = function(a, b) {
        	return a + b;
        }
        // index.js
        const {sum} = require('./sum.js')
        console.log(sum(1, 2));
        ```
        
    - UMD (Universal Module Definition)
        - CommonJS와 AMD를 통합해 다양한 환경에서 호환성 제공하기 위함
        
        ```jsx
        ;(function (root, factory) {
        	if (typeof define === 'function' && define.amd) { /* AMD 환경 */ }
          else if (typeof module === 'object' && module.exports) { /* commonJS 환경 */ }
          else { /* 전역 객체에 할당 */ }
        }) (typeof self !== 'undefined' ? self : this, function (dependency) {
          // 모듈 내부 코드
          return {
           // 공개되는 부분
          }
        }
        ```
        
        - 단점
            - 불필요한 코드 증가
            - 성능 저하
            - 최신 모듈 시스템과 비교 (ESModule를 통해 호환 문제 해결됨)
            - 복잡한 유지보 수
    - SystemJS
        - 필요한 시점에 모듈을 동적으로 로드 > 비동기적 로드
        - System.register 함수에 의존성 배열과 모듈 코드를 정의하는 함수를 넘겨줌으로써 작성
        - 단점
            - 표준화되지 않은 형식
            - 성능 이슈
            - 복잡성 증가
    - ESModule
        - ES6에서는 웹 브라우저와 Node.js에서 표준 모듈 시스템을 도입
        
        ```jsx
        // sum.js
        export function sum(a, b) {
        	return a + b;
        }
        // index.js
        import {sum} from './sum.js'
        ```
        
    
    <aside>
    
    ### 오늘날 JS 모듈 시스템
    
    CommonJS와 ESModule 가장 많이 사용
    
    특히 서버사이드는 CommonJS, 클라이언트 사이드는 ESModule을 사용하는 것이 일반적
    
    ### 정리 (역사)
    
    1. 호출 함수 표현식
    2. 브라우저 환경에서는 비동기 로드 지원하는 AMD & RequireJS 도입
    3. 서버 환경에서는 동기 로드 지원하는 CommonJS 도입
    4. CommonJS와 AMD를 모두 지원하기 위한 UMD와 SystemJS
    5. ESModule이 오늘날 브라우저와 서버 환경에서 모두 표준으로 채택
    </aside>
    
- ES6
    - ES6의 주요 변화 및 특징
        
        ES6은 6번째 개정된 ECMAScript 2015이다.
        
        주요 기능은 다음과 같다.
        
        1. let & const: 기존 var는 변수의 범위를 제어하기 어려우며, 함수 스코프였다. let은 블록 스코프를 지원한다.
        2. 화살표 함수
        
        ```jsx
        const add = (a, b) => a + b;
        ```
        
        1. 템플릿 리터럴
        - 문자열을 더 쉽게 다루기 위해서 템플릿 리터럴 방식이 도입되었다.
        
        ```jsx
        // 전통 방식
        const greeting = 'hello' + name + '!';
        
        // 템플릿 리터럴
        const greeting = `hello, ${name}!`'
        ```
        
        1. 구조 분해 할당
        - 배열이나 객체에서 값을 편하게 추출할 수 있도록 하는 기능
        
        ```jsx
        const person = { name: "Kim", age: 25 };
        const { name, age } = person;
        ```
        
        1. 기본 매개변수
        - es6 이전에는 조건문을 통해 기본값을 설정했지만, ES6에서는 default 매개변수를 지정할 수 있다.
        1. 스프레드 연산자 & 나머지 매개변수
        
        ```jsx
        const arr = [1, 2, 3];
        const arr2 = [...arr, 4, 5];
        
        function sum(a, b, ...ect) { }
        ```
        
        1. 클래스
        2. 모듈
        - ES6 이전에는 자바스크립트에서 모듈 구현을 위해 다양한 패턴들이 사용되었는데, ES6에서는 모듈 시스템이 공식적으로 도입됨
        - ESModule !
        1. Promise
        - 기존에는 비동기 코드를 처리하기 위해 콜백 지옥에 빠짐
        - 비동기 코드 처리를 보다 명확하고 간단하게 만듦
        1. 심볼
        - 고유하고 변경 불가능한 값으로, 객체의 프로퍼티 키로 사용됨
        
    - ES6를 중요시 하는 이유
        
        현대 자바스크립트 개발의 출발점이 ES6이기 때문
        
        현재 React, Vue, Angular, Next.js, Node.js 등이 모두 ES6문법을 기본 전제로 한다.
        
        과거에는 브라우저마다 JS 지원이 달라 호환성 문제가 생겼지만, 지금은 거의 모든 브라우저와 Node.js가 ES6 표준을 완벽히 지원한다.
        
- ES Module
    
    # CommonJS란?
    
    배경
    
    - JS를 서버에서 확장성 있게 활용하기 위한 몇 가지 요구사항 제안
        - 상호 호환되는 표준 라이브러리 필요
        - 서버와 웹 간에 상호작용할 수 있는 표준 인터페이스
        - 다른 모듈을 로드할 수 있는 표준 필요
        - 로드 패키지하고 배포 및 설치하는 방법 필요
        - 패키지 설치, 패키지 의존성 해결하는 패키지 저장소 필요
    - 케빈 > ServerJS 그룹 창설
    - JS 콘퍼런스에서 CommonJS 발표
    
    CommonJS의 명세
    
    - 독립적인 실행 영역 - 변수명 충돌 X
    - exports 객체로 모듈 정의 - 모듈 간 정보 교환이 필요할 때 공개할 기능만 exports 객체로 정의
    - require() 함수로 모듈 사용 - require() 인수로 모듈명 전달하면 모듈의 exports 객체 반환됨
    
    <aside>
    
    ### 브라우저 환경에서 CommonJS 모듈 사용 시 문제 & 해결
    
    - CommonJS는 본래 서버 사이드 목적 - 모든 모듈이 디스크 내에 있어 바로 불러올 수 있는 환경 전제
    - 해결
        - 서버 모듈을 비동기적으로 클라이언트에 전송할 수 있는 모듈 전송 포맷을 추가로 정의
        - 다만, CommonJS를 네이티브로 브라우저에서 사용하는 방법은 존재 X, 반드시 번들러 도움 필요
    </aside>
    
    - Node.js에서 CommonJS
        
        도입 이유
        
        - 다양한 기능의 필요성 - 모듈 관리 필요
        - 논블로킹 I/O 모델과의 호환 - CommonJS의 동기적 로딩 방식 → 블로킹 처리 편리
        - CommonJS의 보급도 good
        
         CommonJS 모듈로 인식하는 기준
        
        - require()가 사용된 .js 파일
        - 가장 가까운 package.json의 type 필드가 “commonjs”인 하위 .js 파일들
        - .cjs 확장자 파일들 (반대로 .mjs는 ESModule로 해석됨)
        
        모듈 내보내기
        
        - Node에서는 exports 객체 외에 module.exports 를 추가로 둠
        - exports와 module.exports는 기본적으로 동일한 객체를 참조
        - export에 새로운 객체를 직접 할당하면 module.exports와 참조가 끊어지며 exports는 독립적인 객체가 됨
            - 이 변경은 module.exports에는 영향 X, 외부에서 reuiqre()로 불러올 때 export에 할당된 값은 사용 불가
        - 즉, exports에 새 객체를 재할당하면 module.exports와 독립적인 객체가 됨
        - 굳이 왜 module.exports를 따로 뒀을까?
            - export = 값 형태로 사용할 경우 module.exports와의 연결이 끊어져 주의 필요
        
        모듈 래퍼 & 모듈 스코프
        
        - 모듈래퍼함수 : 모듈마다 독립적인 실행 영역을 제공
            - 모듈의 코드가 실행되기 전에 모듈을 감싸는 함수
            - 모듈 코드는 모듈 래퍼의 스코프 내부에서만 유효, 이 스코프를 모듈 스코프라 함
        
        ```jsx
        ;(function (exports, require, module, __filename, __dirname) {
        	// 모듈 스코프
        })
        ```
        
        - 이렇게 감싸 외부에 공개하는 것의 이점
            - export로 할당되지 않은 내부 것들이 외부로부터 숨겨짐
            - CommonJS로 작성된 파일에서 개발자가 따로 선언하지 않았는데도 사용할 수 있는 변수가 있음. 사실 이것들은 모듈 래퍼가 제공하는 모듈 스코프에서 생성되는 변수임
                - module : 현재 파일(모듈)에 대한 정보를 담고 있는 **객체**
                - export : `module.exports`와 **같은 객체**를 참조하는 **객체**
                - require : 다른 모듈, 내장 모듈, 설치된 패키지를 현재 모듈로 **불러오는(Import)** 역할하는 함수
                - __filename : 현재 실행 중인 파일의 **절대 경로**와 **파일명**을 포함하는 **문자열**이 출력
                - __dirname : 현재 실행 중인 파일이 위치한 **디렉토리(폴더)의 절대 경로**를 포함하는 **문자열**이 출력
        
        ```jsx
        // console.log(__dirname)
        /Users/gyuyeonjo/me/npm
        
        //console.log(__filename)
        /Users/gyuyeonjo/me/npm/test.js
        
        // export
        {}
        
        // require
         [Function: require] {
          resolve: [Function: resolve] { paths: [Function: paths] },
          main: {
            id: '.',
            path: '/Users/gyuyeonjo/me/npm',
            exports: {},
            filename: '/Users/gyuyeonjo/me/npm/test.js',
            loaded: false,
            children: [],
            paths: [
              '/Users/gyuyeonjo/me/npm/node_modules',
              '/Users/gyuyeonjo/me/node_modules',
              '/Users/gyuyeonjo/node_modules',
              '/Users/node_modules',
              '/node_modules'
            ],
            [Symbol(kIsMainSymbol)]: true,
            [Symbol(kIsCachedByESMLoader)]: false,
            [Symbol(kIsExecuting)]: true
          },
          extensions: [Object: null prototype] {
            '.js': [Function (anonymous)],
            '.json': [Function (anonymous)],
            '.node': [Function (anonymous)]
          },
          cache: [Object: null prototype] {
            '/Users/gyuyeonjo/me/npm/test.js': {
              id: '.',
              path: '/Users/gyuyeonjo/me/npm',
              exports: {},
              filename: '/Users/gyuyeonjo/me/npm/test.js',
              loaded: false,
              children: [],
              paths: [Array],
              [Symbol(kIsMainSymbol)]: true,
              [Symbol(kIsCachedByESMLoader)]: false,
              [Symbol(kIsExecuting)]: true
            }
          }
        }
        
        // module
         {
          id: '.',
          path: '/Users/gyuyeonjo/me/npm',
          exports: {},
          filename: '/Users/gyuyeonjo/me/npm/test.js',
          loaded: false,
          children: [],
          paths: [
            '/Users/gyuyeonjo/me/npm/node_modules',
            '/Users/gyuyeonjo/me/node_modules',
            '/Users/gyuyeonjo/node_modules',
            '/Users/node_modules',
            '/node_modules'
          ],
          [Symbol(kIsMainSymbol)]: true,
          [Symbol(kIsCachedByESMLoader)]: false,
          [Symbol(kIsExecuting)]: true
        }
        ```
        
        모듈 가져오기
        
        - require()의 실제 구현 간소화
        
        ```jsx
        function require(moduleName) {
        	const module = {exports: {}}
        	
        	;((module, exports) => {
        		// 모듈 코드
        	})(module, module.exports
        	return module.exports
        }
        ```
        
        - 파일 모듈 불러오기 : 인자로 절대경로, 상대경로. 확장자는 js, json, .node 순으로 탐색. 이외의 확장자는 반드시 명시하기
        - 모듈로 정의된 폴더(인자에 폴더가 들어가는 경우)
            - package.json 파일에 main 속성이 정의된 경우 해당 파일 찾기
            - 혹언 index.js 파일 찾기
            - 둘다 없으면 MODULE_NOT_FOUND 에러
        - node_modules 폴더
            - 탐색 순서
                1. /home/ry/projects/node_modules/bar.js
                2. /home/ry/node_modules/bar.js
                3. /home/node_modules/bar.js
                4. /node_modules/bar.js
        - 전역 폴더 - 별로 추천하지 않음
        - 코어 모듈
            - lib 폴더에 여러 코어 모듈 포함
            - 기본적으로 제공되는 모듈 (외부 의존성 설치하지 않고도 사용 가능)
        
        require.cache
        
        - 동일한 모듈을 다시 호출할 때, 이미 로드된 객체를 반환한다.
        
        ```jsx
        // module.js
        console.log('call')
        module.exports = 'hello'
        
        // index.js
        const data1 = require('./data.js')
        const data2 = require('./data.js')
        console.log(data1, data2)
        
        // 결과
        // call data
        // hello hello
        ```
        
    - 소스코드를 CommonJS로 빌드하기(번들링)
        - 번들링 : 빌드 프로세스에서 의존 관계에 있는 JS 파일들을 압축, 난독화해서 하나의 파일로 통합하는 작업
        - 번들러 : 번들러를 수행하는 도구
        - 트리 셰이킹 : 번들링 과정에서 사용되지 않는 모듈 제거하고 불필요한 코드 식별해 삭제하는 기술
            - 대표적 번들러 : Webpack, Parcel, Rollup, Vite 등 > CommonJS 빌드 지원
        
        Node.js의 CommonJS 특징이 빌드 과정에 미치는 영향, 번들러가 이것을 어떻게 처리할까
        
        - 모듈 래퍼는 클로저를 생성한다.
            - 서버 환경에서 CommonJS는 모든 모듈이 로컬 디스크에 존재해서 문제되지 않지만
            성능이 중요한 브라우저 환경에서는 매번 클로저 생성, 참조하는 방식이 문제
            → Rollup, Webpack같은 번들러로 모듈을 하나의 클로저로 통합해 require() 호출로 인한 성능 문제를 줄인다.
        - require()는 런타임에 사용할 모듈이 결정된다.
            - 모든 require() 호출을 단일 클로저로 연결하는 방식은 클로저 성능 이슈를 해겨할 수 있지만, 트리 셰이킹이 어려워진다.
            - 빌드 시점에 어떤 모듈이 실제로 어떤 함수를 사용할지 예측할 수 없어 모든 함수를 번들에 포함하게 되어 번들 크기가 증가하는 문제 발생
        - CommonJS 트리 셰이킹에 대한 사실
            - CommonJS가 반드시 트리 셰이킹이 불가능한건 아니지만 require()를 사용하는 경우 트리셰이킹이 어렵다는 점 숙지하자!
    
    <aside>
    
    ### 정리
    
    CommonJS는 JS의 서버 확장을 위해 제안된 모듈 표준이며, Node.js의 기본 모듈 시스템이다.
    
    exports/require 기반으로 동작하며, 독립적 모듈 스코프를 제공한다.
    
    브라우저에서는 번들러가 필요하며, 동적 로딩 특성상 빌드 최적화(트리 셰이킹)에 제약이 있다.
    
    </aside>
    
    # ESModule이란?
    
    CommonJS는 서버 사이드 개발에서는 good, 브라우저 환경에서는 어려움.
    
    ESModule 배경
    
    - CommonJS의 한계
        - 동기적 로딩 방식 : 모듈을 동기적으로 로드하기 때문에 서버환경은 ok, but 브라우저 환경은 로딩 중 블로킹 발생
        - 프리로딩 불가 : 모듈을 미리 로드할 수 없어 브라우저에서 로딩 최적화 어려움
        - 트리 셰이킹 및 최적화 어려움 : 런타임에 모듈을 동적으로 로드해서, 의존성 분석이 복잡, 트리 셰이킹 어려움
        - 메모리 이슈 : CommonJS 모듈 래퍼는 클로저를 생성해 각 모듈마다 독립적인 스코프 제공하는데, 파일이 추가될 때마다 클로저가 생성되어 브라우저에서 메모리 사용량 증가
        - 브라우저 호환성 문제 : Node.js 환경을 위해 설계된 시스템이기 때문에 브라우저에서 직접 사용 X, 웹팩같은 번들러 필요
    - ES6에서는 브라우저/서버 모두 일관되게 사용할 수 있는 표준 모듈 시스템인 ESModule 도입
    
    특징
    
    ES6부터 도입된 import/export 키워드를 기반으로 모듈을 정의하고 관리한다
    
    - 명세
        - export
            - named export와 default export로 나뉘며, 모듈 하나에서 이름으로 내보내기는 여러 개 존재, 기본 내보내기는 반드시 하나만 존재해야함
                
                ```jsx
                // named export 방식
                function sum(a, b) { return a + b; }
                const variable = '1'
                class CustomClass {}
                export {sum, variable, CustomClass}
                
                // named export 다른 형태
                export function sum(a, b) { return a + b; }
                export const variable = '1'
                export class CustomClass {}
                ```
                
                export default는 하나의 대상만 내보낼 수 있다. 즉 export default는 하나만 존재한다. 이렇게 기본 내보내기로 내보내면, 모듈 사용하는 쪽에서 원하는 이름으로 사용 가능하다.
                
                ```jsx
                // sum.js
                // 기본 내보내기
                export default function sum(a, b) {}
                // export default가 있더라도 다른 모듈을 이름으로 내보내기 가능
                export const variable = '1'; 
                // 중복으로 사용 불가
                export default class CustomClass {} // ❌
                ```
                
                추가로, export는 `function`, `class`, `const`, `let`, `var` **선언문**과 함께 써야 합니다.
                
                export 다음에 그냥 “할당문”(`=`)을 쓸 수는 없다.
                
                ```jsx
                // ❌
                export sum = (a, b) => { return a + b; } 
                
                // ✅
                export function sum(a, b) { return a + b; }
                ```
                
        - import
            - default 임포트 구문
            
            ```jsx
            import sum from './sum.js'
            // default export가 없는데 이렇게 불러오기 하면 아래 에러 뜸
            // SyntaxError: The requested module './test.js' does not provide an export named 'default'
            ```
            
            - named 임포트 구문
            
            ```jsx
            import {sum} from '.sum.js'
            ```
            
        - import.meta
            - CommonJS에서 __dirname, __filename과 같은 특별한 변수들을 모듈 래퍼 내부 모듈 스코프에서 제공한 것처럼 ESModule에서는 그대신 import.met를 지원한다.
            - 다음 속성과 메서드 제공
            
            ```jsx
            // console.log(import.meta)
            [Object: null prototype] {
              dirname: '/Users/gyuyeonjo/me/npm',
              filename: '/Users/gyuyeonjo/me/npm/use.js',
              resolve: [Function: resolve],
              url: 'file:///Users/gyuyeonjo/me/npm/use.js'
            }
            
            // 현재 모듈의 URL, 브라우저에서는 외부에서 스크립트를 가져온 URL, 
            // Node.js에서는 file://과 같은 파일 경로
            import.meta.url 
            
            // 주어진 모듈 이름을 모듈의 실제 URL로 변환해주는 함수
            import.meta.resolve(moduleName)
            
            ```
            
        - mjs 확장자 : ESModule 문법만 사용 가능한 파일 확장자이다.
        
    - 정적 모듈 로딩
        
        코드 실행되기 전에 필요한 모듈이 이미 로드돼 있음
        
        번들러가 소스코드를 번들링할 때 모든 의존성을 파악하고 포함시키는 방식으로 이뤄짐
        
        장점
        
        - 런타임 중 모듈 불러오는 시간 감소
        - 코드 예측 가능
        - 모듈 캐싱과 최적화 : 한번 로드된 모듈 재사용
        - 의존성 관리 용이
        - 번들 최적화, 트리 셰이킹을 쉽게 수행 가능
        
        동적 로드하는법
        
        ```jsx
        async function use() {
        	const module = await import('./myModule.js')
        }
        ```
        
    - 최상위 수준 await
        
        모듈의 최상위에서 await를 사용할 수 있다.
        
        즉 async 함수 밖에서 await 사용할 수 있다.
        
        대신 모듈 단위에서는 A모듈의 await가 끝날 때까지 B 모듈이 기다린다.
        
        ```jsx
        // moduleA
        const response = await fetch('httls://api')
        todoList = await response.json()
        export {todoList}
        
        // moduleB
        import {todoList} from './moduleA.js'
        console.log(todoList) // moduleA의 await를 다 기다린 후 완벽한 응답을 받음
        ```
        
    - ESModule이 의존성 그래프 구성하는 방식
        1. 모듈 파싱
            - 로드한 모듈 파일을 해석하는 단계에서 모듈 레코드 생성
            - 모듈 레코드는 실제 메모리에 로드된 모듈의 값을 관리하는 구조체 (의존성 정보도 저장)
            - 모듈이 문법적으로 유효한지 확인, 모듈의 구조 이해하는 작업 수반
            - 토큰화를 통해 문법적 구조를 이해
            - import문을 통한 의존성 분석
        2. 모듈 인스턴스화
            - 모듈의 export된 값들이 메모리에 할당되고 초기화되는 단계
            - 해당 모듈에서 import된 기능들도 메모리에 로드됨
            - 모듈 레코드의 export, import문이 해석되고, 필요한 값들이 메모리에 할당되어 모듈간 참조가 이루어짐 > 의존성 해결
            - export와 impor가 동일한 메모리 주소 참조
        3. 모듈 평가
            - 해당 모듈 코드가 실제로 실행됨
        
        동작 방식 정리
        
        | 모듈 파싱 | 모듈 인스턴스화 | 모듈 평가 |
        | --- | --- | --- |
        | 1. 시작
        2. 로드한 모듈의 구문 분석
        3. import문으로 다른 모듈의 의존성 분석 | 1. epoxrt 문을 따라 의존성의 가장 마지막 지점까지 모듈을 인스턴스화
        2. import문을 따라 가져올 모듈을 연결하여 모듈 레코드 완성 | 1. 코드 실행하여 실제 메모리에 모듈의 내용 로드
        2. 종료 |
        
    
- 프로젝트 아키텍처
    - 프로젝트 아키텍처가 중요한 이유
        
        잘 설계된 아키텍처는 유지보수가 쉽고, 확장성이 높으며, 시스템 성능을 최적화할 수 있다.
        
        아키텍처 설계 시 기본 원칙은 여러가지가 있는데,
        
        1. 시스템의 변경 용이성
        2. 시스템의 확장성 
        3. 시스템의 성능
        4. 시스템 보안
        5. 시스템의 가용성
        
        효과적인 아키텍처 설계 전략
        
        1. 모듈화를 통해 시스템을 여러 개의 작은 컴포넌트로 나누기
            - 각 컴포넌트는 독립적으로 개발, 테스트, 배포될 수 있음
        2. 재사용 가능한 컴포넌트 사용
        3. 클라우드 서비스, 마이크로서비스를 통해 시스템 확장성 높이기
        4. 성능 테스트와 보안 테스트 정기적 수행하여 지속적 개선 가능
        5. 사용자 피드백을 반영해 시스템을 지속적으로 개선한다.
        
    - Service-Oriented Architecture(Service Layer Pattern)
        
        서비스 지향 아키텍처는 애플리케이션 기능을 비즈니스 적인 의미를 가지는 기능 단위로 묶어,
        
        서로 표준화된 인터페이스(API 형식)를 통해 통신한다.
        
        각 서비스는 특정 기능을 수행하는 독립적인 모듈이다.
        
        예를 들어 전자상거래 시스템을 만든다고 하면,
        
        - 사용자 서비스 : 회원가입, 로그인, 정보 수정 역할
        - 상품 서비스 : 상품 등록, 조회, 수정
        - 주문 서비스 : 주문 생성, 결제 처리
        - 알림 서비스 : 이메일 또는 문자 발송
        
        장점은 무엇인가?
        
        - 출시 기간 단축 : 기존에 있는 서비스를 재사용 가능
        - 효율적인 유지 보수
        - 뛰어난 적응성
        
        기본 원칙이나 표준 지침은 없지만, 아래 원칙이 일반적이다.
        
        - 상호 운용성 : 각 서비스에는 서비스 기능과 관련된 약관을 지정하는 설명 문서가 포함되어있고, 플랫폼이나 프로그램 언어에 관계없기 때문에 각 서비스가 다른 언어로 개발되는 것도 가능하다.
        - 느슨한 결합 : 서비스끼리 최대한 독립적이어야한다. 즉, 서로 내부 구현을 몰라도, 명세된 인터페이스 API만으로 통신이 가능하다.
        
        SOA의 구성요소는?
        
        - 서비스
        - 서비스 공급자
        - 서비스 소비자
        - 서비스 레지스트리
        
    - MVC 패턴
        
        SOA가 시스템 간 구조를 짜는 툴이라면, MVC는 하나의 시스템 내부를 효율적으로 나누는 툴이다.
        
        MVC는 model-view-controller 약자로,
        
        - 모델: 데이터와 비즈니스 로직을 담당
        - 뷰 : 사용자가 실제로 보는 부분, 로직은 거의 없고 시각적인 표현
        - 컨트롤러 : model과 view를 연결하는 중간 관리자
        
        로 이루어져있다.
        
        유의사항
        
        - 작은 프로젝트에는 구조과 과도하게 복잡해지니, 프로젝트 크기에 따라서 사용하자.
        - 기능이 많아질 수록 contoller가 점점 비대해진다.
        - 구조를 나누고 연결하는데 시간이 많이 들어 프로토타입 개발 시 속도가 느리다.
    - 그 외 다른 프로젝트 구조
        
        ## **NestJS 구조**
        
        1. **계층형 구조(Layered Architecture) 기반**
            - **Controller**: 요청과 응답 처리, 라우팅 담당
            - **Service**: 비즈니스 로직 처리
            - **Module**: 관련 기능들을 그룹핑 (의존성 관리)
            - **Provider / Repository**: DB 접근, 외부 API 연동 담당
        2. **의존성 주입(DI) 지원**
            - Angular와 비슷하게 DI 컨테이너를 통해 Service, Provider를 주입
            - 모듈 간 결합도를 낮춤
        3. **유연한 구조**
            - MVC처럼 Controller-Service를 나누어 역할 분리
            - Clean Architecture처럼 모듈화 가능
            - Microservices, GraphQL, WebSocket 등 다양한 아키텍처와 통합 가능
        
        ## MVP
        
        1. **Model**
            - 애플리케이션의 데이터와 비즈니스 로직을 담당
            - UI와 독립적으로 동작
            - 예: DB 조회, 계산, 상태 관리
        2. **View**
            - 사용자에게 보여지는 화면과 입력 처리 담당
            - Presenter와 상호작용
            - 가능한 한 로직을 갖지 않고 단순히 화면 표시와 사용자 입력만 처리
        3. **Presenter**
            - View와 Model 사이의 중재자 역할
            - View에서 발생한 이벤트를 받아 Model을 업데이트
            - Model의 변경사항을 받아 View를 업데이트
            - **UI 로직이 여기에 집중**되며, View는 단순히 Presenter를 호출
        
        흐름 예시
        
        1. 사용자가 버튼 클릭 → **View**
        2. View는 클릭 이벤트를 → **Presenter**
        3. Presenter는 필요한 로직 수행 → **Model**
        4. Model이 데이터 반환 → **Presenter**
        5. Presenter가 결과를 View에 전달 → 화면 업데이트
        
        장점
        
        - **UI와 로직 분리** → 테스트 용이
        - View 교체 용이 → 같은 Presenter에 다른 UI 적용 가능
        - 유지보수 쉬움 → Presenter에 로직 집중
        
        단점
        
        - Presenter가 비대해질 수 있음
        - MVC보다 코드가 조금 더 복잡
- 비즈니스 로직
    
    비즈니스 로직은 소프트웨어에서 사용자 요구나 실제 비즈니스 규칙을 처리하는 부분
    
    - 예: 쇼핑몰 앱
        - 상품 재고 차감
        - 할인 적용 계산
        - 결제 승인
        - 포인트 적립
    
- DTO
    
    DTO는 Data Transfrer Object 데이터 전송 객체로, 데이터를 전달하는 객체를 의미한다. 주로 클라이언트에서 API를 요청해 데이터를 주고받을 때 정해진 데이터 양식을 지키기 위해 사용된다.
    
    왜 DTO를 사용하는게 좋은가?
    
    - 계층 간 의존성 최소화
        - DB 엔티티나 내부 도메일 모델을 그대로 View나 외부 API에 노출하면 변경하기 까다로워진다. DTO를 사용하면 내부 구조를 바꿔도 외부 계층에 영향을 최소화할 수 있따.
    - 네트워크/데이터 전송 최적화
        - 필요 없는 필드는 줄이고, 필요한 데이터만 담아서 전송할 수 있다.
        - json 직렬화 시 용량이 감소되어 성능이 개선된다.
    - API 명세와 문서화 용이
        - 어떤 데이터가 오고 가는지 명확하게 문서화가 가능하다.
    - 테스트 및 유지보수 용이
    - 보안
        - 엔티티에는 내부적으로 민감한 데이터가 있는데, DTO로 외부 노출 차단이 가능하다