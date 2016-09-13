# Perl 6 sheets

cheat sheet v4                                                »ö«

```perl

SIGILS    MAJOR/MINOR CONTEXTS         ACCESS ARRAYS      HASHES
$scalar   item   list       sink       whole: @array[]    %hash{}
@array     Str    flat/lol           element: @array[0]   %hash{'a'}
%hash      Num    lazy/eager/hyper/race  (or)             %hash<a>
&code      Bool                        slice: @array[0,2] %hash{'a','b'}
                       COMPOSERS         (or)             %hash<a b>
TWIGILS                [ ] array
$normal-lexical        { } block/hash     AUTOMATIC DEREFERENCE
$?compiler-constant    < > quotewords     &($foo)(1,2)     == $foo(1,2)
$*dynamic-or-global    (,) parcel         @($foo)[1]       == $foo[1]
$.public-accessor      :() signature      %($foo){'bar'}   == $foo<bar>
$!private-attribute    \() capture        @(@($foo)[1])[2] == $foo[1][2]
$^positional-param
$:named-parameter         CONTROL SYNTAX
$=pod-info                for LIST { }                # implicit $_ arg
$<named-match-capture>    for LIST -> $a, $b { }      # explicit args
$~slang-variable          while/until EXPR { }
                          repeat while/until EXPR { } # do at least once
OPERATOR PRECEDENCE       loop { }   loop (a;b;c) { } # parens required!
.method .[] i             if EXPR { } elsif EXPR { } else { }
++ --                     unless EXPR { }             # no else allowed!
**                        given EXPR { when EXPR { } default { } }
unary + - ~ ! ? ^         EXPR if EXPR for LIST;      # list comprehension
* / % %% div              next, last, redo            # loop controls
+ -                       proceed, succeed            # switch controls
x xx       TYPES
~          Bool Bit Int Rat FatRat UInt Num Complex int32 complex64 etc.
&          Str Cat Blob Char Byte Codepoint Grapheme Buf buf8 buf32 utf8
| ^                    IO Mu Any Cool Junction Supply Whatever Match Nil
temp let                                        Parcel Capture Signature
<=> leg cmp .. but          SCOPE DECLARATORS     Pair Range Set Bag Mix
~~ > == gt eq === eqv !op   my  lexical scope    SetHash BagHash MixHash
&&                         our  package scope     Scalar Array Hash Code
|| ^^ // min max           has  instance scope       Enum Order TrigBase
??!! ff                   anon  no scope at all        Block Routine Sub
= := op= =>              state  persistent lexical   Method Regex Cursor
so not                 augment  benign parasitic       Failure Exception
, :                  supersede  deadly parasitic        Instant Duration
X Xop Z Zop ...                                            Date DateTime
say die map etc.  OPERATOR DOMAINS
and               Numeric: ==  !==(!=) + <      >     <=> <=     >=
or xor            Stringy: eq  !eq(ne) ~ lt     gt    leg le     ge
<== ==>             Value: eqv  !eqv     before after cmp !after !before
                 ObjectID: ===  !===
METAOPERATORS                         LINKS      IRC
[op] reduce listop to A op B op C...  perl6.org  #perl6 irc.freenode.net
op=  A = A op B                       rakudo.org #parrot irc.perl.org
!op  !(A op B)
»op« hyper/vectorize    REGEX  METACHARS          REGEX MODIFIERS
Zop  zip with op        ^ $    string begin/end   :i   ignore case
Xop  cross with op      ^^ $$  line begin/end     :m   ignore marks
Rop  reverse args       +      one or more        :g   global
Sop  sequentialize      *      zero or more       :r   ratchet
                        ?      zero or one        :s   sigspace
SPECIAL VARIABLES       **1..3 repeat in range    :4th nth occurrence
$_     current topic    ()     capture to $0,$1   :4x  n times
$/     regex result     []     no capture
$!     error object     <foo>  subrule          REGEX CHARCLASSES
@*ARGS command line     <[]>   character class  .  == anychar, \N non \n
@*INC  include path     |      parallel or      \s == <space>, \S non
%*ENV  environment      ||     serial or        \d == <digit>, \D non
$*PID  process id       « »    word boundary    \w == <+alpha+digit+[_]>l
```
