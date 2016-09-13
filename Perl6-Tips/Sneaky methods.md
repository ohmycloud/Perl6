[Sneaky methods](https://gfldex.wordpress.com/2016/07/20/sneaky-methods/)

�������������, ����Ķ����п��������Ͷ��巽�������������ߵ������ĵ��ж������ἰ���� `my` �ؼ�����������Ѹ��㷽��������Ϊʲô����Ҫ:

```perl6
my method foo(SomeClass:D:) { self }
```

���ԵĴ���[Ԫ����Э��](https://docs.perl6.org/language/mop)�е� [add_method](https://docs.perl6.org/type/Metamodel$COLON$COLONMethodContainer#method_add_method) ����, �� Rakudo �������ҵ�����

```perl6
src/core/Bool.pm
32:    Bool.^add_method('pred',  my method pred() { Bool::False });
33:    Bool.^add_method('succ',  my method succ() { Bool::True });
35:    Bool.^add_method('enums', my method enums() { self.^enum_values });
```
���ַ�����������һ�ָ�������÷�������ܺ���֪������ʽ���������о���������ʲô�����ǿ��Գ�����������Ǹ����ʽ������һ���̵ı���, ������ǵĵ���, ���Ҽ�����ʽ���á��õ����ֺ���Ҫ���Ұ������˷���һ���̱�����û�б�Ҫ��

```perl6
<a b c>.&(my method ::(List:D) { dd self; self } ).say;

# output
# ("a", "b", "c")
# (a b c)
```

û����ʽ�������ǾͲ���û������, ��Ϊ Perl 6 ����������������, ��������ʹ���˿յ������� `::` ��ʹ���������ˡ�ʹ��һ�����ʵĵ���, ���ǾͲ���Ҫ���ˡ�����, �Ǹ������������� List �е�һԱ��������Ҫʹ�ú�׺ `.&` �������������������Ҫ���ʹ���Ǹ��������ǿ��԰���������������һ�����֡�

```perl6
my multi method debug(List:D:) { dd self; self };
<a b c>.&debug.say;

# output
("a", "b", "c")
(a b c)
```

����, �������������ص��Ļ�, ���ǿ��԰�����ΪĬ�ϲ�����ֵ��

```perl6
sub f(@l, :&debug = my method (List:D:){self}) { @l.&debug.say };
f <a b c>, debug => my method ::(List:D){dd self; self};

# output
#("a", "b", "c")
# (a b c)
```

�� Perl 6 �л��������еĶ���������, ����[����](https://docs.perl6.org/type/Method)�� �����������������һ��������������������ϲ�����κεط����ȥ��



















































