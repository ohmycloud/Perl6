[Execute an external program with timeout in Perl6](http://ks0608.hatenablog.com/entry/2016/05/17/001826)

Proc::Async ���������첽��ִ���ⲿ����

```perl6
my $proc = Proc::Async.new("curl", "-s", "-o", "index.html", "http://www.cpan.org");
my $res = await $proc.start;
```

���ǿ����� Proc::Async ��ʹ�ó�ʱ��? Proc::Async û����ʽ֧�ָù��ܣ��������ǿ��Ժ����׵�ʵ��������һ�������

```perl6
class Proc::Async::Timeout is Proc::Async {
    has $.timeout is rw;
    method start($self: |) {
        return callsame unless $.timeout;
        my $killer = Promise.in($.timeout).then: { $self.kill };
        my $promise = callsame;
        Promise.anyof($promise, $killer).then: { $promise.result };
    }
}

my $proc = Proc::Async::Timeout.new("perl", "-E", "sleep 5; warn 'end'");
$proc.timeout = 1;
my $res = await $proc.start;
say "maybe timeout" if $res.signal;
```

������������������

```perl6
my $start = Proc::Async.^methods.first(*.name eq "start");

$start.wrap: method ($self: :$timeout, |c) {
    return callwith($self, |c) unless $timeout;
    my $killer = Promise.in($timeout).then: { $self.kill };
    my $promise = callwith($self, |c);
    Promise.anyof($promise, $killer).then: { $promise.result };
};

say await Proc::Async.new("perl", "-E", 'sleep 3; say "end"').start(timeout => 2);
```

�ۣ��ۣ�����㷢���˸���ķ�����������ҡ�