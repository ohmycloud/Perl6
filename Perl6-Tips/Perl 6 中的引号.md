���� q �� qq ֮�⣬���ڻ���һ�ֻ�����ʽ�� Q����������в�ֵ��������ʽ���޸��������������ԣ�q ʵ������ Q:q �ļ�ƣ�qq ʵ������ Q:qq �ļ�ơ�ʵ�������е� quote-like ��ʽ�������Դ��и��ʵ� Q ��ʽ��


[S02-literals/quoting.t lines 95�C116](https://github.com/perl6/roast/blob/master/S02-literals/quoting.t#L95-L116)
[S02-literals/quoting.t lines 132�C139](https://github.com/perl6/roast/blob/master/S02-literals/quoting.t#L132-L139)

```perl6
q//         Q :q //
qq//        Q :qq //
rx//        Q :regex //
s///        Q :subst ///
tr///       Q :trans ///
```

���� `:regex` �ĸ���ͨ��ת������ͬ�Ľ������ı������ԵĽ�����������ȫ�ı��κ�֮��ĸ��ʻ���������Ķ�������Ľ��͡�

```perl6
q:s//       Q :q :scalar //
rx:s//      Q :regex :scalar //
```

���� `q[...]` ӵ�м�д��ʽ�� '...', ���� `qq[...]` ӵ�м�д��ʽ�� "..." һ���������� `Q[...]` ����Ҳ��һ��ʹ�ð������ ?...? �Ķ���ʽ��

## �����ϵĸ���


�����ϵ��������ڿ��Խ��ո����ˣ�
[S02-literals/quoting.t lines 210�C223](https://github.com/perl6/roast/blob/master/S02-literals/quoting.t#L210-L223)
  [S02-literals/quoting.t lines 55�C69](https://github.com/perl6/roast/blob/master/S02-literals/quoting.t#L55-L69)
 [S02-literals/quoting.t lines 427�C501](https://github.com/perl6/roast/blob/master/S02-literals/quoting.t#L427-L501)

```perl6
Short       Long            Meaning
=====       ====            =======
:x          :exec           ��Ϊ����ִ�в����ؽ��
:w          :words          �����ʷָ���(û�����ű���)
                                     

:ww         :quotewords     �����ʷָ��� (�������ű���)
:v          :val            Evaluate word or words for value literals
:q          :single         ��ֵ \\, \q �� \' (or whatever)
                                    

:qq         :double         ʹ�� :s, :a, :h, :f, :c, :b ���в�ֵ
:s          :scalar         ��ֵ $ vars
:a          :array          ��ֵ @ vars
:h          :hash           ��ֵ % vars

                        
:f          :function       ��ֵ & ����
:c          :closure        ��ֵ {...} ���ʽ
:b          :backslash      ��ֵ \n, \t, ��. (���ٰ�ʾ�� :q )
:to         :heredoc        �ѽ������Ϊ heredoc ��ֹ��
            :regex          ����Ϊ������ʽ
            :subst          ����Ϊ�û� (substitution)
            :trans          ����Ϊת�� (transliteration)
            :code           Quasiquoting
:p          :path           ����һ�� Path ���� (�鿴 S16 ��ȡ����ѡ��)
```

ͨ���ڿ�ͷ����һ�����ж���ʽ�ĵ������ʵ� Q��q���� qq�������ʡ�Ե���һ��ð�ţ��������������ʽ��

```perl6
qw /a b c/;                         # P5-esque qw// meaning q:w
Qc '...{$x}...';                    # Q:c//, interpolate only closures
qqx/$cmd @args[]/                   # equivalent to P5's qx//
```
(ע�� qx// ����ֵ)

��������һ����д����ô����һ���꣺

```perl6
macro qx { 'qq:x ' }          # equivalent to P5's qx//
macro qTO { 'qq:x:w:to ' }    # qq:x:w:to//
macro quote:<? ?> ($text) { quasi { {{{$text}}}.quoteharder } }
```

���д�д�ĸ��ʱ����������û���������š������� Latin-1 ����� Unicode �ָ��������������û���������š�
[S02-literals/quoting.t lines 352�C426](https://github.com/perl6/roast/blob/master/S02-literals/quoting.t#L352-L426)

��������������������һ�����ۣ�����������˵��

```perl6
 %hash = qw:c/a b c d {@array} {%hash}/;
```
��

```perl6
%hash = qq:w/a b c d {@array} {%hash}/;
```

�Ѷ���(items)��ֵ�� qw �С�Ĭ�ϵأ������ɢ���ڲ�ֵʱֻ���пո�ָ���������֮��İ��ո�ָ��Ծ��ܹ��������������õ�  ?...?  �����Զ������˵ȼ��� `qq:ww:v/.../` �Ĳ�ֵ���� ���õ� `<...>` �ȼ��� `q:w:v/.../`��