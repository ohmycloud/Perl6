# ��ʼ Bailador

[Bailador](https://github.com/tadzik/Bailador/) �Ƕ� [Perl Dancer](http://perldancer.org/) Web ������ܵ�ģ�¡�
��װ������

```perl6
panda install Bailador
# or
zef install Bailador
```

����������һ���ű� **first.pl**����ӡ "hello world":

```perl6
use v6;
use Bailador;

get '/' => sub {
    "hello world"
}

baile;
```

���У�perl6 first.pl ��������һ��С�͵� Web ���������������3000�˿��Ϸ�������

```
$ perl6 first.pl
Entering the development dance floor: http://0.0.0.0:3000
[2016-05-05T12:57:31Z] Started HTTP server.
```

�� Bailador �У�������Ҫ�� **HTTP** ���󷽷��ͷ������ϵ�·��ӳ���һ������������, ��������̻᷵������������ݡ�����������У����ǰ����Ǹ���������վ��·���� **get** HTTP ����ӳ��Ϊ�����ַ��� **hello world**��������������������������� [http://0.0.0.0:3000/](http://0.0.0.0:3000/) ��ͻῴ������ı���

���ǻ�����ӳ������·��(path-es):

```perl6
get '/about' => sub {
    "������"
}
```

���� [ http://0.0.0.0:3000/about]( http://0.0.0.0:3000/about) url ӳ��Ϊ���� �������ҡ���


## ·���е�ռλ��

·���е�һ���ֿ�������ð�ſ�ͷ��ռλ��:

```perl6
get '/hello/:name' => sub ($name) {
    "Hello $name!"
};
```

**:name** ������ƥ�����б�� **/** ֮����κ��ַ�������������ƥ�䵽��ֵ�ᱻ��ֵ�������������е� **$name** ������

������ռλ�������ӵ�ж��������ռλ����ʵ��������ʲô�޹ؽ�Ҫ��ռλ�������񵽵�ֵ�ᰴ�����ǳ����� url �е�˳��ֵ�������Ĳ�����

```perl6
get '/hello/:first/:family' => sub ($fname, $lname) {
    "Hello $fname! And hi $lname"
};
```

����������У����� **:first** ռλ�����񵽵���ʲô�������ᱻ��ֵ�� **$fname** ���������� **:family** ���񵽵���ʲô�������ᱻ��ֵ�� **:$lname**������ url [http://0.0.0.0:3000/hello/Foo/Bar](http://0.0.0.0:3000/hello/Foo/Bar) ������������Ӧ:

```
Hello Foo! And hi Bar!
```

��Ȼ����ռλ�������ֺͲ�����������ͬ���ܻ��ô�����׶������ǵڶ����ű��������汾:

```perl6
use v6;
use Bailador;

get '/' => sub {
    "hello world"
}

get '/hello/:first/:family' => sub ($fname, $lname) {
    "Hello $fname! And hi $lname"
};


baile;
```

# ʹ�� Bailador �����ı�

������������ô���û��Ƕ��������벢��������Ը��û���

## ʹ�� POST ����

�����⣬���Ǳ��봴������·��(routes)��Ϊ���� Bailador �����ܴ��� GET ������

```perl6
# echo_post.p6

use v6;
use Bailador;

get '/' => sub {
	'<form method="POST" action="/echo"><input name="text"><input type="submit"></form>';
}

post '/echo' => sub {
  my $text = request.params<text> // '';
	my $html = 'You said (in a POST request) ';
	$html ~= $text;
	return $html;
}

baile;
```

![img](http://upload-images.jianshu.io/upload_images/326727-569a4158b27cc4d0.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

�����ܿ�����ô����һ��·�������� POST ����

��һ��·�� **get '/' => {** �ᷢ��һ�� **GET** ���������᷵��һ������������ű��е� HTML Ƭ�Ρ�(��֪�������Ǻܿ�ͻ�ʹ��ģ����) �Ǹ� HTML Ƭ�ΰ�����һ�����е����ı���ı���һ���ύ��ť���������һ��ͨ�� **/echo** URL �� **action**�����ұ�ӵ�� **method="POST"**������ζ�ţ����û�����ύ��ťʱ��������ᷢ�ͻ� POST ����

�ڶ���·�� **post '/echo' => sub {** �ᴦ�� **/echo** ·���� POST ����
  
Bailador �ṩ�� **request** ������ [Bailador::Request](https://github.com/tadzik/Bailador/blob/master/lib/Bailador/Request.pm)����ʽ���ش���ǰ����Ķ���

**request** �����м�������������һ���� **params** ������������һ��ɢ�У�����ɢ�еļ��ǲ��������֣������������������ **text**����ֵ���ύ��ֵ��

���ǰ��Ǹ�ֵ������ **$text** �����У���������ʹ�� '//' defined-or �����������ñ�����ֵΪ�գ����û�û���ṩ�κ�ֵ������¡�Ȼ�����������û��ṩ��ֵ��� "html" �ַ���������ͻ��Ǹ��ַ������������СС�Ļ��Է��������ܹ�������

![img](http://upload-images.jianshu.io/upload_images/326727-d07297442a570b93.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## ʹ�� GET ����

```perl6
use v6;
use Bailador;

get '/' => sub {
	'<form method="GET"  action="/echo"><input name="text"><input type="submit"></form>';
}

get '/echo' => sub {
    return 'You said (in a GET request) ' ~ (request.params<text> // '');
}

baile;
```

![img](http://upload-images.jianshu.io/upload_images/326727-399effeb8572bf83.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


����������У���ʡ������ʱ���� **$text** �� **$html**����֮ǰ������������Ҳ���Ǳ�Ҫ�ġ�������ʹ�� **GET** ����������ύ������������ URL ��ַ����ƴ�������ǵ� text �ֶκ��ֶε�ֵ��

# Bailador Application in a module

## ģ��

���������ģ���У��������ݽ��յ����� `$h` �У�֮��ʹ�����������չʾ�汾�ź͵�ǰʱ�� - �Ӽ�Ԫ��ʼ��������
*bailador/code_in_module/views/index.tt*

```perl6
% my ($h) = @_;
<!DOCTYPE html>
<html>
  <head>
    <title>Bailador App</title>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=yes">
  </head>
  <body>
    <h1>Bailador App</h1>
    <div>
      Version <%= $h<version> %> Current time: <%= $h<date> %>
    </div>
  </body>
</html>
```


## ģ��

����ļ������д������������:

```perl6
unit class Demo;
```

Ϊ��ӵ���ض���������(DSL)���������� Bailador �������Ƕ���·�ɸ����ס�

```perl6
use Bailador;
```
����Ҫ������������·�ɡ�

```perl6
unit class Demo;
use Bailador;
 
my $version = '0.01';

get '/' => sub {
    template 'index.tt', { version => $version, date => time }
}
```

## ����Ӧ�ó���Ľű�

```perl6
use Bailador;
Bailador::import();
use lib callframe(0).file.IO.dirname ~ '/lib';
use Demo;

baile;
```

������˼��Ӧ������δ���:

```perl6
use lib callframe(0).file.IO.dirname ~ '/lib';
```

������������̵ĸ�Ŀ¼ - ���� `app.pl` �ļ��ڸ�Ŀ¼�� - Ȼ��� `/lib` ��Ŀ¼��ӵ� perl ��Ҫ���Ҷ���ģ��ĵط�������� `lib` ��Ŀ¼�¼��� `Demo.pm` �ļ���


![img](http://upload-images.jianshu.io/upload_images/326727-317746ed1c73c978.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)