### html <"input "/>关于value 空格后的内容消失的问题
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

