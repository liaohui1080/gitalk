title: hexo学习日志
category: hexo
date: [YYYY-MM-DD]
tag: [hexo, 学习, blog]
---
主要记录hexo配置信息

<!--more-->
---
多tag配置
```
category: hexo学习  #增加分类
date: [YYYY-MM-DD]
tag: [hexo, 学习, blog] #增加tag
---
这里显示简介
<!--more-->
这里显示主要内容
```

自定义的命令
```json
"build": "hexo generate",
"clean": "hexo clean",
"copyImages": "node copy.js",   #拷贝图片到public文件夹，这样在服务器上才能显示图片
"deploy": "npm run build && npm run copyImages && hexo deploy", #部署到sftp服务器上，先编译 再拷贝图片，最后上传
"server": "npm run copyImages && hexo server"

```

拷贝图片到public代码

```js
var fs = require("fs");

/*
 * 复制目录、子目录，及其中的文件
 * @param src {String} 要复制的目录
 * @param dist {String} 复制到目标目录
 */
function copyDir(src, dist, callback) {
  fs.access(dist, function(err) {
    if (err) {
      // 目录不存在时创建目录
      fs.mkdirSync(dist);
    }
    _copy(null, src, dist);
  });

  function _copy(err, src, dist) {
    if (err) {
      callback(err);
    } else {
      fs.readdir(src, function(err, paths) {
        if (err) {
          callback(err);
        } else {
          paths.forEach(function(path) {
            var _src = src + "/" + path;
            var _dist = dist + "/" + path;
            fs.stat(_src, function(err, stat) {
              if (err) {
                callback(err);
              } else {
                // 判断是文件还是目录
                if (stat.isFile()) {
                  fs.writeFileSync(_dist, fs.readFileSync(_src));
                } else if (stat.isDirectory()) {
                  // 当是目录是，递归复制
                  copyDir(_src, _dist, callback);
                }
              }
            });
          });
        }
      });
    }
  }
}

copyDir(
  "这里是实际的路径/images",
  "这里是实际的路径/public/images",
  function(err) {
    if (err) {
      console.log(err);
    } else {
      console.log("sss");
    }
  }
);

```