# cmsAutoApi
基于Wordpress和Zblog的API管理操作案例 -For PHP

## Wordpress:  
接口文档：https://developer.wordpress.org/rest-api/reference/categories/#list-categories  
接口初始化:
*以下为php代码，先初始化，其中用户名和密码是在wp后台用户生成的密钥。wp-load.php是wp安装好后根目录有的。  *
````
$user="root";
$password="MI4h 7CA6 uOVh Muu9 RNm1 I07O";
require( dirname(__FILE__) . '/wp-load.php' );
$headers = array (
    'Authorization' => 'Basic ' . base64_encode( $user . ':' .$password ),
);
````
直接调用:  
````
 $url = rest_url( 'wp/v2/tags/' );//接口名字
    $theresponse = wp_remote_post( $url, array (
    'method'  => 'POST',
    'headers' => $headers,
    'body' => array(
        'name' => $thename,
        'description' =>$thename//参数
    )));
    echo $therespons;
````
