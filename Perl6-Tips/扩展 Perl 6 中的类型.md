# ��չ Perl 6 �е�����

## ʹ�ü̳�

```perl6
class BetterInt is Int {
    method even { self %% 2 }
}

my BetterInt $x .= new: 42;
say $x.even; 

$x .= new: 71;
say $x.even;

say $x + 42;

# OUTPUT:
# True
# False
# 113
```

`my BetterInt $x` Լ�� `$x` ֻ�ܰ���  *BetterInt* �����������������͵Ķ���`.= new: 42` �ȼ��� `= BetterInt.new: 42`��
�������������������һ�� *Int* �͵Ĳ������������������һ�� *BetterInt* ���͵Ĳ�������ܸ���:

```perl6
sub foo(Int $x) { say "\$x is $x"}

my BetterInt $x .= new: 42;
foo $x;

# OUTPUT:
# $x is 42
```

## But... But... But...

����һ��ѡ���ǲ��Ͻ�һ����ɫ(role)��*but* ��׺���������������һ�ݿ�����Ϊ�ö������һ������:

```perl6
my $x = 42 but role { method even { self %% 2 } };
say $x.even;

# OUTPUT:
# True
```

��Ȼ��ɫ��һ���������ġ����������һ������ʹ����һ��Ԥ����Ľ�ɫ���һ�չʾ�����ǵĶ���ȷʵ��������һ�ݣ�

```perl6
role Better {
    method better { "Yes, I am better" }
}

class Foo {
    has $.attr is rw
}

my $original = Foo.new: :attr<original>;
my $copy     = $original but Better;
$copy.attr   = 'copy'; 

say $original.attr;  # still 'original'
say $copy.attr;      # this one is 'copy'

say $copy.better;
say $original.better; # fatal error: can't find method

# OUTPUT:
# original
# copy
# Yes, I am better
# Method 'better' not found for invocant of class 'Foo'
#   in block <unit> at test.p6 line 18
```

�⿴����ͦ����ģ����Ƕ�������ԭ����Ŀ����˵��������������൱���ģ�

```perl6
my $x = 42 but role { method even { self %% 2 } };
say $x.even; # True
$x = 72;
say $x.even; # No such method!
```

�Ǹ���ɫ����Ͻ�������������洢�Ķ������ˣ�����һ�������������зŽ���һ���µ�ֵ����߼���Ķ�������ô *.even* �����Ͳ����ˣ����������ٴΰ��Ǹ���ɫ��Ͻ�����

## ������

��֪������԰������̵���������� ������Ǹ�������Ϊ�����̵ĵ�һ��λ�ò��������������ܼ���ʹ����ʽ�������ã����ǲ��ܰ���Щ���ӷֽ�ɶ��У�

```perl6
sub even { $^a %% 2 };
say 42.&even.uc;

# OUTPUT:
# TRUE
```

��ȷʵ��Ϊ����������Ӷ��⹦�ܵ�һ�ֵ��巽ʽ�����ǵ������̶����е� `$^a` ���õ�һ�������������ڵ��õ��Ǹ����󣩲�������������Ҳ���Ա�дΪ��

```perl6
sub ($x) { $x %% 2 }
```

## ��������

����[Javaccript ����Щ������ô����˵](http://shop.oreilly.com/product/9780596517748.do), Ȼ������ԭ��������Σ�յġ���Ϊ����Ӱ���������в��֡�������������������ģ��Ҳ�ܵ�Ӱ�졣

��������Ȩ�����㣬�Ҹ���˵�����㹤���ĺ˵糧�ڻ��ˣ������ǿ���һЩ���룺

```perl6
# Foo.pm6
unit module Foo;
sub fob is export {
    say 42.even;
}

# Bar.pm6
unit module Bar;
use MONKEY-TYPING;
augment class Int {
    method even { self %% 2 }
}

# test.p6
use Foo;
use Bar;

say 72.even;
fob;

# OUTPUT:
# True
# True
```

���е���Ϊ�������� *Bar.pm6* �С����ȣ�����д��һ�� *use MONKEY-TYPING* ����������������������һЩΣ�յ���Ϊ��Ȼ���������� **class Int** ��ǰ��ʹ���� *augment* �ؼ�������������Ѿ����ڵ��ࡣ���ǵ����������һ���� *even* �ķ����Ը��������Ǹ� Int �Ƿ���ż����

���е�����������ʹ�� *even* �����ˣ�����Ȼ�ﵽ�����ǵ�Ҫ�����е�Σ�ա�


##  ��а����

���������� [Cool ����](http://docs.perl6.org/type/Cool)�Ժ������е������Ű��г���λ��

```perl6
use MONKEY-TYPING;
augment class Cool {
    method even { self %% 2 }
}

.say for 72.even, '72'.even, pi.even, ?.even;

# OUTPUT:
# Method 'even' not found for invocant of class 'Int'
# in block <unit> at test.p6 line 8
```

��⣬�������ˣ�ԭ�������������� **Cool** ���͵�ʱ�������� **Cool** �����������Ѿ�������(composed)������Ϊ�������ܹ��������Ǳ���ʹ�� `.^compose` Ԫ����Э�鷽�������¹������ǣ�

```perl6
use MONKEY-TYPING;
augment class Cool {
    method even { self %% 2 }
}

.^compose for Int, Num, Rat, Str, IntStr, NumStr, RatStr;

.say for 72.even, '72'.even, pi.even, ?.even;

# OUTPUT:
# True
# True
# False
# False
```

�������ܹ����ˣ�Int, Num, Rat, Str, IntStr, NumStr, RatStr ����ӵ���� `.even` ����(ע�⣺��Щ���Ǽ̳��� Cool �Ľ��е�����)! ���а�������˳Ծ���

## ����

������ Perl 6 �ĺ������ͻ�����������Ĺ���ʱ�����м���ѡ��

- ʹ�� **is Class** ����
- ʹ�� **but Role** ���һ����ɫ
- ʹ�� `$objec.&sub` ������������Ϊ����ʹ��
- ʹ��  augment��ע�ⰲȫ��

[Perl 6 �� There Is More Than One Way To Extend it](http://blogs.perl.org/users/zoffix_znet/2016/04/extra-typical-perl-6.html).