# TripleUni - Script
## How to use
### 1. Copy the code below
The code below is a template. You can copy it and paste it to tripleuni.
``` html
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
``` html
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
* Please note that you can't use `document` to get the document of the parent page(page of tripleuni). You should use `pdoc` instead. `pdoc` is the document of the parent page. You can use it to edit the html.
* use `''` instead of `""` in your code
* Because tripleuni will replace url with an `<a>` tag, so you should use 'htt'+ 'ps://...' instead of 'https://...'

### 3. Paste the code to tripleuni and publish it
* Paste the code to tripleuni
* Click the publish button
* The script will run when you open your post on tripleuni

## Examples
You can edit the html with js. The `pdoc` variable is the document of the parent page(page of tripleuni). You can use it to edit the html.
### Edit the html
``` javascript
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

### Send a request
``` javascript
// send a request
let xhr = new XMLHttpRequest();
xhr.open('GET', 'https://www.baidu.com');
xhr.send();
```

### Redirect to another page
``` javascript
// redirect to another page
pdoc.location.href = 'https://www.baidu.com';
```

### Get the id of the post
``` javascript
// get the url of the post
let url = pdoc.location.href;
// get the id of the post
let id = url.split('/').pop();
```
