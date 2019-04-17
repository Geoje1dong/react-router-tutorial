# react router tutorial

## 라이브러리 설치
```{.terminal}
    $ yarn add react-router-dom
    $ yarn add cross-env --dev
```

1. react-router-dom: 브라우저에서 사용되는 리액트 라우터 입니다.
2. cross-env: 프로젝트에서 NODE_PATH 를 사용하여 절대경로로 파일을 불러오기 위하여 환경 변수를 설정 할 때 운영체제마다 방식이 다르므로 공통적인 방법으로 설정 할 수 있게 해주는 라이브러리입니다.

## 디렉토리 생성

1. src/components: 컴포넌트들이 위치하는 디렉토리입니다.
2. src/pages: 각 라우트들이 위치하는 디렉토리 입니다.
3. src/client: 브라우저 측에서 사용할 최상위 컴포넌트 입니다. 우리가 추후 서버사이드 렌더링을 구현 할 것이기 때문에 디렉토리를 따로 구분하였습니다. (서버사이드 렌더링을 할 때에는 서버 전용 라우터를 써야합니다.) 여기서 라우터를 설정합니다.
4. src/server: 서버측에서 사용 할 리액트 관련 코드를 여기에 넣습니다.
5. src/shared: 서버와 클라이언트에서 공용으로 사용되는 컴포넌트 App.js 가 여기에 위치합니다.
6. src/lib: 나중에 웹 연동을 구현 할 때 사용 할 API와 코드스플리팅 할 때 필요한 코드가 여기에 위치합니다.

## NODE_ENV 설정
상대 경로를 절대 경로로 셋팅 하기 위해 설정법 'package.json' 'script' 부분을 수정

### 기존
```{.json}
"scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject"
  }
```

### 수정
```{.json}
"scripts": {
    "start": "cross-env NODE_PATH=src react-scripts start",
    "build": "cross-env NODE_PATH=src react-scripts build",
    "test": "react-scripts test --env=jsdom",
    "eject": "react-scripts eject"
  }
```

## 컴포넌트 준비하기
`src/client/Root.js`
```{.javascript}
    import React from 'react';
    import { BrowserRouter } from 'react-router-dom';
    import App from 'shared/App';
    
    const Root = () => (
        <BrowserRouter>
            <App/>
        </BrowserRouter>
    );
    
    export default Root;
```

이제 우리가 만든 파일에 맞춰서 index.js 를 수정하세요.
`src/index.js`
```{.javascript}
import React from 'react';
import ReactDOM from 'react-dom';
import Root from './client/Root';

ReactDOM.render(<Root />, document.getElementById('root'));
```

## Router 와 파라미터 준비

### 기본 라우터 준비
자 그럼 우리의 첫 라우트 Home, About을 만들어보겠습니다.
이 라우터는 주소에 아무 path 도 주어지지 않았을 때 기본적으로 보여주는 라우트입니다.

'src/pages/Home.js'
```
    import React from 'react';
    
    const Home = () => {
        return(
            <div>
                <h2>홈</h2>
            </div>
        );
    };
    
    export default Home;
```

`src/pages/About.js`
```
    import React from 'react';
    
    const About = () => {
        return (
            <div>
                <h2>About</h2>
            </div>
        );
    };
    
    export default About;
```

`src/pages/index.js`
```
    export { default as Home } from './Home';
    export { default as About } from './About';
```

### 라우트 설정하기
`src/shared/App.js`
```
import React, { Component } from 'react';
import { Route } from 'react-router-dom';
import { Home, About } from 'pages';


class App extends Component {
    render() {
        return (
            <div>
                <Route exact path="/" component={Home}/>
                <Route path="/about" component={About}/>
            </div>
        );
    }
}

export default App;
```
라우트를 설정 할 때에는 Route 컴포넌트를 사용하고, 경로는 `path` 값으로 설정합니다.
첫번째 라우트 / 의 경우에는 Home 컴포넌트를 보여주게 했고, 두번째 라우트 `/about` 에서는 About 컴포넌트를 보여주게 했습니다.
첫번째 라우트의 경우엔 `exact` 가 붙어있지요? 이게 붙어있으면 주어진 경로와 정확히 맞아 떨어져야만 설정한 컴포넌트를 보여줍니다.
About의 'path'를 Home과 같이 / 하니 Home의 내용과 About 내용이 나왔다.
'exact'가 없으면 Home일 경우 `/about`에도 `/` 포함하기 때문에 about 에도 home의 내용이 나온다.

### 라우트 파라미터 읽기
라우트의 경로에 특정 값을 넣는 방법을 알아보겠습니다. 방법은 두가지가 있는데요, params 를 사용하는 것 과, query 를 사용하는 것 입니다.
라우트로 설정한 컴포넌트는, 3가지의 props 를 전달받게 됩니다:
    1. 'history' 이 객체를 통해 'push', 'replace' 를 통해 다른 경로로 이동하거나 앞 뒤 페이지로 전환 할 수 있습니다.
    2. 'location' 이 객체는 현재 경로에 대한 정보를 지니고 있고 URL 쿼리 ('/about?foo=bar' 형식) 정보도 가지고있습니다.
    3. 'match' 이 객체에는 어떤 라우트에 매칭이 되었는지에 대한 정보가 있고 params ('/about/:name' 형식) 정보를 가지고있습니다.

참조 사이트 VELOPERTI님의 react-router :: 1장. 리액트 라우터 사용해보기[https://velopert.com/3417]