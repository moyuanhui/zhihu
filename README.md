# 测试账号信息
博客地址：[https://www.cnblogs.com/moyhui](https://www.cnblogs.com/moyhui)

页面效果：![](http://upload-images.jianshu.io/upload_images/8030866-b3a29ab6725ff47e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

封装http请求：
```
import Promise from "../plugin/promise"

let wxPromisify = fn => {
    return function (obj = {}) {
        return new Promise((resolve, reject) => {
            obj.success = function (res) {
                resolve(res)
            }
            obj.fail = function (res) {
                reject(res)
            }
           
            fn(obj)
        })
    }
}
//无论promise对象最后状态如何都会执行
Promise.prototype.finally = callback => {
    let P = this.constructor;
    return this.then(
        value => P.resolve(callback()).then(() => value),
        reason => P.resolve(callback()).then(() => { throw reason })
    );
}

/**
 * 微信请求get方法
 * url
 * data 以对象的格式传入
 */
let getRequest = (url, data) => {
    var getRequest = wxPromisify(wx.request)
    return getRequest({
        url: url,
        method: 'GET',
        data: data,
        header: {
            'Content-Type': 'application/json;charset=UTF-8',
            'Cookies':"API-ANTH-SESSION=12323"
        }
    })
}

/**
 * 微信请求post方法封装
 * url
 * data 以对象的格式传入
 */
let postRequest = (url, data) => {
    var postRequest = wxPromisify(wx.request)
    return postRequest({
        url: url,
        method: 'POST',
        data: data,
        header: {
            "content-type": "application/x-www-form-urlencoded"
        }
    })
}

module.exports = {
    postRequest: postRequest,
    getRequest: getRequest
}
```
封装wx请求：
```
import Promise from "../plugin/promise.js"

let wxPromisify = (fn) => {
    return function (obj = {}) {
        return new Promise((resolve, reject) => {
            obj.success = function (res) {
                resolve(res)
            }
            obj.fail = function (res) {
                reject(res)
            }
            fn(obj)
        })
    }
}
//无论promise对象最后状态如何都会执行
Promise.prototype.finally = (callback) => {
    let P = this.constructor;
    return this.then(
        value => P.resolve(callback()).then(() => value),
        reason => P.resolve(callback()).then(() => { throw reason })
    );
};
/**
 * 微信用户登录,获取code
 */
let wxLogin = () => {
    return wxPromisify(wx.login)
}
//获取授权信息
let wxSetting = () => {
    return wxPromisify(wx.getSetting)
}

/**
 * 获取微信用户信息
 * 注意:须在登录之后调用
 */
let wxGetUserInfo = (withCredentials) => {
    var wxGetUserInfo = wxPromisify(wx.getUserInfo)
    return wxGetUserInfo({
        withCredentials: withCredentials
    })
}

let wxAuthorize = (scope)=>{
    var wxAuthorize = wxPromisify(wx.authorize)
    return wxAuthorize({
        scope:scope
    })
}


/**
 * 获取系统信息
 */
let wxGetSystemInfo = () => {
    return wxPromisify(wx.getSystemInfo)
}

/**
 * 保留当前页面，跳转到应用内的某个页面
 * url:'../index/index'
 * params:{key:value1}
 */
let wxNavigateTo = (url, params) => {
    var wxNavigateTo = wxPromisify(wx.navigateTo)
    const serializedParams = this.paramSerializer(params)
    if (serializedParams.length > 0) {
        url += ((url.indexOf('?') == -1) ? '?' : '&') + serializedParams
    }
    return wxNavigateTo({
        url: url
    })
}
module.exports = {
    wxPromisify,
    wxLogin,
    wxSetting,
    wxAuthorize,
    wxGetUserInfo,
    wxGetSystemInfo,
    wxNavigateTo
}
```
