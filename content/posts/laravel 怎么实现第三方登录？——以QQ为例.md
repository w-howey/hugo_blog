---
title: "laravel 怎么实现第三方登录？——以QQ为例"
date: 2023-07-26T22:21:16+08:00
draft: false
slug: "2307262211"
tags: ["php", "laravel", "社会化登录-qq"]
series: ["编程系列"]
categories: ["后端"]
---

## laravel 怎么实现第三方登录？——以 QQ 为例

1. 例如我们需要 qq 的第三方登录支持，首先需要升级 sociaite，那么我们就需要安装一个 composer 包，打开 laragon 终端。执行安装命令。
   `composer require socialiteproviders/qq   //后面的为key可以换成weibo等`
2. 跑完命令之后我们需要加入事件监听器：在`app/Providers/EventServiceProvide.php`中`protected $listen=[];`中加入：

```protected $listen = [
        Registered::class => [
            SendEmailVerificationNotification::class,
        ],
        'SocialiteProviders\Manager\SocialiteWasCalled' => [
            'SocialiteProviders\QQ\QqExtendSocialite@handle',
        ],
    ];
```

3. 去`.env`里面配置一下`key`和`secret`

```
QQ_KEY=your key
QQ_SECRET=your secret
QQ_REDIRECT_URI=http://your-callback-url,
```

4. 去`config/services.php`中加入

```'qq' => [
    'client_id' => env('QQ_KEY'),         // Your QQ Client ID
    'client_secret' => env('QQ_SECRET'), // Your QQ Client Secret
    'redirect' =>env('QQ_REDIRECT_URI'),  //这个地址很重要
],
```

5. 设计路由：在`web.php`当中,思考：我们需要定义两个路由，一个是跳转到 qq 登录给予权限的页面，和 qq 回退过来之后的页面,路由设计如下：

```
    Route::namespace('Auth')->prefix('auth/qq')->group(function () {
    Route::get('/', 'SocialitesController@qq');
    Route::get('callback', 'SocialitesController@callback');
});
```

前缀和命名空间可以随意更改,打印路由终端命令：`php artisan r:l`

6. 在`login.blade.php`当中加入 QQ 登录按钮，给一个`a`便签加入上面设计的路由地址：`<a href="/auth/qq></a>"`
7. 新建第三方登录管理控制器，终端命令：`php artisan make:controller Auth\SocialitesController`
8. 打开控制器输入一下代码：

```
<?php

namespace App\Http\Controllers\Auth;

use Illuminate\Http\Request;
use App\Http\Controllers\Controller;
use Socialite;
use App\User;
use Illuminate\Support\Str;

class SocialitesController extends Controller
{
    public function qq()
    {
        return Socialite::with('qq')->redirect();
    }

    //用户授权后，跳转回来
    public function callback()
    {
        $message= Socialite::driver('qq')->user();
        dump($message);exit;
    }
}
```

查看见 qq 返回过来的信息。
会得到如下信息。

```
<pre class="sf-dump" id="sf-dump-616717864" data-indent-pad="  " tabindex="0" style="display: block; white-space: pre-wrap; padding: 5px; overflow: initial !important; background-color: rgb(24, 23, 27); color: rgb(255, 132, 0); font: 400 12px Menlo, Monaco, Consolas, monospace; overflow-wrap: break-word; position: relative; z-index: 99999; word-break: break-all; letter-spacing: normal; orphans: 2; text-align: start; text-indent: 0px; text-transform: none; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; text-decoration-style: initial; text-decoration-color: initial;"><abbr title="SocialiteProviders\Manager\OAuth2\User" class="sf-dump-note" style="text-decoration: none; border: none; cursor: pointer; color: rgb(18, 153, 218);">User</abbr> {#361 ▼ <samp data-depth="1" class="sf-dump-expanded">+accessTokenResponseBody: array:3 [▶]
  +token: "2B2A83C5B937C1139B03BBBBB7F2DAB6"
  +refreshToken: "B553B05ECBC7981B8AE18C5E85117A68"
  +expiresIn: "7776000"
  +id: "4BB34E789377567E0323577F5233D7F2"
  +nickname: "****"
  +name: null
  +email: null
  +avatar: "http://thirdqq.qlogo.cn/g?b=oidb&k=tHUTU6J82j7LSnmjPUKUAw&s=100&t=1560692607"
  +user: array:21 [▼ <samp data-depth="2" class="sf-dump-expanded">"ret" => 0
    "msg" => ""
    "is_lost" => 0
    "nickname" => "***"
    "gender" => "***"
    "province" => "***"
    "city" => "***"
    "year" => "****"
    "constellation" => ""
    "figureurl" => "http://qzapp.qlogo.cn/qzapp/"
    "figureurl_1" => "http://qzapp.qlogo.cn/qzapp/"
    "figureurl_2" => "http://qzapp.qlogo.cn/qzapp/"
    "figureurl_qq_1" => "http://thirdqq.qlogo.cn"
    "figureurl_qq_2" => "http://thirdqq.qlogo.cn"
    "figureurl_qq" => "http://thirdqq.qlogo.cn"
    "figureurl_type" => "1"
    "is_yellow_vip" => "0"
    "vip" => "0"
    "yellow_vip_level" => "0"
    "level" => "0"
    "is_yellow_year_vip" => "0"</samp> ]
  +"unionid": ""</samp> }</pre>
```

这些就是 QQ 给你返回的用户信息
我们需要把这些得到的信息选取有用字段插入到我们的 users 表中。

9. 检查模型白名单：

```
protected $fillable = ['填写要插入的字段名'];
```

10. 将`dump`屏蔽改为以下代码

```
 $user = User::where('provider', 'qq')->where('uid', $info->id)->first();
        //这里是判断user是否存在，如果存在就直接登录了
        //不存在直接执行插入
        if (!$user) {
        //直接插入数据库的对应字段，注意写白名单
            $user = User::create([
                'username'=>$message->nickname,
                'provider' => 'qq',   //写死了，这里随意
                'uid' => $message->id,
                'email' => 'qq+' . $message->id . '@qq.com',    //这里拼的是个假的邮箱地址防止报错，但要符合邮箱格式，做了验证也需要唯一
                'password' => bcrypt(Str::random(10)),   //这里也是设置了一个假的密码，随机生成一个10位的密码，防止报错
                'name' => $message->nickname,
                'avatar' => $message->avatar,
            ]);
        }

        //Auth::login($user);
        Auth::login($user, true);
        return redirect('/admin'); //这里是个调转 登录完后跳转到后台首页
```

11. 以上搞完基本 OK、
    ` 'redirect' =>env('QQ_REDIRECT_URI'),  //这个地址很重要`
    这个地址需要和你申请 qq 互联应用的那个回调地址对应，并且访问也需要与前面的地址对应，不然会报`no message`的错误
