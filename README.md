## 获取收趣收集链接

>由于本人一直使用收趣云书签来存储自己稍后阅读（不读）的文章博客（拖延症太重了），但是最近发现收趣已经好久没有更新了，所以需要导出存储的站点数据，以备后边万一访问不了了，但由于收趣的导出是收费功能，所以自己就写了小脚本，自动导出数据。

方法如下：

### 打开[收趣](http://shouqu.me/my.html)

### 执行导出数据

有两种方法

 #### jQuery 控制台输出
 
  此方法是是页面滑倒最底端（获取到全部的标签），然后 复制下边代码到 页面的控制台，回车执行， 把数据以json形式打印到控制台，然后复制到本地文件中格式化即可

``` javascript
var list = $('.cardFixedHeight');

var arr = [];

list.each(function() {
  var tag = $(this).find('div[categoryids]').text();
  var dataTime = $(this).find('.date').text();
  var alink = $(this).find('.bookmarkLink').attr('href');
  var name = $(this).find('.bookmarkTitle').text();
  var introduct = $(this).find('.introduct').text();
  var bookmarkView = $(this).find('.bookmarkView').html();
  arr.push({
    tag: tag,
    dataTime: dataTime,
    alink: alink,
    name: name,
        introduct: introduct,
        bookmarkView: bookmarkView
  })
})
console.log(arr);
```
#### 直接输出文件
此方法比较傻瓜，直接打开页面登录用户，然后打开控制台，复制下边代码，  
页面会自动滑动到最底端（拉取全部数据）， 然后会自动执行输出一个json文件，打开文件格式化内容即可。  
也可以直接设置格式化处理 `JSON.stringify(getData(), null, '\t'))`, 那么导出的文件无需格式化，文件内存也会相应大一些
``` javascript
  scroll();
  // 获取总数
  var total = userInfo.markCount;
  var times = null;
  var nowDate = getFormatDate()

  function scroll() {
    console.log('key', window.arrKey, total);
    bodys = document.body.scrollHeight + 100;
    if (window.arrKey == total) {
      clearTimeout(times);
      downloadUserData()
    } else {
      times = window.scrollTo(0, bodys);
      setTimeout(() => {
        scroll();
      }, 30)
    }
  }
 
  // downloadUserData()
  function downloadUserData() {
    exportRaw(`收趣书签-${nowDate}.json`, JSON.stringify(getData(), null, '\t'));
  }

  function fakeClick(obj) {
    let ev = document.createEvent('MouseEvents');
    ev.initMouseEvent('click', true, false, window, 0, 0, 0, 0, 0, false, false, false, 0, null);
    obj.dispatchEvent(ev);
  }

  function exportRaw(name, data) {
    let urlObject = window.URL || window.webkitUrl || window;
    let export_blob = new Blob([data]);
    let save_link = document.createElementNS('http://www.w3.org/1999/xhtml', 'a');
    save_link.href = urlObject.createObjectURL(export_blob);
    save_link.download = name;
    fakeClick(save_link);
  }

  function getData() {
    var list = document.querySelectorAll('.cardFixedHeight');

    var arr = [];
    Array.prototype.slice.call(list).forEach(ele => {
      let item = ele.innerHTML;
      let tag = ele.querySelector('div[categoryids]').innerText;
      let dataTime = ele.querySelector('.date').innerText;
      let alink = ele.querySelector('.bookmarkLink').getAttribute('href');
      let name = ele.querySelector('.bookmarkTitle').innerText;
      let bookmarkView = ele.querySelector('.bookmarkView').innerHTML;
      arr.push({
        tag,
        dataTime,
        alink,
        name,
        bookmarkView
      })
    })
    return arr;
  }

  function getFormatDate() {
    /**  
      * 日期格式化（原型扩展或重载）  
      * 格式 YYYY/yyyy/ 表示年份  
      * MM/M 月份  
      * dd/DD/d/D 日期  
      * @param {formatStr} 格式模版  
      * @type string  
      * @returns 日期字符串  
      */  
    Date.prototype.format = function(formatStr){   
            var str = formatStr;   
            var Week = ['日','一','二','三','四','五','六'];   
            str=str.replace(/yyyy|YYYY/,this.getFullYear());  
            str=str.replace(/MM/,(this.getMonth()+1)>9?(this.getMonth()+1).toString():'0' + (this.getMonth()+1));   
            str=str.replace(/dd|DD/,this.getDate()>9?this.getDate().toString():'0' + this.getDate());   
          return str;   
    } 
    return new Date().format("yyyy-MM-dd");
  } 
```

页面是每30ms向下滑动100像素，然后触发下拉加载更多，此处有可能根据网速问题会频繁执行，可以根据网速自行设置。