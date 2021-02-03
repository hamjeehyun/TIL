`yarn add module` 후

Add @babel/plugin-transform-react-jsx ([https://git.io/vb4yd](https://git.io/vb4yd)) to the 'plugins' section of your Babel config to enable transformation.

에러가 떴다. 당황했지만 당황하지 않은 척하고 해결해보기...



\[해결과정\]

1\. package.json에 있는 `@babel/plugin-transform-react-jsx` 을 지움

2\. node\_modules파일을 삭제

3\. `yarn`재설치

rootReducer에 reducer추가는 되었으나, rootSagas에 sagas를 추가하자 동일한 오류가 나왔다ㅠㅠ

범인은 너였구나😑

알고봤더니 내가 만든 module 자체의 문제였다.💦 

어쨌든 저 문제는 위와 같이 진행하면 해결된다.
