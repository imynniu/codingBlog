## axios使用IP代理发送请求

使用IP代理发送请求不仅能够更好的保护使用者的安全，同时使用代理可以更换IP，‘解锁’一些网站对于IP的限制。

### 1 使用工具

1 本文使用的是**axios模块发送请求**，根据自己的情况可以更换其他的模块。使用https代理的时候，我们还需要的一个模块是**`https-proxy-agent`**

2 **常见的IP代理**，本人使用的是免费的[四叶天](https://www.siyetian.com/apis.html)，注册登录之后有免费的额度可以使用，同时，闪臣代理也不错，相比四叶天收费略贵。

3 **[测试网址](https://ident.me/ip)**，`ident.me/ip`点击打开该网址之后可以看到自己的IP，如果失效可以自行百度跟换一个以他的。

### 2 JS实现

#### 1 创建一个简单如request请求

```js
import axios from "axios";

const main = async () => {
  const url = "http://ident.me/ip";
  axios({
    method: "get",
    url,
  })
    .then((res) => console.log(res.data))
    .catch((err) => console.log(err));
};

main();
```

通过这个程序，我们可以看的到ident给我们返回的IP地址，就是我们的网络IP

#### 2 注册使用四叶天的IP代理

> 四叶天可以每天登录免费领一些ip使用

![1705149070428](H:\codingBlog\images\1705149070428.png)

通过四叶天生成的专属API链接，我们每调用一次就会生成一个新的IP地址和端口号给我们使用。

![1705149694682](H:\codingBlog\images\1705149694682.png)

所以我们可以先发起一个get调用一下这个链接,并且我们可以直接封装成一个函数直接使用。

```js
const proxyUrl =
  "http://proxy.siyetian.com/apis_get.html?token=gHbi1ST6NWMNpXT59ERVpnTn1STqFUeORUQ41EVBh3TUVUeNRVS39ERjNjTqFEe.gM1ATO0ETNwcTM&limit=1&type=0&time=&split=1&split_text=";
const getTheIp = async () => {
  let data;
  await axios({
    method: "get",
    url: proxyUrl,
  })
    .then((res) => {
      data = res.data;
    })
    .catch((err) => console.log(err));
  console.log(data);
  return data;
};
```

我们可以测试调用一下。

#### 3 在axios发起请求的时候使用代理

在使用代理的时候我们还需要一个模块就是`https-proxy-agent`

该有的东西都有了，我们只需要进行简单的组合。

```js
import axios from "axios";
import { HttpsProxyAgent } from "https-proxy-agent";
const proxyUrl =
  "http://proxy.siyetian.com/apis_get.html?token=gHbi1ST6NWMNpXT59ERVpnTn1STqFUeORUQ41EVBh3TUVUeNRVS39ERjNjTqFEe.wM0ADN5ETNwcTM&limit=1&type=0&time=&split=1&split_text=";
const getTheIp = async () => {
  let data;
  await axios({
    method: "get",
    url: proxyUrl,
  })
    .then((res) => {
      data = res.data;
    })
    .catch((err) => console.log(err));
  return data;
};
const main = async () => {
  const url = "http://ident.me/ip";
  const host = await getTheIp();
  const agent_url = `http://${host}`;
  console.log(agent_url);

  const httpsAgent = new HttpsProxyAgent(agent_url);
  axios
    .get(url, httpsAgent)
    .then((res) => console.log(res.data))
    .catch((err) => console.log(err));
};

main();

```

运行一下，我们可以发现我的IP，是每次发送请求每次就会改变的，这是因为代理IP帮助我们发送的http请求。

![1705195125474](H:\codingBlog\images\1705195125474.png)
