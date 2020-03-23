# redux-saga

`redux-saga` 是 redux 的中间件，它主要进行异步操作管理。

## 安装

```bash
npm install --save redux-saga

// 或者
yarn add redux-saga
```

## 例子

```javascript
class UserComponent extends React.Component {
  ...
  onSomeButtonClicked() {
    const { userId, dispatch } = this.props
    dispatch({type: 'USER_FETCH_REQUESTED', payload: {userId}})
  }
	...
}
```

我们会创建一个监听所有 `USER_FETCH_REQUESTED` action 的 Saga 然后触发 API 去取用户的数据。

 ```javascript
// saga.js

import { call, put, takeEvery, takeLatest } from 'redux-saga/effects'
import Api from '...'

// worker Saga: will be fired on USER_FETCH_REQUESTED acitons
function* fetchUser(action) {
  try {
    const user = yield call(Api.fetchUser, action.payload.userId)
    yield put({type: "USER_FETCH_REQUESTED", user: user})
  } catch (e) {
    yield put({type: "USER_FETCH_REQUESTED", message: e.message})
  }
}

/*
  Starts fetchUser on each dispatched `USER_FETCH_REQUESTED` action.
  Allows concurrent fetches of user.
*/
function* mySaga() {
  yield takeEvery("USER_FETCH_REQUESTED", fetchUser);
}

/*
  Alternatively you may use takeLatest.

  Does not allow concurrent fetches of user. If "USER_FETCH_REQUESTED" gets
  dispatched while a fetch is already pending, that pending fetch is cancelled
  and only the latest one will be run.
*/
function* mySaga() {
  yield takeLatest("USER_FETCH_REQUESTED", fetchUser);
}

export default mySaga;
 ```

运行 Saga，需要连接 Redux Store：

```javascript
// main.js

import { createStore, applyMiddleware } from 'redux'
import createSagaMiddleware from 'redux-saga'

import reducer from './reducers'
import mySaga from './sagas'

// create the saga middleware
const sagaMiddleware = createSagaMiddleware()
// mount it on the Store
const store = createStore(
  reducer,
  applyMiddleware(sagaMiddleware)
)

// then run the saga
sagaMiddleware.run(mySaga)

// render the application
```

---

参考：[github](https://github.com/redux-saga/redux-saga)



