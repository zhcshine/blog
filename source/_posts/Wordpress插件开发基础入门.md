---
title: Wordpress插件开发基础入门
tags:
- Php
categories:
- 后端
date: 2016-11-10 11:40:48
---

## 插件
### 创建
```shell
├── hanchang-copyright
│   └── hanchang-copyright.php
```
```php
# hanchang-copyright.php
<?php
/*
Plugin Name: hanchang-copyright
Plugin URI: http://www.zhuohc.com
Description: 在页脚显示一段版权信息
Version: 1.0
Author: hanchang
Author URI: http://www.zhuohc.com
License: GPLv2
    */
```

### 插件启用和停用初始化
```php
# hanchang-copyright.php
<?php
/*
Plugin Name: hanchang-copyright
Plugin URI: http://www.zhuohc.com
Description: 在页脚显示一段版权信息
Version: 1.0
Author: hanchang
Author URI: http://www.zhuohc.com
License: GPLv2
*/

//定义插件启动时候调用的方法
register_activation_hook( __FILE__, 'hanchang_copyright_install');

function hanchang_copyright_install() {

    //插件启动，添加一个默认的版权信息
    update_option( "hanchang_copyright_text", "<p style='color:red'>本站点所有文章均为原创，转载请注明出处！</p>" );

}

//定义插件停用时候调用的方法
register_deactivation_hook( __FILE__, 'hanchang_copyright_deactivation');

function hanchang_copyright_deactivation() {

    //插件停用，设置停用标识为1
    update_option( "hanchang_copyright_deactivation", "yes" );

}
```

### 删除插件
```shell
.
├── hanchang-copyright.php
└── uninstall.php # 删除插件执行的文件
```

```php
# uninstall.php
<?
// 如果 uninstall 不是从 WordPress 调用，则退出
if( !defined( 'WP_UNINSTALL_PLUGIN' ) )
exit();

//删除插件创建的项目，以确保不占用数据库资源
delete_option( 'hanchang_copyright_deactivation' );
delete_option( 'hanchang_copyright_text' );
```


### add_action
```text
add_action("$tag钩子名称", "$function_to_do执行函数", $priorrity执行优先级, $accepted_arga带参数函数的参数个数)
```
```php
# hanchang-copyright.php
    
//为 wp_footer 钩子挂载一个新的动作 hc_copyright_insert
add_action( "wp_footer", "hc_copyright_insert" );

function hc_copyright_insert(){

    //输出一段字符串
    echo get_option( "hc_copyright_text" );

}

add_action( "wp_head", "hanchang_copyright_insert_new" );

function hanchang_copyright_insert_new() {
    echo "<script>alert(1);</script>";
}

add_action( 'save_post', 'save_post_meta', 10, 2 );

function save_post_meta( $post_id, $post ) {

    update_post_meta( $post_id, "save-time", "更新时间：" . date("Y-m-d H:i:s") );

}

```
    
### 常见钩子
* init 加载初始化
* wp_head 加载添加到head标签中的内容
* wp_enqueue_scripts 加载css或者js文件
* wp_foot 加载添加到底部内容

* save_post 添加保存文章
* wp_trash_post 将文章添加到回收站
* delete_post 将文章删除
* wp_insert_comment 插入评论
* user_register 用户注册

```php
# hanchang-copyright.php
<?php
/*
Plugin Name: hanchang-copyright
Plugin URI: http://www.zhuohc.com
Description: 在页脚显示一段版权信息
Version: 1.0
Author: hanchang
Author URI: http://www.zhuohc.com
License: GPLv2
*/

//定义插件启动时候调用的方法
register_activation_hook( __FILE__, 'hanchang_copyright_install');

function hanchang_copyright_install() {

    //插件启动，添加一个默认的版权信息
    update_option( "hanchang_copyright_text", "<p style='color:red'>本站点所有文章均为原创，转载请注明出处！</p>" );

}

//定义插件停用时候调用的方法
register_deactivation_hook( __FILE__, 'hanchang_copyright_deactivation');

function hanchang_copyright_deactivation() {

    //插件停用，设置停用标识为1
    update_option( "hanchang_copyright_deactivation", "yes" );

}

//为 wp_footer 钩子挂载一个新的动作 hc_copyright_insert
add_action( "wp_footer", "hc_copyright_insert" );

function hc_copyright_insert(){

    //输出一段字符串
    echo get_option( "hc_copyright_text" );

}


add_action( "wp_head", "hanchang_copyright_insert_new" );

function hanchang_copyright_insert_new() {
    echo "<script>alert(1);</script>";
}

add_action( 'save_post', 'save_post_meta', 10, 2 );

function save_post_meta( $post_id, $post ) {

    update_post_meta( $post_id, "save-time", "更新时间：" . date("Y-m-d H:i:s") );

}



//在输出内容之前，给页面管理添加摘要功能
add_action( 'init', 'hc_add_excerpts_to_pages' );

function hc_add_excerpts_to_pages() {

    //给页面管理添加摘要的功能
    add_post_type_support( 'page', array( 'excerpt' ) );
}

//wp_head钩子
add_action('wp_head','hc_wp_head');

function hc_wp_head() {

    //只有首页输出描述
    if( is_home() ){ ?>
        <meta name="description" content="<? bloginfo('description'); ?>" />
    <? }

}


//自定义引用样式表
function hc_enqueue_style() {
    wp_enqueue_style( 'core', plugins_url('css/hc_copyrighy.css', __FILE__) , false );
}

//自定义引用脚本文件
function hc_enqueue_script() {
    wp_enqueue_script( 'my-js', plugins_url('js/hc_copyrighy.js', __FILE__), false );
}

//引用文件的钩子
add_action( 'wp_enqueue_scripts', 'hc_enqueue_style', 5 );
add_action( 'wp_enqueue_scripts', 'hc_enqueue_script', 7 );

//删除所有挂载在 wp_enqueue_scripts 钩子上的方法
remove_all_actions( 'wp_enqueue_scripts', 5 );

//评论被添加的时候触发
add_action( 'wp_insert_comment', 'comment_inserted', 10, 2 );

//移除 wp_insert_comment 钩子上的 comment_inserted 方法
remove_action( 'wp_insert_comment', 'comment_inserted', 10 );

function comment_inserted($comment_id, $comment_object ) {

    //获取该评论所在文章的评论总数
    $comments_count = wp_count_comments( $comment_object->comment_post_ID );

    $commentarr = array();
    $commentarr['comment_ID'] = $comment_id;

    //修改评论的内容，在评论内容前加上 “第{$comments_count->total_comments}个评论：” 这么一段字符串
    $commentarr['comment_content'] = "第{$comments_count->total_comments}个评论：" . $comment_object->comment_content;

    wp_update_comment( $commentarr );

}


add_action( 'user_register', 'myplugin_registration_save', 10, 1 );

function myplugin_registration_save( $user_id ) {

    //将新用户的个人说明，设置为注册时间
    wp_update_user( array( 'ID' => $user_id, 'description' => "注册时间：" . date("Y-m-d H:i:s") ) );

}
?>

```
    
### 过滤器

```text
add_filter($tag过滤器名称, $function_to_add需要添加的函数, $priority优先级, $acceptedargs添加的参数数量)
```
    
```php
# hanchang-copyright.php
//一开始，我们设置一个变量
$value = "hello";

//给名为 hc_filter 的过滤器，挂载一个 hc_filter_fun 方法，传递给 hc_filter 的变量都会经过 hc_filter_fun 方法进行过滤
//add_filter( "hc_filter", "hc_filter_fun" );

//给名为 hc_filter 的过滤器，再挂载一个 hc_filter_fun_add_time 方法，传递给 hc_filter 的变量都会经过 hc_filter_fun、hc_filter_fun_add_time 两个方法进行过滤
//add_filter( "hc_filter", "hc_filter_fun_add_time" );

//对 $value 值使用名为 hc_filter 的过滤器进行过滤，这个时候，由于 hc_filter 过滤器只挂载了 hc_filter_fun 方法，因此，只使用 hc_filter_fun 方法 过滤了一次，并且返回给 $myvar 变量
//$myvar = apply_filters( "hc_filter", $value );

add_filter( "the_content", "hc_filter_fun" );
add_filter( "the_content", "hc_filter_fun_add_time" );
```
    

### 带参数的过滤器
```php
# hanchang-copyright.php
function suppress_if_blurb( $title, $id = null ) {

    if ( in_category('wpcj', $id ) ) {
        return '不显示标题';
    }

    return $title . "...";
}

//对 the_title 过滤器挂载一个 suppress_if_blurb 方法，优先级为10，传递的参数有2个
add_filter( 'the_title', 'suppress_if_blurb', 10, 2 );

```

### 常见过滤器

* the_content 文章内容 
* content_save_pre 文章保存前
* the_title 文章标题
* wp_handle_upload_prefilter 上传文件之前
    
### 创建菜单

```text
add_menu_page  # 添加菜单
add_submenu_page  # 添加子菜单
```

```php
# hanchang-copyright.php
// admin_menu钩子挂载创建菜单方法
add_action( 'admin_menu', 'hc_create_menu' );
function hc_create_menu() {

    // 创建顶级菜单
    add_menu_page(
        'Hanchang的插件首页', // 菜单title标题
        'hanchang-copyright',  // 菜单名称，显示在后台管理页面侧边栏
        'manage_options', // 浏览插件所需要的权限=管理员权限
        'hanchang_copyright' ,  // 菜单别名，不能与其他插件冲突
        'hanchang_settings_page',  // 菜单需要显示的页面，调用的哪个方法
        plugins_url( '/images/icon.png', __FILE__ )  // 调用图标
    );

    // 创建子菜单
    add_submenu_page(
        'hanchang_copyright',  // 父级菜单别名
        '关于HC的插件',  // 菜单title标题
        '关于',  //  菜单名称，显示在后台管理页面侧边栏
        'manage_options',  // 浏览插件所需要的权限=管理员权限
        'hc_copyright_about',  // 菜单别名，不能与其他插件冲突
        'hc_create_submenu_menu'  // 调用方法
    );
}

function hanchang_settings_page() {
    ?>
    <h2>插件顶级菜单</h2>
    <?
}

function hc_create_submenu_menu() {

    ?>
    <h2>子菜单</h2>
    <?
}

```
    
### 小工具
```php
#hanchang-copyright.php
// 使用 widgets_init 动作钩子来执行自定义的函数
add_action( 'widgets_init', 'hc_register_widgets' );

// 注册小工具
function hc_register_widgets() {
    register_widget( 'hc_widget_info' );
}

//使用 WP_Widget 类来创建小工具
class hc_widget_info extends WP_Widget {

    //构造函数
    public function __construct() {
        $widget_ops = array(
            'classname' => 'hc_widget_info',
            'description' => '显示作者的个人信息'
        );
        $this->WP_Widget( '显示作者个人信息', '作者的小工具', $widget_ops );
    }

    //小工具管理界面
    public function form( $instance ) {

        $defaults = array( 'title' => '作者的个人信息', 'xingming' => '作者', 'book' => '《作者》' );
        $instance = wp_parse_args( (array) $instance, $defaults );

        $title = $instance['title'];
        $xingming = $instance['xingming'];
        $book = $instance['book'];
        ?>
        <p>标题: <input class="widefat" name="<?php echo $this->get_field_name( 'title' ); ?>" type="text" value="<?php echo esc_attr( $title ); ?>" /></p>
        <p>姓名: <input class="widefat" name="<?php echo $this->get_field_name( 'xingming' ); ?> "type="text" value="<?php echo esc_attr( $xingming ); ?> " /></p>
        <p>著作: <textarea class="widefat" name=" <?php echo $this->get_field_name( 'book' ); ?> " /><?php echo esc_attr( $book ); ?></textarea> </p>
        <?php
    }

    //保存小工具设置
    public function update( $new_instance, $old_instance ) {

        $instance = $old_instance;

        $instance['title'] = strip_tags( trim( $new_instance['title'] ) );
        $instance['xingming'] = strip_tags( trim(  $new_instance['xingming'] ) );
        $instance['book'] = strip_tags( trim( $new_instance['book'] ) );
        return $instance;
    }

    //显示小工具
    public function widget( $args, $instance ) {

        extract( $args );

        $title = apply_filters( 'widget_title', $instance['title'] );
        $xingming = empty( $instance['xingming'] ) ? ' ' : $instance['xingming'];
        $book = empty( $instance['book'] ) ? ' ' : $instance['book'];

        echo '<p> 标题: ' . $title . '</p>';
        echo '<p> 姓名: ' . $xingming . '</p>';
        echo '<p> 著作: ' . $book . '</p>';
    }
}
```
    
    

### 添加元数据框
```php
# hanchang-copyright.php
/**
 * 添加一个元数据框到 post 和 page 的管理界面中
 */
function myplugin_add_meta_box() {

    $screens = array( 'post', 'page' );

    add_meta_box(
        'myplugin_sectionid',  // 元数据框的html代码中的id值
        '转载自',  // 元数据框标题
        'myplugin_meta_box_callback',  // 回调函数名称，输出元数据框中的内容
        $screens  // 元数据框需要添加在哪个分类内的（文章或者页面）
    );
}

//需要给 add_meta_boxes 钩子，挂载一个自定义的方法
add_action( 'add_meta_boxes', 'myplugin_add_meta_box' );

/**
 * 元数据框展示代码
 */
function myplugin_meta_box_callback( $post ) {

    // 添加一个验证信息，这个在保存元数据的时候用到
    wp_nonce_field( 'myplugin_save_meta_box_data', 'myplugin_meta_box_nonce' );

    /*
     * 输出元数据信息
     */
    $value = get_post_meta( $post->ID, '_zzurl', true );

    echo '<label for="myplugin_new_field">';
    _e( '本文章转载自：' );
    echo '</label> ';
    echo '<input type="text" id="_zzurl" name="_zzurl" value="' . esc_attr( $value ) . '" size="25" />';
}

function myplugin_save_meta_box_data( $post_id ) {

    //验证是否为有效信息
    if ( ! isset( $_POST['myplugin_meta_box_nonce'] ) ) {
        return;
    }

    if ( ! wp_verify_nonce( $_POST['myplugin_meta_box_nonce'], 'myplugin_save_meta_box_data' ) ) {
        return;
    }

    if ( defined( 'DOING_AUTOSAVE' ) && DOING_AUTOSAVE ) {
        return;
    }

    // Check the user's permissions.
    if ( isset( $_POST['post_type'] ) && 'page' == $_POST['post_type'] ) {

        if ( ! current_user_can( 'edit_page', $post_id ) ) {
            return;
        }

    } else {

        if ( ! current_user_can( 'edit_post', $post_id ) ) {
            return;
        }
    }

    if ( ! isset( $_POST['_zzurl'] ) ) {
        return;
    }

    $my_data = sanitize_text_field( $_POST['_zzurl'] );

    update_post_meta( $post_id, '_zzurl', $my_data );
}

//文章保存的时候，会调用 save_post 钩子，因此我们要借助这个钩子来保存元数据框内的数据
add_action( 'save_post', 'myplugin_save_meta_box_data' );
```
    
### 后台常见css样式表

```php
# copyright.php
function hanchang_settings_page() {
    ?>
    <div class="wrap">
        <h2>插件顶级菜单</h2>
        <div id="message" class="updated">设置保存成功</div>
        <div id="message" class="error">保存出现错误</div>
        <p>
            <input type="submit" name="Save" value="保存设置" />
            <input type="submit" name="Save" value="保存设置" class="button" />
            <input type="submit" name="Save" value="保存设置" class="button button-primary" />
            <input type="submit" name="Save" value="保存设置" class="button button-secondary" />
            <input type="submit" name="Save" value="保存设置" class="button button-large" />
            <input type="submit" name="Save" value="保存设置" class="button button-small" />
            <input type="submit" name="Save" value="保存设置" class="button button-hero" />
        </p>
        <p>
            <a href="#">搜索</a>
            <a href="#" class="button">搜索</a>
            <a href="#" class="button button-primary">搜索</a>
            <a href="#" class="button button-secondary">搜索</a>
            <a href="#" class="button button-large">搜索</a>
            <a href="#" class="button button-small">搜索</a>
            <a href="#" class="button button-hero">搜索</a>
        </p>

        <form method="POST" action="">
            <table class="form-table">
                <tr valign="top">
                    <th><label for="xingming">姓名：</label></th>
                    <td><input id="xingming" name="xingming" /></td>
                </tr>
                <tr valign="top">
                    <th><label for="shenfen">身份：</label></th>
                    <td>
                        <select name="shenfen">
                            <option value="在校">在校</option>
                            <option value="毕业">毕业</option>
                        </select>
                    </td>
                </tr>
                <tr valign="top">
                    <th><label for="tongyi">同意注册</label></th>
                    <td><input type="checkbox" name="tongyi" /></td>
                </tr>
                <tr valign="top">
                    <th><label for="xingbie">性别</label></th>
                    <td>
                        <input type="radio" name="xingbie" value="男" /> 男
                        <input type="radio" name="xingbie" value="女" /> 女
                    </td>
                </tr>
                <tr valign="top">
                    <th><label for="beizhu">备注</label></th>
                    <td><textarea name="beizhu"></textarea></td>
                </tr>
                <tr valign="top">
                    <td>
                        <input type="submit" name="save" value="保存" class="button-primary" />
                        <input type="submit" name="reset" value="重置" class="button-secondary" />
                    </td>
                </tr>
            </table>
        </form>

        <table class="widefat striped">
            <thead>
            <tr>
                <th>序号</th>
                <th>姓名</th>
            </tr>
            </thead>
            <tbody>
            <tr>
                <td>1</td>
                <td>黄聪</td>
            </tr>
            <tr>
                <td>2</td>
                <td>黄聪</td>
            </tr>
            <tr>
                <td>3</td>
                <td>黄聪</td>
            </tr>
            </tbody>
            <tfoot>
            <tr>
                <th>序号</th>
                <th>姓名</th>
            </tr>
            </tfoot>
        </table>

        <div class="tablenav">
            <div class="tablenav-pages">
                <span class="displaying-num">第1页，共458页</span>
                <span class="page-numbers current">1</span>
                <a href="#" class="page-numbers">2</a>
                <a href="#" class="page-numbers">3</a>
                <a href="#" class="page-numbers">4</a>
                <a href="#" class="next page-numbers">»</a>
            </div>
        </div>
    </div>
    <?
}
```
    
### 保存插件设置到数据库中
```shell
├── hc-test
│   ├── css
│   ├── hc-test.php
│   ├── images
│   └── js

```
```php
hc-test.php
    
<?php
/*
Plugin Name: hc-test
Plugin URI: http://zhuohc.com
Description: 测试插件
Version: 1.0
Author: hc
Author URI: http://zhuohc.com
License: GPLv2
*/

//设置时区为 亚洲/上海
date_default_timezone_set('Asia/Shanghai');

//定义插件启动时候调用的方法
register_activation_hook( __FILE__, 'hc_test_install');

function hc_test_install() {

   global $wpdb;

   if( $wpdb->get_var( "SHOW TABLES LIKE '{$wpdb->prefix}test'" ) != "{$wpdb->prefix}test" ) 
   {
      $sql = "CREATE TABLE IF NOT EXISTS `{$wpdb->prefix}test` (
           `id` int(11) NOT NULL auto_increment COMMENT '编号',
           `color` varchar(10) DEFAULT '' COMMENT '字体颜色',
           `size`  varchar(10) DEFAULT '' COMMENT '字体大小',
           PRIMARY KEY  (`id`)
         ) DEFAULT CHARSET=utf8 AUTO_INCREMENT=0;";
      $wpdb->query( $sql );

      $sql = "REPLACE INTO `{$wpdb->prefix}test` VALUES (1, '#FF0000','20');";
      $wpdb->query( $sql );
   }
}

add_action( 'admin_menu', 'hc_test_create_menu' );

function hc_test_create_menu() {

   //创建顶级菜单
   add_menu_page( 
      'HC-TEST', 
      'HC的插件',
      'manage_options', 
      'hc_test' ,
      'hc_settings_page',
      plugins_url( '/images/icon.png', __FILE__ )
   );
}

function hc_settings_page() {

   global $wpdb;

   //当提交了，并且验证信息正确
   if( !empty( $_POST ) && check_admin_referer( 'hc_test_nonce' ) ) {

      //更新设置
      update_option( 'hc_test_bold', $_POST['hc_test_bold'] );

      $wpdb->update( "{$wpdb->prefix}test", array( 'color' => $_POST['color'], 'size' => $_POST['size'] ), array( 'id' => 1 ) );
      ?>
      <div id="message" class="updated">
         <p><strong>保存成功！</strong></p>
      </div>
      <?
   }

   $sql = "SELECT * FROM `{$wpdb->prefix}test`";
   $row = $wpdb->get_row( $sql, ARRAY_A );

   $color = $row['color'];
   $size = $row['size'];
   ?>
   <div class="wrap">
      <h2>插件顶级菜单</h2>
      <form action="" method="post">
         <p><label for="color">字体颜色：</label><input type="text" name="color" value="<?php echo $color; ?>" /></p>
         <p><label for="size">字体大小：</label>
         <select name="size">
            <option value="12" <? selected( '12', $size ); ?>>12</option>
            <option value="14" <? selected( '14', $size ); ?>>14</option>
            <option value="16" <? selected( '16', $size ); ?>>16</option>
            <option value="18" <? selected( '18', $size ); ?>>18</option>
            <option value="20" <? selected( '20', $size ); ?>>20</option>
         </select></p>
         <p><label for="hc_test_obold">字体加粗：</label><input name="hc_test_bold" type="checkbox"  value="1" <? checked( 1, get_option( 'hc_test_bold' ) ); ?> /> 加粗</p>
         <p><input type="submit" name="submit" value="保存设置" /></p>
         <?
            //输出一个验证信息
            wp_nonce_field('hc_test_nonce');
         ?>
      </form>
   </div>
   <?
}

add_action( 'wp_head', 'hc_test_head_fun' );

function hc_test_head_fun() {

   global $wpdb;

   //获取自定义数据库中的设置
   $sql = "SELECT * FROM `{$wpdb->prefix}test`";
   $row = $wpdb->get_row( $sql, ARRAY_A );

   //获取options表中的设置选项
   $bold = get_option( "hc_test_bold" ) == 1 ? "bold" : "normal";

   ?><style>body{color:<? echo $row["color"] ?>;font-size:<? echo $row["size"] ?>px;font-weight:<? echo $bold; ?>;}</style><?
}

```
    
### 使用wordpress内置api设置插件属性
```php
# hc-test.php
<?php
/*
Plugin Name: hc-test
Plugin URI: http://zhuohc.com
Description: 测试插件
Version: 1.0
Author: hc
Author URI: http://zhuohc.com
License: GPLv2
*/

//设置时区为 亚洲/上海
date_default_timezone_set('Asia/Shanghai');

add_action( 'admin_menu', 'test_create_menu' );

function test_create_menu() {

   //创建顶级菜单
   add_menu_page(
      'HC的插件首页',
      'HC的插件',
      'manage_options',
      'hc_test' ,
      'hc_settings_page',
      plugins_url( '/images/icon.png', __FILE__ )
   );
}

add_action( 'admin_init', 'register_hc_test_setting' );

//使用register_setting()注册要存储的字段
function register_hc_test_setting() {

   //注册一个选项，用于装载所有插件设置项
   $option_group = "hc_test_group";
   register_setting( $option_group, 'hc_test_option' );

   //添加选项设置区域
   $setting_section = "hc_test_setting_section";
   add_settings_section(
      $setting_section,
      '设置',  // 标题
      'hc_test_setting_section_function',  // 调用方法，自定义输出
      $option_group
   );

   //设置字体颜色
   add_settings_field(
      'hc_test_color',  // 字段id
      '字体颜色',  // 字段标题
      'hc_test_color_function', // 字段对应函数方法
      $option_group,  // 字段对应分组
      $setting_section  // 字段对应设置选项
   );

   //设置字体大小
   add_settings_field(
      'hc_test_size',
      '字体大小',
      'hc_test_size_function',
      $option_group,
      $setting_section
   );

   //设置字体加粗
   add_settings_field(
      'hc_test_bold',
      '字体加粗',
      'hc_test_bold_function',
      $option_group,
      $setting_section
   );
}

function hc_test_bold_function() {
   $hc_test_option = get_option( "hc_test_option" );
   ?>
   <input name="hc_test_option[bold]" type="checkbox"  value="1" <? checked( 1, $hc_test_option["bold"] ); ?> /> 加粗
   <?
}

function hc_test_size_function() {
   $hc_test_option = get_option( "hc_test_option" );
   $size = $hc_test_option["size"];
   ?>
   <select name="hc_test_option[size]">
      <option value="12" <? selected( '12', $size ); ?>>12</option>
      <option value="14" <? selected( '14', $size ); ?>>14</option>
      <option value="16" <? selected( '16', $size ); ?>>16</option>
      <option value="18" <? selected( '18', $size ); ?>>18</option>
      <option value="20" <? selected( '20', $size ); ?>>20</option>
   </select>
   <?
}

function hc_test_color_function() {
   $hc_test_option = get_option( "hc_test_option" );
   ?>
   <input name='hc_test_option[color]' type='text' value='<? echo $hc_test_option["color"]; ?>' />
   <?
}

function hc_settings_page() {
   ?>
   <div class="wrap">
      <h2>插件顶级菜单</h2>
      <form action="options.php" method="post">
         <?
         $option_group = "hc_test_group";

         //输出一些必要的字段，包括验证信息等
         settings_fields( $option_group );

         //输出选项设置区域
         do_settings_sections( $option_group );

         //输出按钮
         submit_button();
         ?>
      </form>
   </div>
   <?
}


add_action( 'wp_head', 'hc_test_head_fun' );

function hc_test_head_fun() {

   $hc_test_option = get_option( "hc_test_option" );

   $bold = $hc_test_option["bold"] == 1 ? "bold" : "normal";
   ?><style>body{color:<? echo $hc_test_option["color"] ?>;font-size:<? echo $hc_test_option["size"] ?>px;font-weight:<? echo $bold; ?>;}</style><?
}


```
    
### 将插件改写成类的形式
```php
#hc-test.php
<?php
/*
Plugin Name: hc-test
Plugin URI: http://zhuohc.com
Description: 测试插件
Version: 1.0
Author: hc
Author URI: http://zhuohc.com
License: GPLv2
*/

//设置时区为 亚洲/上海
date_default_timezone_set('Asia/Shanghai');


class hcsem_change_font_style {

   //声明类里面的属性，用 var 开头
   var $icon_url = "/images/icon.png";
   var $option_group = "hc_test_group";

   //构造方法，创建类的时候调用
   function hcsem_change_font_style() {

      //创建菜单
      add_action( 'admin_menu', array( $this, 'hc_create_menu' ) );
      add_action( 'admin_init', array( $this, 'register_hc_test_setting' ) );
      add_action( 'wp_head',  array( $this,  'hc_test_head_fun' ) );
   }

   //使用register_setting()注册要存储的字段
   function register_hc_test_setting() {

      //注册一个选项，用于装载所有插件设置项
      register_setting( $this->option_group, 'hc_test_option' );

      //添加选项设置区域
      $setting_section = "hc_test_setting_section";
      add_settings_section(
         $setting_section,
         '',
         '',
         $this->option_group
      );

      //设置字体颜色
      add_settings_field(
         'hc_test_color',
         '字体颜色',
         array( $this, 'hc_test_color_function' ),
         $this->option_group,
         $setting_section
      );

      //设置字体大小
      add_settings_field(
         'hc_test_size',
         '字体大小',
         array( $this, 'hc_test_size_function' ),
         $this->option_group,
         $setting_section
      );

      //设置字体加粗
      add_settings_field(
         'hc_test_bold',
         '字体加粗',
         array( $this, 'hc_test_bold_function' ),
         $this->option_group,
         $setting_section
      );
   }

   function hc_test_bold_function() {
      $hc_test_option = get_option( "hc_test_option" );
      ?>
      <input name="hc_test_option[bold]" type="checkbox"  value="1" <? checked( 1, $hc_test_option["bold"] ); ?> /> 加粗
      <?
   }

   function hc_test_size_function() {
      $hc_test_option = get_option( "hc_test_option" );
      $size = $hc_test_option["size"];
      ?>
      <select name="hc_test_option[size]">
         <option value="12" <? selected( '12', $size ); ?>>12</option>
         <option value="14" <? selected( '14', $size ); ?>>14</option>
         <option value="16" <? selected( '16', $size ); ?>>16</option>
         <option value="18" <? selected( '18', $size ); ?>>18</option>
         <option value="20" <? selected( '20', $size ); ?>>20</option>
      </select>
      <?
   }

   function hc_test_color_function() {
      $hc_test_option = get_option( "hc_test_option" );
      ?>
      <input name='hc_test_option[color]' type='text' value='<? echo $hc_test_option["color"]; ?>' />
      <?
   }

   function hc_create_menu() {

      //创建顶级菜单
      add_menu_page(
         'HC的插件首页',
         'HC的插件',
         'manage_options',
         'hc_test' ,
         array( $this, 'hc_settings_page' ),
         plugins_url( $this->icon_url, __FILE__ )
      );
   }

   function hc_settings_page() {
      ?>
      <div class="wrap">
         <h2>插件顶级菜单</h2>
         <form action="options.php" method="post">
            <?
            //输出一些必要的字段，包括验证信息等
            settings_fields( $this->option_group );

            //输出选项设置区域
            do_settings_sections( $this->option_group );

            //输出按钮
            submit_button();
            ?>
         </form>
      </div>
      <?
   }

   function hc_test_head_fun() {

      $hc_test_option = get_option( "hc_test_option" );

      $bold = $hc_test_option["bold"] == 1 ? "bold" : "normal";
      ?><style>body{color:<? echo $hc_test_option["color"] ?>;font-size:<? echo $hc_test_option["size"] ?>px;font-weight:<? echo $bold; ?>;}</style><?
   }
}

new hcsem_change_font_style();

```

### 将插件设置内容转移到wordpress设置里

```php
# hc-test.php
  
class hcsem_change_font_style {

   var $option_group = "general";  // general常规 writing撰写 reading阅读 discussion讨论 media多媒体 permalink固定链接

   //构造方法，创建类的时候调用
   function hcsem_change_font_style() {

      add_action( 'admin_init', array( $this, 'register_hc_test_setting' ) );
      add_action( 'wp_head',  array( $this,  'hc_test_head_fun' ) );
   }

   //使用register_setting()注册要存储的字段
   function register_hc_test_setting() {

      //注册一个选项，用于装载所有插件设置项
      register_setting( $this->option_group, 'hc_test_option' );

      //设置字体颜色
      add_settings_field(
         'hc_test_color',
         '字体颜色',
         array( $this, 'hc_test_color_function' ),
         $this->option_group
      );

      //设置字体大小
      add_settings_field(
         'hc_test_size',
         '字体大小',
         array( $this, 'hc_test_size_function' ),
         $this->option_group
      );

      //设置字体加粗
      add_settings_field(
         'hc_test_bold',
         '字体加粗',
         array( $this, 'hc_test_bold_function' ),
         $this->option_group
      );
   }

   function hc_test_bold_function() {
      $hc_test_option = get_option( "hc_test_option" );
      ?>
      <input name="hc_test_option[bold]" type="checkbox"  value="1" <? checked( 1, $hc_test_option["bold"] ); ?> /> 加粗
      <?
   }

   function hc_test_size_function() {
      $hc_test_option = get_option( "hc_test_option" );
      $size = $hc_test_option["size"];
      ?>
      <select name="hc_test_option[size]">
         <option value="12" <? selected( '12', $size ); ?>>12</option>
         <option value="14" <? selected( '14', $size ); ?>>14</option>
         <option value="16" <? selected( '16', $size ); ?>>16</option>
         <option value="18" <? selected( '18', $size ); ?>>18</option>
         <option value="20" <? selected( '20', $size ); ?>>20</option>
      </select>
      <?
   }

   function hc_test_color_function() {
      $hc_test_option = get_option( "hc_test_option" );
      ?>
      <input name='hc_test_option[color]' type='text' value='<? echo $hc_test_option["color"]; ?>' />
      <?
   }

   function hc_test_head_fun() {

      $hc_test_option = get_option( "hc_test_option" );

      $bold = $hc_test_option["bold"] == 1 ? "bold" : "normal";
      ?><style>body{color:<? echo $hc_test_option["color"] ?>;font-size:<? echo $hc_test_option["size"] ?>px;font-weight:<? echo $bold; ?>;}</style><?
   }
}

new hcsem_change_font_style();
```
    
### 调用ajax
```php
# hc-test.php
<?php
/*
Plugin Name: hc-test
Plugin URI: http://zhuohc.com
Description: 测试插件
Version: 1.0
Author: hc
Author URI: http://zhuohc.com
License: GPLv2
*/

//设置时区为 亚洲/上海
date_default_timezone_set('Asia/Shanghai');

class hcsem_change_font_style {

   //声明类里面的属性，用 var 开头
   var $icon_url = "/images/icon.png";
   var $option_group = "hc_test_group";

   //构造方法，创建类的时候调用
   function hcsem_change_font_style() {

      //创建菜单
      add_action( 'admin_menu', array( $this, 'hc_create_menu' ) );
      add_action( 'admin_init', array( $this, 'register_hc_test_setting' ) );
      add_action( 'wp_head',  array( $this,  'hc_test_head_fun' ) );

      //使用ajax校验信息
      wp_enqueue_script( 'hc_test', plugins_url('js/hc_test.js', __FILE__), array('jquery') );
      wp_localize_script( 'hc_test', 'ajax_object', array( 'ajax_url' => admin_url( 'admin-ajax.php' ) ) );

      add_action( 'wp_ajax_color_check_action' , array( $this, 'color_check_action_fun') );
      add_action( 'wp_ajax_nopriv_hcsem_description' , array( $this, 'hcsem_description_fun') );
   }

   function color_check_action_fun(){

      if( trim( $_POST['color'] ) != "" ){ echo "ok"; }
      wp_die();
   }

   function hcsem_description_fun() {

      echo "hc的笔记本：" . $_POST['description'];
      wp_die();

   }

   //使用register_setting()注册要存储的字段
   function register_hc_test_setting() {

      //注册一个选项，用于装载所有插件设置项
      register_setting( $this->option_group, 'hc_test_option' );

      //添加选项设置区域
      $setting_section = "hc_test_setting_section";
      add_settings_section(
         $setting_section,
         '',
         '',
         $this->option_group
      );

      //设置字体颜色
      add_settings_field(
         'hc_test_color',
         '字体颜色',
         array( $this, 'hc_test_color_function' ),
         $this->option_group,
         $setting_section
      );

      //设置字体大小
      add_settings_field(
         'hc_test_size',
         '字体大小',
         array( $this, 'hc_test_size_function' ),
         $this->option_group,
         $setting_section
      );

      //设置字体加粗
      add_settings_field(
         'hc_test_bold',
         '字体加粗',
         array( $this, 'hc_test_bold_function' ),
         $this->option_group,
         $setting_section
      );
   }

   function hc_test_bold_function() {
      $hc_test_option = get_option( "hc_test_option" );
      ?>
      <input name="hc_test_option[bold]" type="checkbox"  value="1" <? checked( 1, $hc_test_option["bold"] ); ?> /> 加粗
      <?
   }

   function hc_test_size_function() {
      $hc_test_option = get_option( "hc_test_option" );
      $size = $hc_test_option["size"];
      ?>
      <select name="hc_test_option[size]">
         <option value="12" <? selected( '12', $size ); ?>>12</option>
         <option value="14" <? selected( '14', $size ); ?>>14</option>
         <option value="16" <? selected( '16', $size ); ?>>16</option>
         <option value="18" <? selected( '18', $size ); ?>>18</option>
         <option value="20" <? selected( '20', $size ); ?>>20</option>
      </select>
      <?
   }

   function hc_test_color_function() {
      $hc_test_option = get_option( "hc_test_option" );
      ?>
      <input name='hc_test_option[color]' type='text' value='<? echo $hc_test_option["color"]; ?>' />
      <font id="error_color"></font></div>
      <?
   }

   function hc_create_menu() {

      //创建顶级菜单
      add_menu_page(
         'HC的插件首页',
         'HC的插件',
         'manage_options',
         'hc_test' ,
         array( $this, 'hc_settings_page' ),
         plugins_url( $this->icon_url, __FILE__ )
      );
   }

   function hc_settings_page() {
      ?>
      <div class="wrap">
         <h2>插件顶级菜单</h2>
         <form action="options.php" method="post">
            <?
            //输出一些必要的字段，包括验证信息等
            settings_fields( $this->option_group );

            //输出选项设置区域
            do_settings_sections( $this->option_group );

            //输出按钮
            submit_button();
            ?>
         </form>
      </div>
      <?
   }

   function hc_test_head_fun() {

      $hc_test_option = get_option( "hc_test_option" );

      $bold = $hc_test_option["bold"] == 1 ? "bold" : "normal";
      ?><style>body{color:<? echo $hc_test_option["color"] ?>;font-size:<? echo $hc_test_option["size"] ?>px;font-weight:<? echo $bold; ?>;}</style><?
   }
}

new hcsem_change_font_style();


# js/hc_test.js
jQuery(document).ready( function($) {
   $("input[name='hc_test_option[color]']").blur( function() {
      $.ajax({
         type: "POST",
         data: "color=" + $(this).val() + "&action=hc_color_check",
         url: ajax_object.ajax_url,
         beforeSend: function() {
            $('#error_color').html('校验中...'); 
         },
         success: function( $data ) {
            if( $data == 'ok'){
                $('#error_color').html('输入正确');  
            } else {
                $('#error_color').html('颜色不能为空！'); 
            }
         }
      });
   });
});


jQuery(document).ready( function($) {
   $(".description").click( function() {
      $.ajax({
         type: "POST",
         data: "description=" + $(this).text() + "&action=hcsem_description",
         url: ajax_object.ajax_url,
         success : function( $data ) {

            if( $data != "0" ) {
               $(".description").text( $data );
            }
         }
      });
   });
});
```

### 实现插件翻译功能
```shell
.
├── css
│   └── hc_copyrighy.css
├── hc-test.php
├── images
│   └── icon.png
├── js
│   └── hc_test.js
└── languages
    ├── zh_CN.mo
    └── zh_CN.po
```
```php
# hc-test.php
<?php
/*
Plugin Name: hc-test
Plugin URI: http://zhuohc.com
Description: 测试插件
Version: 1.0
Author: hc
Author URI: http://zhuohc.com
License: GPLv2
*/

//设置时区为 亚洲/上海
date_default_timezone_set('Asia/Shanghai');

class hcsem_change_font_style {

   //声明类里面的属性，用 var 开头
   var $icon_url = "/images/icon.png";
   var $option_group = "hc_test_group";

   //构造方法，创建类的时候调用
   function hcsem_change_font_style() {

      //创建菜单
      add_action( 'admin_menu', array( $this, 'hc_create_menu' ) );
      add_action( 'admin_init', array( $this, 'register_hc_test_setting' ) );
      add_action( 'wp_head',  array( $this,  'hc_test_head_fun' ) );

      //使用ajax校验信息
      wp_enqueue_script( 'hc_test', plugins_url('js/hc_test.js', __FILE__), array('jquery') );
      wp_localize_script( 'hc_test', 'ajax_object', array( 'ajax_url' => admin_url( 'admin-ajax.php' ) ) );

      add_action( 'wp_ajax_color_check_action' , array( $this, 'color_check_action_fun') );
      add_action( 'wp_ajax_nopriv_hcsem_description' , array( $this, 'hcsem_description_fun') );

      add_action( 'init', array( $this, 'hcsem_load_textdomain' ) );
   }

   function hcsem_load_textdomain() {

      //加载 languages 目录下的翻译文件 zh_CN
      $currentLocale = get_locale();

      if( !empty( $currentLocale ) ) {

         $moFile = dirname(__FILE__) . "/languages/{$currentLocale}.mo";

         if( @file_exists( $moFile ) && is_readable( $moFile ) ) load_textdomain( 'hc-test', $moFile );
      }
   }

   function color_check_action_fun(){

      if( trim( $_POST['color'] ) != "" ){ echo "ok"; }
      wp_die();
   }

   function hcsem_description_fun() {

      echo "HC的笔记本：" . $_POST['description'];
      wp_die();

   }

   //使用register_setting()注册要存储的字段
   function register_hc_test_setting() {

      //注册一个选项，用于装载所有插件设置项
      register_setting( $this->option_group, 'hc_test_option' );

      //添加选项设置区域
      $setting_section = "hc_test_setting_section";
      add_settings_section(
         $setting_section,
         '',
         '',
         $this->option_group
      );

      //设置字体颜色
      add_settings_field(
         'hc_test_color',
         __( 'color', 'hc-test' ),
         array( $this, 'hc_test_color_function' ),
         $this->option_group,
         $setting_section
      );

      //设置字体大小
      add_settings_field(
         'hc_test_size',
         __( 'size', 'hc-test' ),
         array( $this, 'hc_test_size_function' ),
         $this->option_group,
         $setting_section
      );

      //设置字体加粗
      add_settings_field(
         'hc_test_bold',
         __( 'bold', 'hc-test' ),
         array( $this, 'hc_test_bold_function' ),
         $this->option_group,
         $setting_section
      );
   }

   function hc_test_bold_function() {
      $hc_test_option = get_option( "hc_test_option" );
      ?>
      <input name="hc_test_option[bold]" type="checkbox"  value="1" <? checked( 1, $hc_test_option["bold"] ); ?> /> <? _e( 'set bold', 'hc-test' ); ?>
      <?
   }

   function hc_test_size_function() {
      $hc_test_option = get_option( "hc_test_option" );
      $size = $hc_test_option["size"];
      ?>
      <select name="hc_test_option[size]">
         <option value="12" <? selected( '12', $size ); ?>>12</option>
         <option value="14" <? selected( '14', $size ); ?>>14</option>
         <option value="16" <? selected( '16', $size ); ?>>16</option>
         <option value="18" <? selected( '18', $size ); ?>>18</option>
         <option value="20" <? selected( '20', $size ); ?>>20</option>
      </select>
      <?
   }

   function hc_test_color_function() {
      $hc_test_option = get_option( "hc_test_option" );
      ?>
      <input name='hc_test_option[color]' type='text' value='<? echo $hc_test_option["color"]; ?>' />
      <font id="error_color"></font></div>
      <?
   }

   function hc_create_menu() {

      //创建顶级菜单
      add_menu_page(
         'hc的插件首页',
         'hc的插件',
         'manage_options',
         'hc_test' ,
         array( $this, 'hc_settings_page' ),
         plugins_url( $this->icon_url, __FILE__ )
      );
   }

   function hc_settings_page() {
      ?>
      <div class="wrap">
         <h2>插件顶级菜单</h2>
         <form action="options.php" method="post">
            <?
            //输出一些必要的字段，包括验证信息等
            settings_fields( $this->option_group );

            //输出选项设置区域
            do_settings_sections( $this->option_group );

            //输出按钮
            submit_button();
            ?>
         </form>
      </div>
      <?
   }

   function hc_test_head_fun() {

      $hc_test_option = get_option( "hc_test_option" );

      $bold = $hc_test_option["bold"] == 1 ? "bold" : "normal";
      ?><style>body{color:<? echo $hc_test_option["color"] ?>;font-size:<? echo $hc_test_option["size"] ?>px;font-weight:<? echo $bold; ?>;}</style><?
   }
}

new hcsem_change_font_style();

```
    