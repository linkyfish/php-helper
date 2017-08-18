jjonline/php-helper
===================

[![Latest Stable Version](https://poser.pugx.org/jjonline/php-helper/v/stable)](https://packagist.org/packages/jjonline/php-helper)
[![License](https://poser.pugx.org/jjonline/php-helper/license)](https://packagist.org/packages/jjonline/php-helper)
[![Build Status](https://travis-ci.org/jjonline/php-helper.svg?branch=master)](https://travis-ci.org/jjonline/php-helper)

jjonline/php-helper是日常开发过程中积累提炼而来，收集整理了基于静态类的多个常用函数方法和通用的对象类，PHP版本要求至少5.4，基于命名空间，适配composer，方便composer一键安装和管理。

## 安装

1、composer安装

`composer require jjonline/php-helper`


2、直接引用自动加载文件

`require_once './autoload.php';`

> 强烈建议使用composer进行包管理和安装


## 文档

快速示例：
~~~
use jjonline\helper\FilterValid;
use jjonline\helper\Tools;
use jjonline\library\Http;

// 表单效验
$isMailValid     = FilterValid::is_mail_valid('jjonline@jjonline.cn');
$isPhoneValid    = FilterValid::is_phone_valid('13800138000');

// ...

// 通用方法
$isMobileBrowser = Tools::is_mobile_browser();
$status          = Tools::rm_dir('./test');

// ...

// 通用http操纵类Post方法
$Http            = Http::init();
$excuteStatus    = $Http->setData('postField','postVallue')
                        ->setTimeOut(10)
                        ->setReferer('http://blog.jjonline.cn')
                        ->setUserAgent('Mozilla/5.0')
                        ->setRequestHeader('X-Powered-By','JJonline.Cn')
                        ->setRequestCookie('cookieName','cookieValue')
                        ->post('http://blog.jjonline.cn');
if($excuteStatus)
{
    $Http->save('/var/www/index.html');
    echo $Http->getBody();
}

// 通用http操纵类Get方法
$excuteStatus = $Http->reset()->get('http://blog.jjonline.cn');
if($excuteStatus)
{
    $Http->save('/var/www/index1.html');
    print_r($Http->getResponseCookie());
}
~~~

jjonline/php-helper包含两部分：

* 静态类函数方法：`src/function`目录下
* 对象类：`src/library`目录下

----

### FilterValid 表单验证静态类

引入命名空间：`use jjonline\helper\FilterValid`

+ **`boolean FilterValid::is_mail_valid(string $str)`**

  检测传入的字符串是否符合邮箱格式

+ **`boolean FilterValid::is_phone_valid(mixed $str|number)`**

  检测传入的字符串或数字是否符合天朝手机号格式

+ **`boolean FilterValid::is_url_valid(string $url)`**

  检测传入的字符串是否为http或http打头的网址，可包含端口号、get参数和锚点

+ **`boolean FilterValid::is_uid_valid(mixd $uid[[,$min_len = 4],$max_len = 11])`**

  检测传入的数字是否为一个数字ID，第二个可选参数指定最小长度默认值4；第三个可选参数指定最大长度默认值11

+ **`boolean FilterValid::is_password_valid(string $pwd[,$min_len = 6,$max_len = 18])`**

  检测传入的字符串是否为同时包含数字和字母的合法密码，第二个可选参数指定最小长度默认值4；第三个可选参数指定最大长度默认值18

+ **`boolean FilterValid::is_chinese_valid(string $str)`**

  检测传入的字符串是否全部为中文汉字

+ **`boolean FilterValid::is_utf8_valid(string $str)`**

  检测传入的字符串是否utf8编码

+ **`mixd[false|array] FilterValid::is_citizen_id_valid(string $citizen_id)`**

  检测传入的字符串是否为合乎编码规范的天朝身份证号，兼容16位和18位，合乎规范返回数组从身份证号提取信息，否则返回false

### Tools 常用工具方法静态类

引入命名空间：`use jjonline\helper\Tools`

+ **`boolean Tools::is_weixin_browser([string $userAgent = null])`**

  检测当前浏览器环境是否为微信浏览器，或者传入浏览器userAgent字符串检测是否为微信浏览器，参数可选

+ **`boolean Tools::is_mobile_browser()`**

  检测当前浏览器环境是否为移动端浏览器，当然微信浏览器也会返回true

+ **`string Tools::hide_name(string $userName)`**

  用户名脱敏处理，字符长度小于3返回3个\*，长度大于3则返回首尾各1个字符以及中间的3个\*号字符

+ **`string Tools::hide_ipv4(string $ip)`**

  IPv4地址脱敏处理，返回IPv4中间两位脱敏后的字符，例如：223.**.**.5

+ **`string Tools::nl2p(string $str)`**

  nl2br的类似函数，只不过是将换行符`\n`、`\r`或`\n\r`转换为成对的`p`标签

+ **`string Tools::time_ago(mixed $unixTimeStamp)`**

  将Unix时间戳转换为`xx前`这种模糊表示法，例如：`刚刚`、`1分钟前`、`2小时前`、`3周前`、`1个月前(07-11)`、`3年前(2014-12-02)` 等

+ **`boolean Tools::is_ssl()`**

  检测当前请求是否为https请求

+ **`mixed Tools::get_client_ip([$isLong = 0],[$isAdv = false])`**

  获取客户端的IPv4地址，第一个**可选参数**1和true返回IPv4数字表示法，0和false返回IPv4字符串，默认值0；第二个**可选参数**是否检测http代理情况，false不检测，true检测，默认值false

+ **`void Tools::redirect(string $url[[,int $time = 0],$text = null])`**

  网页重定向，也就是网页跳转啦！第一参数跳转的url，第二个可选参数跳转等待时间，第三个可选参数跳转等待时显示的文本

+ **`string|null Tools::dump(string $str[,$echo=true[, $label=null[, $strict=true]]])`**

  开发时浏览器友好的调试输出任意变量，来源于ThinkPHP
    >第二个**可选参数**为false将返回处理好的字符串，为true直接输出没有返回值，默认值为true;
    >第三个**可选参数**指定本次调试的标签，也就是在输出或返回值前加上这个标签值，便于开发调试，默认值null；
    >第四个**可选参数**指定本次输出是否严格模式，也就是在输出或返回值的时候是否将html标签转义，默认值true；

+ **`string Tools::transfer_encrypt(string $str[, $key = 'jjonline'[, $expiry = 0]])`**

  来源于discuz的可逆加解密算法，用于有有效期效验的数据交换，加密强度很弱，请甄别使用场景
    >第二个可选参数：本次加密的秘钥，默认加了一个秘钥`jjonline`
    >第三个可选参数：本次加密的有效时间，单位秒，在该参数指定的时间内解密有效
    >其实超过该时间有秘钥也是能解密出明文的，默认值0表示永不失效

+ **string Tools::transfer_decrypt(string $str[, $key = 'jjonline'])`**

  将transfer_encrypt加密的值解密，并效验有效期，成功返回原串，失败或失效返回空字符串
    >第一个参数：待解密的密文
    >第二个参数：解密的秘钥
    >返回值：已失效或解密失败返回空值，解密成功返回明文；警告：过了有效期是可以解出明文的！！！

+ **`string Tools::mbsubstr(string $str [,$start = 0[, $length[, $charset = "utf-8"]]])`**

  返回参数指定的参数截取字符串，参数与原生函数`mb_substr`一致，这是一个兼容处理函数

+ **`Tools::get_rand_string([$len = 6[, $type = ''[, $addChars = '']]])`**

  获取随机字符串，来源于ThinkPHP
    >第一个**可选参数**指定返回字符串长度，默认值6
    >第二个**可选参数**指定返回字符串类型，取值空字符串、0字母、1数字、2大写字母、3小写字母、4中文，默认值空表示返回大小写字母和数字混合的随机字符串，0表示返回大小写字母混合的字符串，以此类推
    >第三个**可选参数**指定附加的用于返回值随机字符串的字符元素，函数中的数字、大小写字母去除了易混淆的字母oOLl和数字0和1，若不需要去除通过第三个参数附加进去即可，默认值空字符串

+ **`string  Tools::clear_js_code(string $html)`**

  去除字符串中的js代码，包括大段的由<script>标签包裹的代码和各标签属性中on开头的事件属性

+ **`string to_absolute_url(string $sUrl,string $baseUrl)`**

  将当前网页中的相对超链接转换为带域名的完整超链接

+ **`boolean Tools::rm_dir(string $dir)`**

  删除目录以及目录下的所有文件，原生函数`rmdir`只能删除非空目录

----

### Http 网络请求对象类

引入命名空间：`jjonline\library`

#### 初始化

+ **`HttpObject Http::init()`**

  初始化Http单例类，返回对象，例如：`$http = Http::init()`

#### 设置方法

+ **`HttpObject $http->setUrl(string $url)`**

  设置请求的网址url

+ **`$http->setTimeOut(30)`**

  设置请求连接上之后的超时时间

+ **`$http->setRequestHeader($key[,$value = null])`**

  设置自定义请求头信息，参数比较灵活
  ~~~
  第一种传参方式：第一个参数为header头的名称部分，第二个参数为header头的值部分
  $http->setRequestHeader('X-Powered-By','PHP/7.0.22')
  第二种传参方式：仅第一个参数二维数组，一次可设置多个header头项目
  $http->setRequestHeader([['X-Powered-By','PHP/7.0.22'],['X-User-By','JJonline']])
  第三种传参方式：仅第一个参数字符串，完整的header头
  $http->setRequestHeader('X-Powered-By: PHP/7.0.22')
  ~~~


#### 获取方法