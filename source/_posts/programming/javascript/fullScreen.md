---
title: 页面元素的全屏
date: 2022-06-17 08:23:10
tags: js
categories: js
---
# 页面元素的全屏

```js
//1、全屏

function fullScreen(ele){
     const target = document.getElementById(ele);
     if(target.RequestFullScreen){
         target.RequestFullScreen();
      }
      //兼容火狐
      if(target.mozRequestFullScreen){
        target.mozRequestFullScreen();
      }
      //兼容谷歌等可以webkitRequestFullScreen也可以webkitRequestFullscreen
      if(target.webkitRequestFullScreen){
        target.webkitRequestFullScreen();
      }
      //兼容IE,只能写msRequestFullscreen
      if(target.msRequestFullscreen){
        target.msRequestFullscreen();
      }
}

//2、退出全屏
function exitFullScreen(){
     const target = document;
     if(target.exitFullScreen){
        target.exitFullscreen()
      }
      //兼容火狐
      if(target.mozCancelFullScreen){
        target.mozCancelFullScreen()
      }
      //兼容谷歌等
      if(target.webkitExitFullscreen){
        target.webkitExitFullscreen()
      }
      //兼容IE
      if(target.msExitFullscreen){
        target.msExitFullscreen()
      }
}
```

<!--more-->

```js
;(function() {
    'use strict';
    var document = typeof window !== 'undefined' && typeof window.document !== 'undefined' ? window.document : {};
    var fn = (function () {
        var val;

        var fnMap = [
            [
                'requestFullscreen',
                'exitFullscreen',
                'fullscreenElement',
                'fullscreenEnabled',
                'fullscreenchange',
                'fullscreenerror'
            ],
            // New WebKit
            [
                'webkitRequestFullscreen',
                'webkitExitFullscreen',
                'webkitFullscreenElement',
                'webkitFullscreenEnabled',
                'webkitfullscreenchange',
                'webkitfullscreenerror'

            ],
            // Old WebKit
            [
                'webkitRequestFullScreen',
                'webkitCancelFullScreen',
                'webkitCurrentFullScreenElement',
                'webkitCancelFullScreen',
                'webkitfullscreenchange',
                'webkitfullscreenerror'

            ],
            [
                'mozRequestFullScreen',
                'mozCancelFullScreen',
                'mozFullScreenElement',
                'mozFullScreenEnabled',
                'mozfullscreenchange',
                'mozfullscreenerror'
            ],
            [
                'msRequestFullscreen',
                'msExitFullscreen',
                'msFullscreenElement',
                'msFullscreenEnabled',
                'MSFullscreenChange',
                'MSFullscreenError'
            ]
        ];

        var i = 0;
        var l = fnMap.length;
        var ret = {};

        for (; i < l; i++) {
            val = fnMap[i];
            if (val && val[1] in document) {
                for (i = 0; i < val.length; i++) {
                    ret[fnMap[0][i]] = val[i];
                }
                return ret;
            }
        }

        return false;
    })();

    var screenfull = {
        request: function (element, options) {
            return new Promise(function (resolve, reject) {
                element = element || document.documentElement;

                var returnPromise = element[fn.requestFullscreen](options);;

                if (returnPromise instanceof Promise) {
                    returnPromise.then(resolve).catch(reject);
                } else {
                    reject();
                }
            }.bind(this));
        },
        exit: function () {
            return new Promise(function (resolve, reject) {
                if (!this.isFullscreen) {
                    resolve();
                    return;
                }

                var returnPromise = document[fn.exitFullscreen]();

                if (returnPromise instanceof Promise) {
                    returnPromise.then(resolve).catch(reject);
                } else {
                    reject();
                }
            }.bind(this));
        },
        toggle: function (element, options) {
            return this.isFullscreen ? this.exit() : this.request(element, options);
        },
        raw: fn
    };

    Object.defineProperties(screenfull, {
        isFullscreen: {
            get: function () {
                return Boolean(document[fn.fullscreenElement]);
            }
        },
        element: {
            enumerable: true,
            get: function () {
                return document[fn.fullscreenElement];
            }
        },
        isEnabled: {
            enumerable: true,
            get: function () {
                return Boolean(document[fn.fullscreenEnabled]);
            }
        }
    });

    window.screenfull = screenfull;
})();
```

