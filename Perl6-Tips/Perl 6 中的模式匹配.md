### ģʽƥ��

```perl6
my $name = "twostraws";

given $name {
  when "bilbo"      { say "Hello, Bilbo Baggins!"}
  when "twostraws"  { say "Hello, Paul Hudson!"  }
  default           { say "�����֤ʧ��"           }
}
```


### ͬʱ������ֺ�����

```perl6
my $name     = "twostraws";
my $password = "fr0st1es";

given ($name, $password) {
  when ("bilbo", "bagg1n5")      { say "Hello, Bilbo Baggins!" }
  when ("twostraws", "fr0st1es") { say "Hello, Paul Hudson!"   }
  default                        { say "����˭?"                }
}
```


### ʹ�õ���Ԫ��
```perl6
my $authentication = ("twostraws", "fr0st1es");

given $authentication {
  when ("bilbo", "bagg1n5")      { say "Hello, Bilbo Baggins!" }
  when ("twostraws", "fr0st1es") { say "Hello, Paul Hudson!"   }
  default                        { say "����˭?"                }
}
```


### ����ƥ��
```perl6
# ��ֻ����ĳЩ����Ȥ��ֵ������������ֵ��ʹ�� `*` �Ż� `$` ������ "any value is fine"
my $authentication = ("twostraws", "fr0st1es", "127.0.0.1");
given $authentication {
  when ("bilbo", "bagg1n5", *)      { say "Hello, Bilbo Baggins!"}
  when ("twostraws", "fr0st1es", $) { say "Hello, Paul Hudson!"  }
  default                           { say "Who are you?"         }
}
```


### ֻƥ��Ԫ���һ����
```perl6
# ����Ȼ��֪������������ʲô
my $authentication = ("twostraws", "fr0st1es");
given $authentication {
  when ("bilbo", *)     { say "Hello, Bilbo Baggins!" }
  when ("twostraws", *) { say "Hello, Paul Hudson: your password was $_!" }
  default               { say "Who are you?"         }
}
```


### ƥ�������Ԫ��
```perl6
sub fizzbuzz(Int $number) returns Str {
    given ($number % 3 == 0, $number % 5 == 0) {
      when (True, False)  { return "Fizz"     }
      when (False, True)  { return "Buzz"     }
      when (True, True)   { return "FizzBuzz" }
      when (False, False) { return $number.Str}
    }
}

say fizzbuzz(15);
```


### ����Ԫ��
```perl6
my $twostraws = ("twostraws", "fr0st1es");
my $bilbo = ("bilbo", "bagg1n5");
my $taylor = ("taylor", "fr0st1es");
my @users = $twostraws, $bilbo, $taylor;

for @users -> $user {
    say $user[0];
}
```

### ʹ�� when ƥ��Ԫ���е�ָ��ֵ
```perl6
my $twostraws = ("twostraws", "fr0st1es");
my $bilbo = ("bilbo", "bagg1n5");
my $taylor = ("taylor", "fr0st1es");
my @users = $twostraws, $bilbo, $taylor;

say "User twostraws has the password fr0st1es" when ("twostraws", "fr0st1es") for @users;

# ��ӡ����Ϊָ��ֵ���û�
say "User $_[0] has password \"fr0st1es\"" when (*, "fr0st1es") for @users;
```


### ƥ�䷶Χ
```perl6
my $age = 36;

given $age {
  when 0 ..^ 18   { say "���л�����ʱ�䣬����ûǮ"  }
  when 18 ..^ 70  { say "���л�����Ǯ������ûʱ��"  }
  default         { say "����ʱ��ͽ�Ǯ������û����"}
}
```


### when �����������ƥ������� ~~ ����ʹ��
```perl6
my $age = 36;
when $age ~~ 0 ..^ 18  { say "���л�����ʱ�䣬����ûǮ"  } 
when $age ~~ 18 ..^ 70 { say "���л�����Ǯ������ûʱ��"  }
default                { say "����ʱ��ͽ�Ǯ������û����"}
```

### ʹ�� contains ����
```perl6
my $age = 36;
when (0 ..^ 18).contains($age)  { say "���л�����ʱ�䣬����ûǮ"  } 
when (18 ..^ 70).contains($age) { say "���л�����Ǯ������ûʱ��"  }
default                         { say "����ʱ��ͽ�Ǯ������û����"}
```

### ƥ��Ԫ���еķ�Χ
```perl6
my $user = ("twostraws", "fr0st1es", 36);
given $user {
  my $name = $user[0];
  when ($name, *, 0 ..^ 18)  { say "$name �л�����ʱ�䣬����ûǮ"  } 
  when ($name, *, 18 ..^ 70) { say "$name �л�����Ǯ������ûʱ��"  }
  when ($name, *, *)         { say "$name ��ʱ��ͽ�Ǯ,����û����" }
}
```


### ö��
```perl6
enum WeatherType <Cloudy Sunny Windy>;
my $today = WeatherType::Cloudy;
given $today {
  when WeatherType::Cloudy { say "����" }
  when WeatherType::Sunny  { say "����" }
  when WeatherType::Windy  { say "�з�" }
}

# ʹ�� if ���
if $today ~~ WeatherType::Cloudy { say "����" }
```


### ����ֵ
```perl6
enum WeatherType  (
    Cloudy => 100,
    Sunny  => 50,
    Windy  => 30
);

my $today = WeatherType::Windy;
given $today {
  when WeatherType::Cloudy { say 20*Cloudy }
  when WeatherType::Sunny  { say 10*Sunny  } 
  when WeatherType::Windy  { say 12*Windy  }
}
```


### when �Ӿ�
```perl6
my @numbers = 1..10;
.say when $_ % 2 == 1 for @numbers;

my @celebrities = "Michael Jackson", "Taylor Swift", "MichaelCaine", "Adele Adkins", "Michael Jordan";
.say when /^Michael/ for @celebrities;     # ʹ��������ʽ
.say when $_.chars > 12 for @celebrities;  # ���÷���
.say when /^Michael/ and $_.chars >12 for @celebrities; # ��������
```