---
title: thinkphp入门基础
tags:
  - php
url: 73.html
id: 73
comments: false
categories:
  - 后端
date: 2016-11-09 15:46:39
---

### URL

### 配置URL伪静态

3.1版本默认支持.html,.shtml,.xml,.pdf伪静态  
在配置文件中配置\[是在项目下的配置文件./App/Conf/config.php\]

    'URL_HTML_SUFFIX'=>'html|shmtl|xml' // 多个用 | 分割
    

### 配置URL路由

基本规则

> 一、规则路由  
> 格式1：'路由规则'=>'\[分组/模块/操作\]?额外参数1=值1&额外参数2=值2...'  
> 格式2：'路由规则'=>array('\[分组/模块/操作\]','额外参数1=值1&额外参数2=值2...')  
> 格式3：'路由规则'=>'外部地址'  
> 格式4：'路由规则'=>array('外部地址','重定向代码')  
> 注意事项：  
> 路由规则中如果以“:”开头，表示动态变量，否则为静态地址  
> 格式2的额外参数可以传入数组或者字符串  
> 外部地址中如果要引用动态变量， 采用 :1、:2 的方式  
> 路由规则支持变量的数字约束定义，例如：'news/:id\\d'=>'News/read'  
> 规则路由可以支持 全动态和动静结合定义，例如':user/blog/:id'=>'Home/Blog/user'  
> 路由规则非数字变量支持排除，例如 'news/:cate^add|edit|delete'=>'News/category'  
> 路由规则中的静态地址部分不区分大小写

配置文件开启路由

    'URL_ROUTER_ON'   => true, //开启路由
    'URL_ROUTE_RULES' => array( //定义路由规则
        'news/:year/:month/:day' => array('News/archive', 'status=1'),
        'news/:id'               => 'News/read',
        'news/read/:id'          => '/news/:1',
     ),
    

> `http://serverName/index.php/news/2012/01/08`匹配第一条路由  
> 也可以写成`'news/:year/:month/:day/'=>'News/archive?status=1',` `http://serverName/index.php/news/8`匹配第二条路由 第三条用于url地址迁移，重定向使用

### 正则路由

> 正则路由可以实现更加复杂的路由定义，支持的定义格式如下：  
> 格式1：'路由正则'=>'\[分组/模块/操作\]?参数1=值1&参数2=值2...'  
> 格式2：'路由正则'=>array('\[分组/模块/操作\]','参数1=值1&参数2=值2...')  
> 格式3：'路由正则'=>'外部地址'  
> 格式4：'路由正则'=>array('外部地址','重定向代码')

注意事项：  
正则路由规则必须以“/”开始和结束  
格式2的参数可以传入数组或者字符串  
参数值和外部地址中可以用动态变量 采用 :1、 :2 的方式  
下面是正则路由的定义示例：

    'URL_ROUTER_ON'   => true, //开启路由
     'URL_ROUTE_RULES' => array( //定义路由规则
        '/^blog\/(\d+)$/'        => 'Blog/read?id=:1',
        '/^blog\/(\d+)\/(\d+)$/' => 'Blog/achive?year=:1&month=:2',
        '/^blog\/(\d+)_(\d+)$/'  => 'blog.php?id=:1&page=:2',
     ),
     ```   
    ### URL区分大小写
    

'URL\_CASE\_INSENSITIVE' =>true, // 不区分 'URL\_CASE\_INSENSITIVE' =>false, // 区分

    ### 缓存
    ### 静态缓存
    开启静态缓存[是在分组下的配置文件./App/Moduels/Index/Conf/config.php]  
    和静态缓存相关的配置参数包括：  
    HTML_CACHE_ON 是否开启静态缓存功能  
    HTML_FILE_SUFFIX 静态文件后缀 惯例配置的值是 .html  
    HTML_CACHE_TIME 默认的静态缓存有效期 默认60秒 可以在静态规则定义覆盖  
    

'HTML\_CACHE\_ON'=>true, 'HTML\_CACHE\_RULES'=> array( 'ActionName' => array('静态规则', '静态缓存有效期', '附加规则'), 'ModuleName(小写)' => array('静态规则', '静态缓存有效期', '附加规则'), 'ModuleName(小写):ActionName' => array('静态规则', '静态缓存有效期', '附加规则'), '*' => array('静态规则', '静态缓存有效期', '附加规则'), 'Show:index' => array('{:module}_{:action}_{id}', 100),

    所有控制器下read方法缓存，其中，{id} 表示取$_GET['id'] 为静态缓存文件名，第二个参数表示缓存60秒
    

'read'=>array('{id}','60')

    User控制器下所有方法缓存，其中，{:action} 表示当前的操作名称
    

'user:'=>array('User/{:action}_{id}','600')

    Blog控制器下read方法缓存
    

'blog:read'=>array('{id}',0)

    全局缓存
    

'*'=>array('{$\_SERVER.REQUEST\_URI|md5}'),

    静态规则  
    1.  使用系统变量 包括 _GET _REQUEST _SERVER _SESSION _COOKIE
    > {$_×××|function}
    
    2.  使用框架特定的变量
    > {:app}、{:group} 、{:module} 和{:action} 分别表示当前项目名、分组名、模块名和操作名
    
    3.  使用_GET变量
    > {var|function}    也就是说 {id} 其实等效于 {$_GET.id}
    
    4.  直接使用函数
    > {|function}   例如：{|time}
    
    5.  支持混合定义
    > '{id},{name|md5}'   
    在{}之外的字符作为字符串对待，如果包含有”/”，会自动创建目录。  
    例如，定义下面的静态规则：  
    {:module}/{:action}_{id}  
    则会在静态目录下面创建模块名称的子目录，然后写入操作名_id.shtml 文件。    
    
    
    
    
    使用get中文参数乱码问题
    ### 原因
    中文浏览器中默认使用的是gbk2312编码
    
    ### 解决方法
    

/\*\* \* 解决get参数中带中文的乱码问题 * @param $keywords 传入的关键字 * @return string */ function decodeToUtf($keywords) { $keywords = mb\_convert\_encoding($keywords, 'UTF-8','GB2312,UTF-8'); return $keywords; }

    

$keywords = I('keywords'); $keywords = decodeToUtf($keywords); $page = new Page($count,self::$perPage); $page->parameter = 'keywords=' . $keywords; ```