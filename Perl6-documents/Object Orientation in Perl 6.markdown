 Perl 6 Documentation
Search 

Object Orientation in Perl 6
Using Objects
Type Objects
Classes
Attributes
Methods
self
Private Methods
Submethods
Inheritance
Object Construction
Roles
Role Application
Stubs
Meta-Object Programming and Introspection
Introspection
can
methods
mro
name
parents

Perl 6 is an object oriented language at its core, even though it allows you to write programs in other programming styles.

Perl 6 comes with a wealth of predefined types, which can be classified in two categories: normal and native types.

Native types are used for low-level types (like uint64 ). They do not have the same capabilities as objects, though if you call methods on them, they are boxed into normal objects.

Everything that you can store in a variable is either a native value, or an object. That includes literals, types (type objects), code and containers. Using Objects

You can use objects by calling methods on them. To call a method on an expression, add a dot, followed by the method name, optionally followed by its argument list in round parenthesis (note that no whitespace is allowed between the method name and the argument list):
say "abc".uc;                   
#        ^^^ method call without arguments
my @words = $string.comb(/\w+/);
#                  ^^^^^^^^^^^^ method call with one argument

Another method call syntax separates the method name and the argument list with a colon:
say @*INC.join: ':';

Many operations that don't look like method calls (for example smart matching, interpolating an object into a string) result in method calls under the hood.

Methods can return mutable containers, in which case you can assign to the return value of a method call.
$*IN.input-line-separator = "\r\n";

All objects support the methods from class Mu , which is the root of the type hierarchy. Type Objects

Types themselves are objects, and you can get the type object simply by writing its name:
my $int-type-obj = Int;

You can ask any object for its type object by calling the WHAT method (which is actually a macro in method form):
my $int-type-obj = 1.WHAT;

Type objects (other than Mu ) can be compared for equality with the === identity operator:
sub f(Int $x) {
    if $x.WHAT === Int {
        say 'you passed an Int';
    }
    else {
        say 'you passed a subtype of Int';
    }
}

Subtype checking is done by smart-matching:
if $type ~~ Real {
    say '$type contains Real or a subtype thereof';
} Classes

Classes are declared using the class keyword, typically followed by a name.
class Journey {
}

This declaration results in a type object being created and installed in the current package and current lexical scope under the name Journey . You can also declare classes lexically:
my class Journey {
}

This restricts their visibility to the current lexical scope, which can be useful if the class is an implementation detail nested inside a module or another class. Attributes

Attributes are variables that exist per instance of a class. They are where the state of an object is stored. In Perl 6, all attributes are private. They are typically declared using the has declarator and using the ! twigil.
class Journey {
    has $!origin;
    has $!destination;
    has @!travellers;
    has $!notes;
}

While there is no such thing as a public (or even protected) attribute, there is a way to have accessor methods generated automatically: replace the ! twigil with the . twigil (the . should remind you of method call).
class Journey {
    has $.origin;
    has $.destination;
    has @!travellers;
    has $.notes;
}

This defaults to providing a read-only accessor. In order to allow changes to the attribute, add the rw trait:
class Journey {
    has $.origin;
    has $.destination;
    has @!travellers;
    has $.notes is rw;
}

Since classes inherit a default constructor from Mu and we have requested that some accessor methods are generated for us, our class is already somewhat functional.
# Create a new instance of the class.
my $vacation = Journey.new(
    origin      => 'Sweden',
    destination => 'Switzerland',
    notes       => 'Pack hiking gear!'
);
# Use an accessor; this outputs Sweden.
say $vacation.origin;
# Use an rw accessor to change the value.
$vacation.notes = 'Pack hiking gear and sunglasses!';

Note that the default constructor will only set attributes that have an accessor method. Methods

Methods are declared with the method keyword inside of a class body.
class Journey {
    has $.origin;
    has $.destination;
    has @!travellers;
    has $.notes is rw;
        
    method add_traveller($name) {
        if $name ne any(@!travellers) {
            push @!travellers, $name;
        }
        else {
            warn "$name is already going on the journey!";
        }
    }
        
    method describe() {
        "From $!origin to $!destination"
    }
}


A method can have a signature, just like a subroutine. Attributes can be used in methods, and can always be used with the ! twigil, even if they are declared with the . twigil. This is because really, the . twigil declares an attribute with the ! twigil in its place, and then additionally generates an accessor method.

There is a subtle but important difference between using, say, $!origin and $.origin in the method describe . The first is always a simple lookup of the attribute. It is cheap, and you know that it is the attribute declared in this class. The latter is really a method call, and thus it may be overridden in a subclass. Only use $.origin if you explicitly want to allow overriding. self

Inside a method, the term self is available, which is bound to invocant, i.e. the object that the method was called on. self can be used to call further methods on the invocant, for example. Private Methods

Methods for internal usage inside the class that cannot be called from anywhere else are introduced with an exclamation mark ! before the method name, and are called with the exclamation mark instead of the dot:
method !do-something-private($x) {
    ...
}
method public($x) {
    if self.precondition {
        self!do-something--private(2 * $x)
    }
}

Private methods are not inherited to subclasses. Submethods

A submethod is public method that is not inherited to subclasses. The name stems from the fact that they are semantically similar to subroutines.

Submethods are useful for object construction and destruction tasks, as well as for tasks that are so specific to a certain type that subtypes must certainly override them.

TODO: example Inheritance

Classes can have parent classes .
class Child is Parent1 is Parent2 { }

If a method is called on the child class, and the child class does not provide that method, the parent classes' method of that name is invoked instead, if it exists. The order in which parent classes are consulted is called the method resolution order (MRO). Perl 6 uses the C3 method resolution order . You can ask a type for its MRO through a call to its metaclass:
say Parcel.^mro;    # Parcel() Cool() Any() Mu()

If a class does not specify a parent class, Any is assumed by default. All classes directly or indirectly derive from Mu , the root of the type hierarchy.

All calls to public method are "virtual" in the C++ sense, which means that the actual type of an object determines which method to call, not the declared type. Object Construction

Objects are generally created through methods calls, either on the type object or on another object of the same type.

Class Mu provides a constructor method called new , which takes named arguments and uses them to initialize public attributes.
class Point {
    has $.x;
    has $.y = 2 * $!x;
}
my $p = Point.new( x => 1, y => 2);
#             ^^^ inherited from class Mu

Mu.new calls method bless on its invocant, passing along all the named arguments. bless creates the new object, and then calls method BUILDALL on it. BUILDALL walks all subclasses in reverse method resolution order (i.e. from Mu to most derived classes), and in each class checks for existence of a method named BUILD . If it exists, it is called, again passing all named arguments from method new to it. If not, the public attributes from this class are initialized from named arguments of the same name. In either case, if neither BUILD nor the default mechanism has initialized the attribute, default values are applied (the 2 * $!x in the example above).

This object construction scheme has several implications for customized constructors. First, custom BUILD methods should always be submethods, otherwise they break attribute initialization in subclasses. Second, BUILD submethods can be used to run custom code at object construction time. They can also be used for creating aliases for attribute initialization:
class EncodedBuffer {
    has $.enc;
    has $.data;

    submethod BUILD(:encoding(:$enc), :$data) {
        $!enc :=  $enc;
        $!data := $data;
    }
}
my $b1 = EncodedBuffer.new( encoding => 'UTF-8', data => [64, 65] );
my $b2 = EncodedBuffer.new( enc      => 'UTF-8', data => [64, 65] );
#  both enc and encoding are allowed now


Since passing arguments to a routine binds the arguments to the parameters, a separate binding step is unnecessary if the attribute is used as parameter. So the example above could also have been written as:
submethod BUILD(:encoding(:$!enc), :$!data) {
    # nothing to do here anymore, the signature binding
    # does all the work for us.
}

The third implication is that if you want a constructor that accepts positional arguments, you must write your own new method:
class Point {
    has $.x;
    has $.y;
    method new($x, $y) {
        self.bless(*, :$x, :$y);
    }
}

However this is considered poor practice, because it makes correct initialization of objects from subclasses harder. Roles

Roles are in some ways similar to classes, in that they are a collection of attributes and methods. They differ in that roles are also meant for describing only parts of an object's behavior, and in how roles are applied to classes. Or to phrase it differently, classes are meant for managing instances, and roles are meant for managing behavior and code reuse.
role Serializable {
    method serialize() {
        self.perl; # very primitive serialization
    }
    method deserialization-code($buf) {
        EVAL $buf; # reverse operation to .perl
    }
}

class Point does Serializable {
    has $.x;
    has $.y;
}
my $p = Point.new(:x(1), :y(2));
my $serialized = $p.serialize;      # method provided by the role
my $clone-of-p = Point.deserialization-code()($serialized);
say $clone-of-p.x;      # 1


Roles are immutable as soon as the compiler parses the closing bracket of the role declaration. Role Application

Role application differs significantly from class inheritance. When a role is applied to a class, the methods of that role are copied into the class. If multiple roles are applied to the same class, conflicts (e.g. non-multi methods of the same name) cause a compile-time error, which can be solved by providing a method of the same name in the class.

This is much safer than multiple inheritance, where conflicts are never detected by the compiler, but are instead simply resolved to the superclass that appears earlier in the MRO, which might or might not be what the programmer wanted.

# TODO: example

When a role is applied to a second role, the actual application is delayed until the second class is applied to a class, at which point both roles are applied to the class. Thus
role R1 {
    # methods here
}
role R2 does R1 {
    # methods here
}
class C does R2 { }

produces the same class C as
role R1 {
    # methods here
}
role R2 {
    # methods here
}
class C does R2 does R1 { } Stubs

When a role contains a stubbed method, a non-stubbed version of a method of the same name must be supplied at the time the role is applied to a class. This allows you to create roles that act as abstract interfaces.
role AbstractSerializable {
    method serialize() { ... }  # literal ... here marks the
                                # method as a stub
}

# the following is a compile time error, for example
#        Method 'serialize' must be implemented by Point because
#        it is required by a role
class APoint does AbstractSerializable {
    has $.x;
    has $.y;
}

# this works:
class SPoint does AbstractSerializable {
    has $.x;
    has $.y;
    method serialize() { "p($.x, $.y)" }
}


The implementation of the stubbed method may also be provided by another role.

TODO: parameterized roles Meta-Object Programming and Introspection

Perl 6 has a meta object system, which means that the behavior of objects, classes, roles, grammars, enums etc. are themselves controlled by other objects; those objects are called meta objects . Meta objects are, like ordinary objects, instances of classes, in this case we call them meta classes .

For each object or class, you can get the meta object by calling .HOW on it. Note that although this looks like a method call, it is actually special-cased in the compiler, so it is more like a macro.

So, what can you do with the meta object? For one you can check if two objects have the same meta class by comparing them for equality:
say 1.HOW ===   2.HOW;      # True
say 1.HOW === Int.HOW;      # True
say 1.HOW === Num.HOW;      # False

Perl 6 uses the word HOW , Higher Order Workings, to refer to the meta object system. Thus it should be no surprise that in Rakudo, the class name of the meta class that controls class behavior is called Perl6::Metamodel::ClassHOW . For each class there is one instance of Perl6::Metamodel::ClassHOW .

But of course the meta model does much more for you. For example it allows you to introspect objects and classes. The calling convention for methods on meta objects is to call the method on the meta object, and pass in the object of interest as first argument to the object. So to get the name of the class of an object, you could write:
my $object = 1;
my $metaobject = 1.HOW;
say $metaobject.name($object);      # Int
# or shorter:
say 1.HOW.name(1);                  # Int

(The motivation is that Perl 6 also wants to allow a more prototype-based object system, where it's not necessary to create a new meta object for every type).

To get rid of using the same object twice, there is a shortcut:
say 1.^name;                        # Int
# same as
say 1.HOW.name(1);                  # Int Introspection

Introspection is the process of getting information about an object or class at runtime. In Perl 6, all introspection goes through the meta object. The standard ClassHOW for class-based objects offers these facilities: can

Given a method names, it returns a Parcel of methods that are available with this name.
class A      { method x($a) {} };
class B is A { method x()   {} };
say B.^can('x').elems;              # 2
for B.^can('x') {
    say .arity;                     # 1, 2
}

In this example, class B has two possible methods available with name x (though a normal method call would only invoke the one installed in B directly). The one in B has arity 1 (i.e. it expects one argument, the invocant ( self )), and the one in A expects 2 arguments ( self and $a ). methods

Returns a list of public methods available on the class (which includes methods from superclasses and roles). By default this stops at the classes Cool , Any or Mu ; to really get all methods, use the :all adverb.
class A {
    method x() { };
}
say A.^methods();                   # x
say A.^methods(:all);               # x infinite defined ...

The returned list actually returns objects of type Method , which you can use to introspect their signatures, and even to call them. mro

Returns the list of the class itself and its superclasses in method resolution order. Perl 6 linearizes the graph of superclasses into a single list that $object.^mro returns; when a method is called, the class and its parent classes are visited in that order. (Only conceptually; actually the list of methods is built at class composition time).
say 1.^mro;                         # (Int) (Cool) (Any) (Mu) name

Returns the name of the class:
say 'a string'.^name;               # Str parents

Returns the list of parent classes. By default it stops at Cool , Any or Mu , which you can suppress by supplying the :all adverb. With :tree , a nested list is returned.
class D { };
class C1 is D { };
class C2 is D { };
class B is C1 is C2 { };
class A is B { };
say A.^parents(:all).perl;          # (B, C1, C2, D, Any, Mu)
say A.^parents(:all, :tree).perl;
    # ([B, [C1, [D, [Any, [Mu]]]], [C2, [D, [Any, [Mu]]]]],)


Generated on 2014-03-22T13:18:49-0400 from the sources at perl6/doc on github . This is a work in progress to document Perl 6, and known to be incomplete. Your contribution is appreciated.

The Camelia image is copyright 2009 by Larry Wall.
