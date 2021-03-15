오늘은 컬럼이 바뀌는 동적 테이블을 만들어야한다. 



서버에서 받아오는 JSON의 형태는 다음과 같다. 여기서 바뀌는 것은 cols의 개수와 cols의 안에 있는 요소들의 개수이다. 즉 column의 개수와 명칭이 유동적으로 바뀌게 되는 것이다. 

name이 column의 명칭이 되고 value값이 해당 column의 값으로 들어가야 한다.

```json
[
  {
    "cols": [
    {
      "name": "name1",
      "value": "value1"
    },
    {
       "name": "name2",
       "value": "value2"
     },
     {
       "name": "name3",
       "value": "value3"
     }
   ]
 },
 {
   "cols": [
     {
       "name": "name1",
       "value": "value4"
     },
     {
       "name": "name2",
       "value": "value5"
     },
     {
       "name": "name3",
       "value": "value6"
     }
   ]
 }
]
```



즉, 위의 JSON 형태가 다음과 같은 형태로 변환되어야한다는 것이다. 

``` json
[
    {
      "name1": "value1",
      "name2": "value2",
      "name3": "value3"
    },
    {
      "name1": "value4",
      "name2": "value5",
      "name3": "value6"
    }
]
```



해당 구현은 Ant Design의 Table을 이용하였다.  Table을 사용하는 방법은 [Ant Design](https://ant.design/components/table/)에서 확인하면 된다.



1. #### 우선 Component에 Table을 선언

```jsx
<Table
    data={this.getListData(listData)}
    columns={this.columns}
 />
```

Json으로 받아온 정보를 그대로 Table에 넣어줄 수는 없기 때문에 가공이 필요하다. 때문에 getListData를 선언해주어 listData를 보기좋게 가공해주자.



2. #### Colums 선언

```jsx
this.columns = this.props.listData[0].cols.map((colsData) => {
    return {
        title: colsData.name,
        dataIndex: colsData.name,
        width: '*%',
        align: 'center',
    };
});
```

우선 Table의 Colums을 만들어줘야한다. listData의 첫번째 cols에 있는 name들로 column을 만들면 된다. 

`map()` 메서드는 배열 내의 모든 요소 각각에 대하여 주어진 함수를 호출한 결과를 모아 새로운 배열을 반환한다. 쉽게 생각하면 java에 있는 for문이라고 생각하면 된다. 간략한 사용법은 다음과 같다.

```javascript
arr.map(callback(currentValue, index, array) => {
    // TODO 필요한 Callback 기능 구현
    
    // currentValue: 처리할 현재 요소
    // index: 처리할 현재 요소의 인덱스
    // array: map()을 호출한 배열
})
```



3. #### data에 들어갈 getListData 구현

```jsx
getListData = (listData) => {
        let list = []; // (1) 빈 리스트 선언

        listData.map((data) => { 
            const cols = data.cols;
            let value = {};

            list.push(value); // (2) 요소들을 하나씩 list에 push 한다.

            return cols.map((data) => {
                let name = data.name;
        
                return value[name] = data.value; // (3) Json 요소 입력
            });
        });
        return list;
    };
```

listData는 위에 정의한 json형태로 들어온다.  

우선 (1)에서 빈 리스트를 선언해준다. 여기에 잘 가공된 json을 넣어 return 해줄 것이다. 

(2) 에서는 listData를 하나씩 순회하며 다음 map()에서 반환된 요소들을 list에 push 한다. push()는 array에 요소를 하나씩 추가해주는 메서드다. 



만약 listData가 다음과 같은 json 형태라고 했을 때를 생각해보자.

[실행 전]

```json
[
     {
       "name": "name1",
       "value": "value1"
     }
]
```



여기에 다음과 같은 코드를 실행하면,

```javascript
const data = [
     {
       name: 'name2',
       value: 'value2'
     }
];

listData.push(data);
```



다음과 같이 배열의 요소가 추가된다.

[실행 후]

```json
[
     {
       "name": "name1",
       "value": "value1"
     },
     {
       name: 'name2',
       value: 'value2'
     }
]
```



(3)에서는 요소를 넣어준다. cols의 요소를 하나씩 순회하며 name을 column으로, value를 해당 colums 의 값으로 만든다. 

다시 다음과 같은 json 형태를 생각해보자. data이 요소에 type라는 요소를 추가하고 값을 넣고자 할 때 다음과 같이 간단하게 가능하다.

```json
const data = {
    name: "name",
    value: "value"
}

data.type = "Numeric"
```



[실행 후]

```json
{
    name: "name",
    value: "value",
    type: "Numeric"
}
```





4. #### 전체코드

```jsx
import React, {Component} from "react";
import {Table} from "antd";

class Graph extends Component {
    constructor(props) {
        this.columns = this.props.listData[0].cols.map((colsData) => {
            return {
                title: colsData.name,
                dataIndex: colsData.name,
                width: '*%',
                align: 'center',
            };
        });
    }
    
    getListData = (listData) => {
        let list = [];

        listData.map((data) => {
            const cols = data.cols;
            let value = {};

            list.push(value);

            return cols.map((data) => {
                let name = data.name;
        
                return value[name] = data.value;
            });
        });
        return list;
    };

    render() {
        return (
            <Table
                data={this.getListData(listData)}
                columns={this.columns}/>
        );
    }
}
```



[실행화면]

![image-20210315133343780](C:\Users\2075j\AppData\Roaming\Typora\typora-user-images\image-20210315133343780.png)

https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/map
