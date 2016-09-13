# Perl  6 �е�����!

�� Perl 5 ��, �Һܸм����������������Ľṹ:

```perl
my @things = $text =~ /thing/g;
my %things = $text =~ /(key)...(value)/g;
```

���ó�һС�ο���Ԥ�����ı���������һ��������ʽ�����, ��õ���һ���б��ɢ�У����ħ��һ�㣡����Ҳ������ Perl  6 ��ʹ��������ʽ������ **[comb](http://docs.perl6.org/routine/comb)** ���ʺ������������

![img](http://upload-images.jianshu.io/upload_images/326727-29a3966bbb8e437f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## Plain 'Ol Characters

����԰� *comb* ���������̻򷽷������������������ʽ�У� *comb* ����ַ����ֽ�Ϊ�ַ�:

```Perl6
'foobar moobar ���bar'.comb.join('|').say;
'foobar moobar ���bar'.comb(6).join('|').say;

# OUTPUT:
# f|o|o|b|a|r| |m|o|o|b|a|r| |�|�|��|b|a|r
# foobar| mooba|r ���b|ar
```

�������κβ����� *comb* ���õ������������ַ����� *comb* �ṩһ������ `$n`, ��ô���õ���������Ϊ `$n` ���ַ���һ���б��������û��ʣ�µ��ַ������Ļ�������б����ս϶̵�����ַ��������������ʹ���������� 30 ����

## Limits

��Ҳ����Ϊ *comb* �ṩ�ڶ��������������� *limit*������ʾÿ���б�����ຬ�� limit ��Ԫ��:

```Perl6
'foobar moobar ���bar'.comb(1, 5).join('|').say;
'foobar moobar ���bar'.comb(6, 2).join('|').say;

# OUTPUT:
# f|o|o|b|a
# foobar| mooba
```
��������ʹ�� *comb* ����/������������ʽ����������������չʾ��������

## ����

*comb* Ҳ������ͨ�� [Str](http://docs.perl6.org/type/Str) ��Ϊ����������һ�������Ǹ��ַ�����ƥ����б��������ڼ������ַ������ַ����г��ֵĴ���ʱ�����á�

```Perl6
'The ?? ran after a ??, but the ?? ran away'.comb('??').Int.say;
'The ?? ran after a ??, but the ?? ran away'.comb('ran').Int.say;

# OUTPUT:
# 1
# 2
```

## �򵥵�ƥ��

*comb* �Ĳ���Ҳ������һ��������ʽ������ƥ�����Ϊһ�����������أ�

```Perl6
foobar moobar ���bar'.comb(/<[a..z]>+ 'bar'/).join('|').say;

# OUTPUT:
# foobar|moobar
```

## ������ƥ��Ķ���

�����ʹ��[���Ӷ���](http://docs.perl6.org/language/regexes#Look-around_assertions)���߸��򵥵� `<(` �� `)>` ������ʽ����Ǻ�:

```perl6
'moo=meow ping=pong'.comb(/\w+    '=' <( \w**4/).join('|').say; # values
'moo=meow ping=pong'.comb(/\w+ )> '='    \w**4/).join('|').say; # keys

# OUTPUT:
# meow|pong
# moo|ping
```

�����ʹ��  `<(` �� `)>` ����֮һ�����߶�ʹ�á� `<(` ��ƥ�����ų��κ���֮ǰ�Ķ����� `)>` ������֮����κζ������� `/'foo' <('bar')> 'ber'/`  ��ƥ����� *foobarber* �Ķ��������Ǵ� *comb* �з��صĶ���ֻ���� *bar*��

## �������

��ô���ó� Perl 5 ������ ��/ֵ�Զ��أ�

```Perl6
my %things = 'moo=meow ping=pong'.comb(/(\w+) '=' (\w+)/, :match)?.Slip?.Str;
say %things;

# OUTPUT:
# moo => meow, ping => pong
```

Բ�������ڲ���`:match` ����ʹ *comb* ����һ�� **Match** ������б����Ƿ���һ���ַ����б���һ��������ʹ������ hyper ������� **Matches** ת��Ϊ [Slips](http://docs.perl6.org/type/Slip)����������һ��������б����������Ծ��� **Match** ���������Ϊʲô���ǻ�Ҫ������ת��Ϊ **Str** ��ԭ��

���ǻ�����ʹ�þ�������ʹ�����������

```Perl6
my %things = 'moo=meow ping=pong'
    .comb(/$<key>=\w+ '=' $<value>=\w+/, :match)
    .map({ .<key> => .<value>.Str });
say %things;

# OUTPUT:
# moo => meow, ping => pong
```

�㻹���԰�����Ĵ���д��������

```Perl6
my %things = ('moo=meow ping=pong' ~~ m:g/(\w+) '=' (\w+)/)?.Slip?.Str;
say %things;

# OUTPUT:
# moo => meow, ping => pong
```

## ����

�� *comb* �� *[rotor](http://blogs.perl.org/users/zoffix_znet/2016/01/perl-6-rotor-the-king-of-list-manipulation.html)* ��������û��ǿ��


## ����

����ʹ�ô��� `:match` ������ `.comb` , ����þ�ʹ�� `.match` ��������: 

```Perl6
'moo=meow ping=pong'.match(/(\w+) '=' (\w+)/, :g)
```