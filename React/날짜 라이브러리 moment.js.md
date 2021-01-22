
# 날짜 라이브러리 moment.js

오늘의 일은 **오늘 이후의 날짜를 선택해서 서버로 보내주는 작업**이었다.

### moment 설치
```javascript
$ npm i moment
// 또는
$ yarn add moment
```

### 사용 예시
```javascript
import moment from 'moment';
import 'moment/locale/ko';

const now = moment().format('YYYY-MM-DD HH:mm:ss');
console.log(now);
// 출력 결과: YYYY-MM-DD HH:mm:SS
```

moment 라이브러리는 위와같이 간단하게 사용가능하다.
나는 format을 따로 선언해주고 썼다.
그러는게 관리가 더 편하기 때문이다.

```javascript
import moment from 'moment';
import 'moment/locale/ko';

const dateFormat = 'YYYY-MM'
const now = moment().format(dateFormat);
console.log(now);
```
이런식으로

<br>

### 날짜 더하고 빼기
```javascript
moment().add(1,'days') // 하루 더하기
moment().subtract(1,'days') // 하루 빼기
moment().add(1,'months') // 한달 더하기
moment().subtract(1,'year') // 1년 빼기
```
오늘 이전의 날짜들은 모두 disabled 해야하므로 저걸 쓰려고 했다.
<br>


# Ant Design - Date Picker
```javascript
ReactDOM.render(
  <Space direction="vertical" size={12}>
    <DatePicker
      format="YYYY-MM-DD"
      disabledDate={
        function disabledDate(current) {
          // Can not select days before today and today
            return current && current < moment().endOf('day');
          }
      OnChange={this.handleChangeDateValue}
    />
    )
```
ant design은 정말 좋은 곳이다. 웬만하면 다 있다. 버전 업이 됐다는데 나도 좀 버전업 된 걸 쓰고싶다. 근데 이걸 버전하려면 다른 것들도 다 마이그레이션 해야하는데 그걸 언제 또 하나 싶어서 관두게 된다. 나중에 한 번 말을 해볼까.

하여튼 저거는 `DatePicker`다. 날짜를 반환해준다. Ant Design 들어가서 보면 월만 선택하는 것도 시간까지 선택하는 것도 있고 다양하다.

`current && current < moment().endOf('day');` 이 부분이 오늘을 포함한 이전의 날짜를 disabled하는 부분이다.

`OnChange`로 받았을 때는 날짜값이 `event._d`에 있다. `DatePicker`에서 지정해준 format으로 날라온다.

```javascript
handleChangeDateValue = (event) => {
  const date = event._d; // 날짜값
  // TODO 원하는 것을 하세요
}
```

사실상 보니까 `moment().add(1,'days') `이건 안썼다. 새로운걸 알았다는데에 의의를 두기로 한다. 개발하면서 이정도의 뻘짓이야 익숙하다.


[momentjs 공식문서](https://momentjs.com/docs/)<br>
[Ant Design DatePicker](https://ant.design/components/date-picker/#header)
<br>
