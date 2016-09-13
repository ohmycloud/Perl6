## Sneaking into a loop

[Sneaking into a loop]https://gfldex.wordpress.com/2016/08/10/sneaking-into-a-loop/

Zoffix �ش���һ������ Perl 5s <> ��������[����](http://irclog.perlgeek.de/perl6/2016-08-09#i_12993090)��

```perl6
slurp.words.Bag.sort(-*.value).fmt("%10s3d\n").say;
```

`slurp` ��� STDIN �ж�ȡ���� "file" ������һ�� Str������ `Str::words` �ᰴ��ĳ�� Unicode ����ĵ��ʰѸ��ַ����ָ��һ���б����б�ǿתΪ Bag �򴴽�һ������ Hash, �������±����Ŀ�ݷ�ʽ��

```perl6
my %h;
%h{$_}++ for <peter paul marry>;
dd %h;

# # OUTPUT?Hash %h = {:marry(1), :paul(1), :peter(1)}??
```

�ڹ��������ϵ��� `.sort(-*.value)` �ᰴ��ֵ�Ľ������򲢷���һ�������� Pairs �б�List::fmt ����� Pair::fmt, ������ fmt ����, .key ��Ϊ��ڶ�������, .value Ҳ��Ϊ������say ���ʹ��һ���ո����Ӹ���Ԫ�ز��������׼��������һ����һ�������Ϊ���˵�һ��֮���ÿһ��ǰ�涼����һ������Ŀո�

```perl6
slurp.words.Bag.sort(-*.value).fmt("%10s => %3d").join("\n").say;
```

�ֶ������ַ������á�����ڼ�̵ĵ��г�����˵�е���ˡ�������Ҫ�ҵ���ĵ��ʲ�ʹ�� `.chars` ����ȡ�п�


slurp ���� `$*IN` ���ϵ��� `.slurp-rest` ������

```perl6
$*IN = <peter paul marry peter paul paul> but role { method slurp-rest { self.Str } };
```

����һ�� hack ��Ϊ�������κ���ʽ�����ͼ����ʧ�ܲ��������� slurp ֮�ⲻ����κζ��������á�����, ʵ�������Ǵ� `$*IN` ������ STDIN����Ҫ�ڹ�����ʹ������������ɡ�


���������ܿ��ĵ����ɲ���ʼ�����ˡ�

```perl6
my %counted-words = slurp.words.Bag;
my $word-width = [max] %counted-words.keys?.chars;
```


���Ҽ��������ӶϿ��ĵط�������

```perl6
%counted-words.sort(-*.value).fmt("%{$word-width}s3d").join("\n").say;
```

�������˵��Ǻܳ�ª�����ǰ�һ�����г�����ˡ��������޸� fmt ��ʹ���ٴ�������

������Ҫ����һ�� fmt ����, ������һ��λ�õ�(Positional), һ�� printf ���ĸ�ʽ�ַ�����һ����ʽ�ַ����е� block per `%*`������, ���ǿ�����Ҫ�� self.fmt ǰ�����һ���ָ�����

```perl6
my multi method fmt(Positional:D: $fmt-str, *@width where *.all ~~ Callable, :$separator = " "){
    self.fmt(
        $fmt-str.subst(:g, "%*", {
            my &width = @width[$++] // Failure.new("missingh block");
            '%' ~ (&width.count == 2 ?? width(self, $_) !! width(self))
        }), $separator);
}
```

���ʽ `*.all ~~ Callable` ��� [slurp array](https://docs.perl6.org/type/Signature#Slurpy_(A.K.A._Variadic)_Parameters)�е�����Ԫ���Ƿ�ʵ���� CALL-ME(����ʵ�ʱ�ִ�еķ�������ִ�� foo()��ʱ��)��

Ȼ�������ڸ�ʽ�ַ�����ʹ���� `subst` ���滻 `%*`, �滻��һ��(�հ�)���, ��ÿ��ƥ�䱻����һ�Ρ�������������в���Ĺ��÷���

```perl6
say "1-a 2-b 3-c".subst(:g, /\d/, {<one two three>[$++]});
# one-a two-b three-c
```

����״̬���� `$` �� 0  ��ʼ����, ÿ�δ����ִ��ʱ�� 1��ʵ����������������ľ����Ƴ�һ��ѭ������ subst ͵͵����һ������ļ������������±ꡣ���߿���˵����ע����һ���������� subst �����ѭ���С����˿��ܻ����� subst Ӧ�ý���һ�� Seq ��Ϊ���ĵڶ���λ�ò���, �����õ��ñ���߳����������, ���ǰѶ������ˡ�

�ڵ� 11 ��, ���Ǵ������������ó�һ��Ԫ�ػ�����û��Ԫ��ʱ����һ�� Failure�����ǰ� block �洢��һ����������Ϊ�������ڵ� 12 ������ʡ������Ǹ� block ��������λ�ò���,we feed the topic subst is calling the block with as a 2nd parameter to our stored block. ��������һ�� Match ���Ҷ���Ӱ����ƥ��Ķ����������á�������������������Ƕ� `%*` ����ƥ�䲢�ҵ�ǰλ���� `$++` �����������Ǹ�֮�����ǵõ���һ����ʽ�ַ���, ������һ�����û��ṩ�� fmt �汾���п������

�û��ṩ�Ŀ��ʹ��һ�� Pairs ���á����ǲ��ò�����һ���Եõ�����ļ���

```perl6
{[max] .values?.keys?.chars}
```  

�õ���һ�е��п�


```perl6
print %counted-words.sort(-*.value).&fmt("%*s3d", {[max] .values?.keys?.chars}, separator => "\n");
```


�Ǹ�ʱ�ֵ� `.&fmt` �����Ǳ������Ϊ������ѵĸ��㷽������ List �ķ�����

-- ����ĺ��á�