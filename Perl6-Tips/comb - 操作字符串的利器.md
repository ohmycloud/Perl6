comb - �����ַ���������

## comb ������

comb �����̵Ķ���Ϊ��

```perl6
multi sub    comb(Regex $matcher, Str(Cool) $input, $limit = *) returns List:D
multi method comb(Regex $matcher,                   $limit = *) returns List:D
```

�÷���

```perl6
comb /PATTERN/, STRING, LIMIT?  # ��������ʽ
STRING.comb(/PATTERN/, LIMIT?)  # ������ʽ
```

���ص����ߣ�������ʽ�������У��������� $limit ��������ṩ�˵Ļ���ƥ�䣬���߷��صڶ���������sub ��ʽ���� Regex ��ƥ����ַ����б�

```perl6
say "6 or 12".comb(/\d+/).join(", "); # 6, 12
```

## Str ���е� comb

```perl6
multi sub    comb(Str:D   $matcher, Str:D $input, $limit = Inf)
multi sub    comb(Regex:D $matcher, Str:D $input, $limit = Inf, Bool :$match)
multi sub    comb(Int:D $size, Str:D $input, $limit = Inf)

multi method comb(Str:D $input:)
multi method comb(Str:D $input: Str:D   $matcher, $limit = Inf)
multi method comb(Str:D $input: Regex:D $matcher, $limit = Inf, Bool :$match)
multi method comb(Str:D $input: Int:D $size, $limit = Inf)
```

�� `$input` ������ `$matcher` ����������ƥ�䣨Ĭ���� Str�������� Match ������� `$match` Ϊ��Ļ�����һ���б�`$limit` ��ʾ���෵�� `$limit` ��ƥ�䡣

���û���ṩ `$matcher`(ƥ����)�� ��ô�᷵���ַ����е������ַ����б��ȼ���ʹ���� `$matcher = rx/./`��

���ӣ�

```perl6
comb(/\w/, "a;b;c").perl;        # ("a", "b", "c").list
comb(/\N/, "a;b;c").perl;        # ("a", ";", "b", ";", "c").list
comb(/\w/, "a;b;c", 2).perl;     # ("a", "b").list
comb(/\w\;\w/, "a;b;c", 2).perl; # ("a;b",).list

"123abc456def".comb(3)           # (123 abc 456 def)
"123abc456def".comb(3,2);        # (123 abc)
```

���ƥ������matcher����һ������ֵ����ô������Ϊ�� `/. ** matcher/` ���ƣ������������ 30 ����