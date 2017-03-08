---
type: doc

layout: reference

category: "Syntax"

title: "Classes and Inheritance"

related:

​    - functions.md

​    - nested-classes.md

​    - interfaces.md

---

 

# Classes and Inheritance ( 클래스와 상속 )

 

## Classes ( 클래스 )

 

Kotlin의 클래스는  *class*{: .keyword } 키워드를 사용하여 선언합니다.

 

``` kotlin

class Invoice {

}

```

 

클래스의 선언은 클래스 이름, 클래스 헤더 그리고 `{}` 중괄호에 둘러싸인 class body로 선언됩니다. 

(매개 변수를 지정하고, 기본 생성자 등등..) 헤더와 본문은 모두 선택 사항입니다.

클래스에 body가 없는 경우, 괄호는 생략 될 수 있습니다.

 

``` kotlin

class Empty

```

 

 

### Constructors ( 생성자 )

 

Kotlin의 클래스는  **primary constructor** (초기 생성자)  와  하나이상의 **secondary constructors** , 부차적인 생성자를 가질 수 있습니다.

기본 생성자는 클래스 헤더의 일부입니다. 그것은 클래스 이름 (및 선택적 유형 매개 변수) 이후에 나옵니다.

 

``` kotlin

class Person constructor(firstName: String) {

}

```

 

만약 기본 생성자가  어떤 annotations도 가지고 있지 않거나 수정사항이 보이지 않는 경우,  *constructor*{: .keyword }

는 생략 될 수있습니다.

 

``` kotlin

class Person(firstName: String) {

}

```

 

기본 생성자는 코드를 포함 할 수 없습니다.

오직 *init*{: .keyword } 키워드가 접두어로 사용된 **initializer blocks** ( 초기화 블럭 ) 에  초기화 코드만 넣을 수 있습니다.

 

``` kotlin

class Customer(name: String) {

​    init {

​        logger.info("Customer initiallized with value ${name}")

​    }

}

```

 

기본 생성자 매개변수는 초기화 블록에서 사용할 수 있습니다. 또한 클래스 본문에 선언된 속성이나 initializers (초기화)도 사용할 수 잇있습니다. 

 

``` kotlin

class Customer(name: String) {

​    val customerKey = name.toUpperCase() // 대문자로 변경

}

```

 

사실, kotlin에는 기본 생성자에 속성을 선언하고 속성을 초기화하기 위한 더 간결한 구문을 사용할 수있습니다.

 

 

``` kotlin

class Person(val firstName: String , val lastName:String, var age:Int) {

​    // ...

}

```

 

일반 속성과 거의 같은 방식으로, 기본 생성자에서 선언 된 속성은 

변경가능한 *var*{: .keyword }이나 read-only 인 상수 *val*{: .keyword } 로 선언 할 수 있습니다.

 

만약 생성자가 annotations이나 수식어구 ( visibility modifiers )가 있다면,  *constructor*{: .keyword } 를 코드에 명시해야한다. 그리고 

수식어구는 명시한 constructor앞에 있어야합니다.

 

``` kotlin

class Customer public @Inject constructor(name: String) { ... }

```

 

더 자세한 사항을 알고싶으시다면 문서를 확인하세요. [Visibility Modifiers](visibility-modifiers.html#constructors).

 

 

#### Secondary Constructors

 

클래스에는 constructor*{: .keyword } 를 앞에 붙인 **secondary constructors** ( 보조 생성자 ) 를 선언할 수도 있습니다.

 

``` kotlin

class Person {

    constructor(parent: Person) {

        parent.children.add(this)

   }

}
```

 


If the class has a primary constructor, each secondary constructor needs to delegate to the primary constructor, either
directly or indirectly through another secondary constructor(s). Delegation to another constructor of the same class
is done using the *this*{: .keyword } keyword:

``` kotlin
class Person(val name: String) {
    constructor(name: String, parent: Person) : this(name) {
        parent.children.add(this)
    }
}
```

If a non-abstract class does not declare any constructors (primary or secondary), it will have a generated primary
constructor with no arguments. The visibility of the constructor will be public. If you do not want your class
to have a public constructor, you need to declare an empty primary constructor with non-default visibility:

``` kotlin
class DontCreateMe private constructor () {
}
```

> **NOTE**: On the JVM, if all of the parameters of the primary constructor have default values, the compiler will
> generate an additional parameterless constructor which will use the default values. This makes it easier to use
> Kotlin with libraries such as Jackson or JPA that create class instances through parameterless constructors.
>
> ``` kotlin
> class Customer(val customerName: String = "")
> ```
> {:.info}

### Creating instances of classes

To create an instance of a class, we call the constructor as if it were a regular function:

``` kotlin
val invoice = Invoice()

val customer = Customer("Joe Smith")
```

Note that Kotlin does not have a *new*{: .keyword } keyword.

Creating instances of nested, inner and anonymous inner classes is described in [Nested classes](nested-classes.html).

### Class Members

Classes can contain

* Constructors and initializer blocks
* [Functions](functions.html)
* [Properties](properties.html)
* [Nested and Inner Classes](nested-classes.html)
* [Object Declarations](object-declarations.html)


## Inheritance

All classes in Kotlin have a common superclass `Any`, that is a default super for a class with no supertypes declared:

``` kotlin
class Example // Implicitly inherits from Any
```

`Any` is not `java.lang.Object`; in particular, it does not have any members other than `equals()`, `hashCode()` and `toString()`.
Please consult the [Java interoperability](java-interop.html#object-methods) section for more details.

To declare an explicit supertype, we place the type after a colon in the class header:

``` kotlin
open class Base(p: Int)

class Derived(p: Int) : Base(p)
```

If the class has a primary constructor, the base type can (and must) be initialized right there,
using the parameters of the primary constructor.

If the class has no primary constructor, then each secondary constructor has to initialize the base type
using the *super*{: .keyword } keyword, or to delegate to another constructor which does that.
Note that in this case different secondary constructors can call different constructors of the base type:

``` kotlin
class MyView : View {
    constructor(ctx: Context) : super(ctx)

    constructor(ctx: Context, attrs: AttributeSet) : super(ctx, attrs)
}
```

The *open*{: .keyword } annotation on a class is the opposite of Java's *final*{: .keyword }: it allows others
to inherit from this class. By default, all classes in Kotlin are final, which
corresponds to [Effective Java](http://www.oracle.com/technetwork/java/effectivejava-136174.html),
Item 17: *Design and document for inheritance or else prohibit it*.

### Overriding Methods

As we mentioned before, we stick to making things explicit in Kotlin. And unlike Java, Kotlin requires explicit
annotations for overridable members (we call them *open*) and for overrides:

``` kotlin
open class Base {
    open fun v() {}
    fun nv() {}
}
class Derived() : Base() {
    override fun v() {}
}
```

The *override*{: .keyword } annotation is required for `Derived.v()`. If it were missing, the compiler would complain.
If there is no *open*{: .keyword } annotation on a function, like `Base.nv()`, declaring a method with the same signature in a subclass is illegal,
either with *override*{: .keyword } or without it. In a final class (e.g. a class with no *open*{: .keyword } annotation), open members are prohibited.

A member marked *override*{: .keyword } is itself open, i.e. it may be overridden in subclasses. If you want to prohibit re-overriding, use *final*{: .keyword }:

``` kotlin
open class AnotherDerived() : Base() {
    final override fun v() {}
}
```

### Overriding Properties 

Overriding properties works in a similar way to overriding methods; properties declared on a superclass that are then redeclared on a derived class must be prefaced with *override*{: .keyword }, and they must have a compatible type. Each declared property can be overridden by a property with an initializer or by a property with a getter method.

``` kotlin
open class Foo {
    open val x: Int get { ... }
}

class Bar1 : Foo() {
    override val x: Int = ...
}
```

You can also override a `val` property with a `var` property, but not vice versa. This is allowed because a `val` property essentially declares a getter method, and overriding it as a `var` additionally declares a setter method in the derived class.

Note that you can use the *override*{: .keyword } keyword as part of the property declaration in a primary constructor.

``` kotlin 
interface Foo {
    val count: Int
}

class Bar1(override val count: Int) : Foo

class Bar2 : Foo {
    override var count: Int = 0
}
```

### Overriding Rules

In Kotlin, implementation inheritance is regulated by the following rule: if a class inherits many implementations of the same member from its immediate superclasses,
it must override this member and provide its own implementation (perhaps, using one of the inherited ones).
To denote the supertype from which the inherited implementation is taken, we use *super*{: .keyword } qualified by the supertype name in angle brackets, e.g. `super<Base>`:

``` kotlin
open class A {
    open fun f() { print("A") }
    fun a() { print("a") }
}

interface B {
    fun f() { print("B") } // interface members are 'open' by default
    fun b() { print("b") }
}

class C() : A(), B {
    // The compiler requires f() to be overridden:
    override fun f() {
        super<A>.f() // call to A.f()
        super<B>.f() // call to B.f()
    }
}
```

It's fine to inherit from both `A` and `B`, and we have no problems with `a()` and `b()` since `C` inherits only one implementation of each of these functions.
But for `f()` we have two implementations inherited by `C`, and thus we have to override `f()` in `C`
and provide our own implementation that eliminates the ambiguity.

## Abstract Classes

A class and some of its members may be declared *abstract*{: .keyword }.
An abstract member does not have an implementation in its class.
Note that we do not need to annotate an abstract class or function with open – it goes without saying.

We can override a non-abstract open member with an abstract one

``` kotlin
open class Base {
    open fun f() {}
}

abstract class Derived : Base() {
    override abstract fun f()
}
```

## Companion Objects

In Kotlin, unlike Java or C#, classes do not have static methods. In most cases, it's recommended to simply use
package-level functions instead.

If you need to write a function that can be called without having a class instance but needs access to the internals
of a class (for example, a factory method), you can write it as a member of an [object declaration](object-declarations.html)
inside that class.

Even more specifically, if you declare a [companion object](object-declarations.html#companion-objects) inside your class,
you'll be able to call its members with the same syntax as calling static methods in Java/C#, using only the class name
as a qualifier.


## Sealed Classes

Sealed classes are used for representing restricted class hierarchies, when a value can have one of the types from a
limited set, but cannot have any other type. They are, in a sense, an extension of enum classes: the set of values
for an enum type is also restricted, but each enum constant exists only as a single instance, whereas a subclass
of a sealed class can have multiple instances which can contain state.

To declare a sealed class, you put the `sealed` modifier before the name of the class. A sealed class can have
subclasses, but all of them must be nested inside the declaration of the sealed class itself.

``` kotlin
sealed class Expr {
    class Const(val number: Double) : Expr()
    class Sum(val e1: Expr, val e2: Expr) : Expr()
    object NotANumber : Expr()
}
```

Note that classes which extend subclasses of a sealed class (indirect inheritors) can be placed anywhere, not necessarily inside
the declaration of the sealed class.

The key benefit of using sealed classes comes into play when you use them in a [`when` expression](control-flow.html#when-expression). If it's possible
to verify that the statement covers all cases, you don't need to add an `else` clause to the statement.

``` kotlin
fun eval(expr: Expr): Double = when(expr) {
    is Expr.Const -> expr.number
    is Expr.Sum -> eval(expr.e1) + eval(expr.e2)
    Expr.NotANumber -> Double.NaN
    // the `else` clause is not required because we've covered all the cases
}
```
