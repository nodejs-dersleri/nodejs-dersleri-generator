---
title: Phantomjs ile responsive ekran görüntüsü alma - 2
author: salih-sagdilek
date: 2015-11-23 23:00
template: article.jade
image : ilk-ekran-goruntusu.png
spot: true 
purpose: Phantomjs ile responsive ekran görüntüsü kaydetmek
projectCode: https://github.com/salihsagdilek/rssg
tags: ['phantomjs','resposive ekran görüntüsü', 'screenshot']
---

İlk yazımızda ekran görüntüsü almış ve kaydetmiştik, bu yazımızda ise farklı çözünürlüklerde birden çok ekran görüntüsü kaydedeceğiz, bunun yanı sıra script'imize cli'dan gelen paremetrelere farklı davranışlar sergileteceğiz.

----

### rssg.js

Dosyamızın son hali bu şekildeydi, bunun üzerinden devam edeceğiz.

```
var args = require('system').args;
var fs = require('fs');
var page = new WebPage();

if ( 1 === args.length ) {
    console.log('Url addresi girmediniz');
    phantom.exit();
}

var urlAddress = args[1].toLowerCase();

var viewports = [
    {
        width : 1200,
        height : 800
    },
    {
        width : 1024,
        height : 768
    },
    {
        width : 768,
        height : 1024
    },
    {
        width : 480,
        height : 640
    },
    {
        width : 320,
        height : 480
    }
];

page.open(urlAddress, function (status) {
    if ( 'success' !== status ) {
        console.log('Adrese bağlanılamadı.');
    } else {
        var folder = urlToDir(urlAddress);

        page.viewportSize = viewports[0];
        var output = folder + "/" + getFileName();
        console.log('Saving ' + output);
        page.render(output);
    }
    phantom.exit();
});


function urlToDir(url) {
    var dir = url
        .replace(/^(http|https):\/\//, '')
        .replace(/\/$/, '');

    if ( !fs.makeTree(dir) ) {
        console.log('"' + dir + '" oluşturulamadı.');
        phantom.exit();
    }
    console.log('"' + dir + '" oluşturuldu.');
    return dir;
}

function getFileName() {
    var d = new Date();
    var date = [
        d.getUTCFullYear(),
        d.getUTCMonth() + 1,
        d.getUTCDate()
    ];
    var time = [
        d.getHours() <= 9 ? '0' + d.getHours() : d.getHours(),
        d.getMinutes() <= 9 ? '0' + d.getMinutes() : d.getMinutes(),
        d.getSeconds() <= 9 ? '0' + d.getSeconds() : d.getSeconds(),
        d.getMilliseconds()
    ];
    return date.join('-') + '_' + time.join('-') + ".png";
}
```

---
