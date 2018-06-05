### 1. Spring 框架用法

1.1 每次新加一个版本的时候，都要把所依赖项目的版本号改一下，即类似于 ```<version>1.0.0-SNAPSHOT</version>``` 这种

### 2. html 中 id 和 class 的用法

代码如下：
```html
<#list pageIterator.data as item>
    <tr class="gradeA odd" role="row" >
        <td class="id">${item.id?c}</td>
        <td>${item.dappId}</td>
        <td>${item.comment}</td>
        <td>${item.createAt?string("yyyy-MM-dd HH:mm:ss")}</td>
        <td>${item.updateAt?string("yyyy-MM-dd HH:mm:ss")}</td>
        <td><img src="https://s3-us-west-2.amazonaws.com/nebulas-dapp/a1.png"
                style="max-width:44px;"></td>
        <td>${item.title}</td>
        <td>${item.intro}</td>
        <td><input class="btn btn-default" value="删除" type="button" style ="color:red" id="btn-delete" name=${item.id?c}><i class="fa fa-delete"></i> </input></td>
        <td><input class="btn btn-default" value="更改" type="button" style ="color:red" id="btn-update" name=${item.id?c}><i class="fa fa-update"></i> </input></td>
    </tr>
</#list>
```
当循环使用的时候，每次 click 删除按钮🔘的时候根据该按钮的 id 拿到该行中的 ```${item.id?c}```，但是根据 id 拿，由于是循环，会有很多名字
名字一样的id，因为在一个 html 页面中 id是不能重复的，所以永远只能拿到第一行的值，所以改成根据 按钮的 class 拿，这样就成功了。

修改代码如下：

```html
<#list pageIterator.data as item>
    <tr class="gradeA odd" role="row" >
        <td class="id">${item.id?c}</td>
        <td>${item.dappId}</td>
        <td>${item.comment}</td>
        <td>${item.createAt?string("yyyy-MM-dd HH:mm:ss")}</td>
        <td>${item.updateAt?string("yyyy-MM-dd HH:mm:ss")}</td>
        <td><img src="https://s3-us-west-2.amazonaws.com/nebulas-dapp/a1.png"
                style="max-width:44px;"></td>
        <td>${item.title}</td>
        <td>${item.intro}</td>
        <td><input class="btn btn-default btn-delete" value="删除" type="button" style ="color:red" name=${item.id?c}><i class="fa fa-delete"></i> </input></td>
        <td><input class="btn btn-default btn-update" value="更改" type="button" style ="color:red" name=${item.id?c}><i class="fa fa-update"></i> </input></td>
    </tr>
</#list>
```


### 3. html <"input "/>关于value 空格后的内容消失的问题
![](https://github.com/Lisanaaa/Nebulas-Learn/blob/master/image/html%E7%A9%BA%E6%A0%BC%E9%97%AE%E9%A2%981.jpeg)
今天做 Dapp store 推荐后台更新 feature 的时候，开始写的代码如下，${dapp.intro} 这种内容里面会有空格，
当点击更新的时候, value 空格后的内容全部消失，错误代码如下：
```html
<tbody>
    <tr class="-comment" role="row" >
        <td>comment</td>
        <td><input value=${dapp.comment} id="comment"/></td>
    </tr>
    <tr class="-logo" role="row" >
        <td>logo</td>
        <td><input value=${dapp.logo} id="logo"/></td>
    </tr>
    <tr class="-title" role="row" >
        <td>title</td>
        <td><input value=${dapp.title} id="title"/></td>
    </tr>
    <tr class="-intro" role="row" >
        <td>introduction</td>
        <td><input value=${dapp.intro} id="intro"/></td>
    </tr>
</tbody>
```

后面查到解决方法为加上双引号，这样所有内容就会全部保留

```html
<tbody>
    <tr class="-comment" role="row" >
        <td>comment</td>
        <td><input value="${dapp.comment}" id="comment"/></td>
    </tr>
    <tr class="-logo" role="row" >
        <td>logo</td>
        <td><input value="${dapp.logo}" id="logo"/></td>
    </tr>
    <tr class="-title" role="row" >
        <td>title</td>
        <td><input value="${dapp.title}" id="title"/></td>
    </tr>
    <tr class="-intro" role="row" >
        <td>introduction</td>
        <td><input value="${dapp.intro}" id="intro"/></td>
    </tr>
</tbody>
```
现在可以了：
![](https://github.com/Lisanaaa/Nebulas-Learn/blob/master/image/html%E7%A9%BA%E6%A0%BC%E9%97%AE%E9%A2%982.jpeg)

