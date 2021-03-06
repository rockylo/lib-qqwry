lib-qqwry
=====

lib-qqwry是一个高效查询纯真IP库(qqwry.dat)的模块;  
为更好的字符转化效率,未使用iconv模块,直接使用gbk编码表文件。  
经过不断优化，相同环境下，单次查询速度从最初的0.6毫秒提升到现在的0.004毫秒;  

### 实现的功能
1.通过IP地址或有效的IP数值,搜索IP地址对应的地理位置信息。  
2.搜索一个IP段的地理位置信息。  
3.IP地址与数值的互转。  

### npm安装
<pre>
npm install lib-qqwry
</pre>

### 调用方法
<pre>
var qqwry = require('lib-qqwry').info(); //调用并初始化，普通机器初始需要70毫秒左右;
var ipL = qqwry.searchIP("202.103.102.10"); //查询IP信息
var ipLA = qqwry.searchIPScope("0.0.0.0","1.0.0.0");  //查询IP段信息
</pre>

## API
标明的"静态方法"可以值接使用,无需初始化.  
初使化操作会将GBK编码表,IP库加载到内存中,以提高后续的查询效率,大概占用12M左右的内存.

### info(dataPath) IP库初始化
dataPath : IP库路径,可选; //默认路径为data文件夹中(__dirname + "/data/qqwry.dat");   
callback : 回调函数 //可在此时调用查询函数  

### infoAsync(dataPath,callback) IP库初始化的异步方法
info()的异步方法；
初始化需要70毫秒，以及占用9MB左右的内存，项目资源紧张可以异步初始化。

<pre>
//你可以这样
qqwry.info();
var ipL = qqwry.searchIP("202.103.102.10");

//也可以这样初始化,推荐;
qqwry.infoAsync(function(){
	var ipL = qqwry.searchIP("202.103.102.10");
});
</pre>

### unInfo() 释放初始化后占用的资源  
info()的逆方法

### searchIP(IP) 单个IP查询
IP : IP地址/IP数值
反回一个JSON对像;  
<pre>
> qqwry.searchIP("255.255.255.255");
{ ip: '255.255.255.255',
  Country: '纯真网络',
  Area: '2013年6月10日IP数据' }
</pre>

### searchIPScope(beginIP,endIP) IP段查询
beginIP : 启始IP  
endIP : 结束IP  
反回一个JSON对像数组;  
<pre>
> qqwry.searchIPScope("0.0.0.0","1.0.0.0");
[ { begIP: '0.0.0.0',
    endIP: '0.255.255.255',
    Country: 'IANA保留地址',
    Area: ' CZ88.NET' },
  { begIP: '1.0.0.0',
    endIP: '1.0.0.255',
    Country: '澳大利亚',
    Area: ' CZ88.NET' } ]
</pre>

### searchIPScopeAsync(beginIP,endIP,callback) IP段查询的异步方法
searchIPScope() 的异步方法,查询结果会以第一个参数的形式传给回调函数;  


### DBUG(Bool) 调试模式开关,默认未启用
DUBG模式会在控制台输出查询的关键信息,方便定位错误;
<pre>
var qqwry = require('lib-qqwry').DBUG().info(); //开启调试模式并初始化
qqrry.DBUG(false); //关闭调试模式;
</pre>

### ipToInt(IP) IP地址转数值(静态方法)
<pre>
> qqwry.ipToInt("255.255.255.255")
4294967295
</pre>

### intToIP(INT) 数值转IP地址(静态方法)
<pre>
> qqwry.intToIP(4294967295)
'255.255.255.255'
</pre>

### ipEndianChange(INT) 字节序转换(静态方法)
按32位转换参数的字节序  
一些云平台的环境变量中IP信息可能是Little-Endian形式的数值;  
比如百度node.js环境中的 `process.env.BAE_ENV_COOKIE_IP` , 这时候就有用了;
<pre>
> qqwry.ipEndianChange(0x010000FF)
4278190081 //0xFF000001
</pre>

## 文档说明
1. ./data/qqwry.dat  默认IP库,可用最新IP库替换; 下载地址[www.cz88.net](http://www.cz88.net/)
2. ./lib/qqwry.js  解析IP库的主文件;
3. ./lib/gbk.js  GBK编码表文件,从[iconv-lite](https://github.com/ashtuchkin/iconv-lite)中找出来的,并增加了一个转码方法;
4. ./test/test.js  使用演示;
5. ./test/test_v.js  效率测试示例;

### 效率测试文件 test_v.js
`node test_v.js 255.255.255.255` 正常工作检查  
`node test_v.js -1` 单个查询效率测试
`node test_v.js -2` 10次IP段查询效率测试  

##作者
[含浪](http://www.cnblogs.com/whyoop)   w.why@163.com


