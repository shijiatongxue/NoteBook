# axios

Promise based HTTP client for the browser and node.js

## 特点

- 从浏览器发送XMLHttpRequest请求
- **从node.js发送HTTP请求**
- **支持Promise API**
- 转化请求和响应
- 取消请求
- **并发请求**
- JSON数据自动转化
- 客户端XSRF攻击防御

## 安装

```bash
// 使用npm
npm install axios
```

```bash
// 使用yarn
yarn add axios
```

```bash
// 使用CDN
<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
```

## 例子

### GET

```js
// GET

const axios = require('axios');

axios.get('/user?ID=12345')
	.then(function(response) {
  	// handle success
  	console.log(response);
	})
	.catch(function(error){
  	// handle error
  	console.log(err);       
  })
	.finally(function(){
  	// always executed
	});
```

或把参数放在一个对象中：

```js
// GET

axios.get('/user', {
  params: {
  	ID: 12345
  }
})
.then()
```

使用异步：

```js
// GET

async function getUser() {
  try {
  	const response = await axios.get('/user?ID=12345');
    console.log(response);
  } catch (error) {
  	console.error(error);
  }
}
```

### POST

```js
// POST

axios.post('/user', {
  firstName: 'Fred',
  lastName: 'Flintstone'
})
.then(function(response){
  console.log(response);
})
.catch(function(error){
  console.log(error);
})
```

### axios API

可以给axios传入设置（config）。

- **axios(config)**

```js
// Send a POST request

axios({
  method: 'post',
  url: 'user/12345',
  data: {
  	firstName: 'Fred',
   	lastName: 'Flintstone'
  }
});
```

```js
// Get request for remote image

axios({
  method: 'get',
  url: 'http://bit.ly/2mTM3nY',
  responseType: 'steam'
})
.then(function(respoonse){
  response.data.pipe(fs.createWriteStream('ada_lovelace.jpg'))
})
```

- **axios(url[, config])**

```js
// Send a GET request (默认方法)

axios('/user/12345');
```

### 方法别名

- **axios.request(config)**

- **axios.get(url[, config])**

- **axios.delete(url[, config])**

- ##### axios.head(url[, config])

- ##### axios.options(url[, config])

- ##### axios.post(url[, data[, config]])

- ##### axios.put(url[, data[, config]])

- ##### axios.patch(url[, data[, config]])

### 创建一个实例

- **axios.create([config])**

```js
const instance = axios.create({
  baseURL: 'https://shijiatongxue/api/',
  timeout: 1000,
  headers: {'X-Custom-Header': 'foobar'}
})
```

实例方法：

下面的配置会和实例的配置合并。

- ##### axios#request(config)

- ##### axios#get(url[, config])

- ##### axios#delete(url[, config])

- ##### axios#head(url[, config])

- ##### axios#options(url[, config])

- ##### axios#post(url[, data[, config]])

- ##### axios#put(url[, data[, config]])

- ##### axios#patch(url[, data[, config]])

- ##### axios#getUri([config])

## 请求配置

下面是请求的一些配置，其中只有url是必需的，其他都是可选的。如果method没有指定的话默认会是GET方法。

```js
{
  url: '/user',
  method: 'get', // default
 	baseURL: 'https://some-domain.com/api/',
  // PUT, POST, PATCH, DELETE only
  transformRequest: [function (data, headers) {
    // Do whatever you want to transform the data
                      
    return data;
  }],
  transformResponse: [function (data) {
    // Do whatever you want to transform the data

    return data;
  }],
  headers: {'X-Requested-With': 'XMLHttpRequest'},
  params: {
    ID: 12345
  },
  paramsSerializer: function (params) {
    return Qs.stringify(params, {arrayFormat: 'brackets'})
  },
  // PUT, POST, PATCH only
  data: {
    firstName: 'Fred'
  },
  timeout: 1000, // default is `0` (no timeout)
  // `withCredentials` indicates whether or not cross-site Access-Control requests
  // should be made using credentials
  withCredentials: false, // default
  adapter: function (config) {
    /* ... */
  },
  responseType: 'json', // default
  responseEncoding: 'utf8', // default
  xsrfCookieName: 'XSRF-TOKEN', // default
  xsrfHeaderName: 'X-XSRF-TOKEN', // default
 	onUploadProgress: function (progressEvent) {
    // Do whatever you want with the native progress event
  },
  onDownloadProgress: function (progressEvent) {
    // Do whatever you want with the native progress event
  },
  maxContentLength: 2000,
	validateStatus: function (status) {
    return status >= 200 && status < 300; // default
  },
 	maxRedirects: 5,
  socketPath: null, // default
  httpAgent: new http.Agent({ keepAlive: true }),
  httpsAgent: new https.Agent({ keepAlive: true }),
  proxy: {
    host: '127.0.0.1',
    port: 9000,
    auth: {
      username: 'mikeymike',
      password: 'rapunz3l'
    }
  },
  cancelToken: new CancelToken(function (cancel) {
  })
}
```

## 返回结构

```js
{
  data: {},
  status: 200,
  statusText: 'OK',
  headers: {},
  config: {},
  request: {}
}
```

## 配置默认项

### 全局axios默认

```js
axios.defaults.baseURL = 'https://api.shijia.com';
axios.defaults.headers.common['Authorization'] = AUTH_TOKEN;
axios.defaults.headers.post['Content-Type'] = 'application/x-www-form-urlencoded';
```

### 局部实例默认

```js
const instance = axios.create({
  baseURL: 'https://api.example.com'
});

instance.defaults.headers.common['Authorization'] = AUTH_TOKEN;
```

## 拦截器

可以在请求和返回的then和catch之前进行拦截。

```js
// Add a request interceptor
axios.interceptors.request.use(function (config) {
    // Do something before request is sent
    return config;
  }, function (error) {
    // Do something with request error
    return Promise.reject(error);
  });

// Add a response interceptor
axios.interceptors.response.use(function (response) {
    // Do something with response data
    return response;
  }, function (error) {
    // Do something with response error
    return Promise.reject(error);
  });
```

移除拦截器：

```js
const myInterceptor = axios.interceptors.request.use(function () {/*...*/});
axios.interceptors.request.eject(myInterceptor);
```

添加拦截器：

```js
const instance = axios.create();
instance.interceptors.request.use(function () {/*...*/});
```

## 处理错误

```js
axios.get('/user/12345')
  .catch(function (error) {
    if (error.response) {
      // The request was made and the server responded with a status code
      // that falls out of the range of 2xx
      console.log(error.response.data);
      console.log(error.response.status);
      console.log(error.response.headers);
    } else if (error.request) {
      // The request was made but no response was received
      // `error.request` is an instance of XMLHttpRequest in the browser and an instance of
      // http.ClientRequest in node.js
      console.log(error.request);
    } else {
      // Something happened in setting up the request that triggered an Error
      console.log('Error', error.message);
    }
    console.log(error.config);
  });
```

## 使用application/x-www-form-urlencoded格式

默认情况下，axios发送的是JSON对象。如果想要发送application/x-www-form-urlencoded格式，可以设置下面：

### 在浏览器

```js
const params = new URLSearchParams();
params.append('param1', 'value1');
params.append('param2', 'value2');
axios.get('/foo', params);
```

> 但是URLSearchParams不是所有浏览器都支持的，下面是polyfill（确保polyfill在全局使用）。

使用qs：

```js
const qs = require('qs');
axios.post('/foo', qs.stringify({'bar': 123}));
```

或用ES6使用qs：

```js
import qs from 'qs';
const data = {'bar': 123};
const options  = {
  method: 'POST',
  headers: {'content-type': 'application/x-www-form-urlencoded'},
  data: qs.stringify(data),
  url,
};
axios(options);
```

### 在Node.js

```js
const querystring = require('querystring');
axios.get('http://shijia.com/', querystring.stringify({foo: 'bar'}));
```

在Node也可以使用qs。

---

参考：[文档](https://github.com/axios/axios)









