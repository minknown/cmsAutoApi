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

## Zblog
接口文档：https://docs.zblogcn.com   
接口初始化:  
*localhost需要更换为您的域名或zblog安装位置，用户名和密码是您的zblog后台管理员账户密码*
````
$as="http://localhost/zb_system/api.php";
$user="root";
$pass="qqq12345678!";
````
依旧账户密码获得token，这里用nst.tx文本文件做了缓存，该token有效期为7天，在第六天的时候会更新下token。  
````
$nst=filemtime("nst.tx");
if(time()-$nst>3600*24*6){
	$data=array("username"=>$user,"password"=>$pass,"savedate"=>"7");
	$json=json_decode(curl_post_https($as."?mod=member&act=login",$data),true);
	$token=$json['data']['token'];
	unlink("nst.tx");file_put_contents("nst.tx",$token);
}else{
	$token=file_get_contents("nst.tx");
}
````
直接调用:  
*curl_post_https这个方法本页由提供。  
````
if($token!=""){
	$data=array(
	"token"=>$token,
	"ID"=>0,
	"Type"=>0,
	"Title"=>$_POST['title'],
	"Content"=>$_POST['nr'],
	"Intro"=>$_POST['d'],
	"Tag"=>$_POST['k'],
	"cate"=>$_POST['tidsim'],
	"Alias"=>time().rand(100,999)
	);
	echo curl_post_https($as."?mod=post&act=post",$data);
}else{
	echo "token-error";
}
````
curl_post_https方法：
````
//post
 function curl_post_https($url,$data){ // 模拟提交数据函数
        $curl = curl_init(); // 启动一个CURL会话
        curl_setopt($curl, CURLOPT_URL, $url); // 要访问的地址
        curl_setopt($curl, CURLOPT_SSL_VERIFYPEER, 0);              // 对认证证书来源的检查
        curl_setopt($curl, CURLOPT_SSL_VERIFYHOST, 2);              // 从证书中检查SSL加密算法是否存在
        curl_setopt($curl, CURLOPT_USERAGENT, $_SERVER['HTTP_USER_AGENT']); // 模拟用户使用的浏览器
        curl_setopt($curl, CURLOPT_FOLLOWLOCATION, 1); // 使用自动跳转
        curl_setopt($curl, CURLOPT_AUTOREFERER, 1); // 自动设置Referer
        curl_setopt($curl, CURLOPT_POST, 1); // 发送一个常规的Post请求
        curl_setopt($curl, CURLOPT_POSTFIELDS, $data); // Post提交的数据包
        curl_setopt($curl, CURLOPT_TIMEOUT, 30); // 设置超时限制防止死循环
        curl_setopt($curl, CURLOPT_HEADER, 0); // 显示返回的Header区域内容
        curl_setopt($curl, CURLOPT_RETURNTRANSFER, 1); // 获取的信息以文件流的形式返回
        $tmpInfo = curl_exec($curl); // 执行操作
        if (curl_errno($curl)) {
            echo 'Errno'.curl_error($curl);//捕抓异常
        }
        curl_close($curl); // 关闭CURL会话
        return $tmpInfo; // 返回数据，json格式
    }
````
