# Java Annotations: A Comprehensive Guide
Discover the power of Java Annotations! This repository explores how Annotations can add metadata to your code, improve documentation, and automate tedious tasks. Perfect for Java novices and pros alike, take your coding skills to the next level with Annotations










## Table of contents

- [Annotations: The Basic Concept](#annotations:-The-Basic-Concept)
  - [The challenge](#the-challenge)
  - [Screenshot](#screenshot)
  - [Links](#links)
- [My process](#my-process)
  - [Built with](#built-with)
  - [What I learned](#what-i-learned)
  - [Continued development](#continued-development)
  - [Useful resources](#useful-resources)
- [Author](#author)
- [Acknowledgments](#acknowledgments)










# Annotations: The Basic Concept
## The Idea
JAVA provides a feature that enables you<br />
to embed supplemental information into a source file.<br />
This information is called `annotation`, another name `metadata`.<br />

this information can be used by various tools<br />
during development and deployment. for example,<br />
annotations can be processed by a source code generator.<br />


## The Approach (declaring an annotation)
As you would expect,<br />
java implemets annotations from an object oriented perspective.<br />

<img src="./ReadMe-Images/annotation-simple-creation.png" style="width:600px; height:auto" alt="Showing a simple annotation Declared">

Notice the `@` that precedes the keyword `interface`<br />
telling the compiler that an annotation type is being declared.<br />

Notice the two members `str()` & `val()`.<br />
all annotations consist solely of method declaration,<br />
however, you don't provide bodies for this methods, JAVA implements them.<br />
To you, this methods acts much like fields as you would soon see.


## The Package
An annotation that you create, like the one above,<br />
cannot include an extends clause. However, <br />
all annotations types extend the `Annotation interface`.<br />

This interface is declared within the `java.lang.annotation` package <br />
it overrides `hashCode()`, `equals()`, `toString()` which are defined by Object. <br />
it also specifies `annotationType()`, which returns a class object<br />
that represents the invoking annotations<br />


## The Usage
Once you have declared an annotation,<br />
you can use it to annotate something. what things?

you can annotate declarations. what declarations?<br />
any type of declarations like `classes`, `methods`, <br />
`fields`, `parameters`, `enum constats`, `other annotations`

you can annotate `type use` with is a concept<br />
we would delve into later on.<br />

<img src="./ReadMe-Images/annotation-simple-usage.png" style="width:600px; height:auto" alt="Showing a simple annotation Used ">

```java
@MyAnno(str="Annotation Example", val=100)
public static void myMeth() { ... }
```

* Note that annotations precedes their declaration
* Notice that name of the annotation is preceeded by `@`<br />
  and is followed by a parenthesized list of member initialization.
* Notice that no parentheses follow `str` in this assignment.<br />
  this is how the annotation members are initialized.<br />
  In this context, <b><u>Annotations members look like fields</u></b>
* Note that you can place one or more annotation on an element.


## The Retention
There is what we call <b>annotation retention policy</b>
<b>A Retention Policy determines at what point an annotition is deicarded.</b>

JAVA defines three such policies by encapsulating them with the
`java.lang.annotation.RetentionPolicy` Enumerator. The constants are
`SOURCE`, `CLASS`, `RUNTIME`.

```java
@Retention( RetentionPolicy.RUNTIME )
@interface MyAnno {
  String str();
  int val();
}
```

With `SOURCE`,
annotations are retained only in the source file
and is dicarded during compilation.

With `CLASS`
annotations are stored in the `.class file` during compilation.
However, it is not available through the JVM during run time.

With `RUNTIME`
annotations are stored in the `.class file` during compilation.
and is available with JVM during runtime.
<b>RUNTIME offers the greatest annotation persistence</b>









# Annotations: Reflection and Annotation
## The Concept
If an annotation specify a rentention policy of RUNTIME,
then they can be queried at runtime by any JAVA program 
through the use of `reflection`.

Reflection is the feature that enables information about
a class to be obtained at runtime. the `reflection API` is
contained in the `java.lang.reflect` package

Obtaining annotations through Reflection Technique entails 3 steps.
You may want to get the annotation on a Class, or it members
e.g Methods, Constructors, Fields to do any of this,
you go through this three steps.
* Get the Class Object
* Get its Member Object
* Get its Annotation(s)


## Get the Class Object
The first step to using reflection is to obtain an object 
that represent the class whose annotations you want to obtain.
`Class` is one of JAVA built-in classes and is defined in `java.lang`.

There are various way to obtain a `Class` Object,
One way is throu the getClass() method defined by `Object`
with signature `final Class<?> getClass()`
this method returns the class object that represent the invoking object.

Another way is via a `class literal`. 
this is used whenever the class object of a known class is needed.

```java
class New { 
  // ... 
}

Class<?> c = New.class;
```

Notice the `<?>`. this is from JAVA Generics Concept.
However, an understanding of generics is not needed
to grasp the fundamental principle of reflection


## Get the Member Object
After you have obtained a `Class Object`, you can use it's methods 
to obtain information about the various items declared by the class,
it's fields, methods, constructors, annotations e.t.c

To obtain the annotations associated with a specific item
declared within a class, <b><u>you must first obtain the object
that represents that item.</u></b>

`Class` supplies (among others) the `getMethod()`,
`getField()`, `getConstructor()` methods. this methods returns object
of type `Method`, `Field`, `Constructor `

for example, to get a method member, 
it requires two arguments, the name of the method and
the class object representing the argument types of the method. 

```java
// getMethod declaration signature
Method getMethod(String methName, Class<?>...paramTypes)
```

```java
Method m = c.getMethod("myMeth", String.class, int.class);
```

if the method can't be found, NoSuchMethodException is thrown
do note, this is a checked exception.


## Get the Annotation(s)
From a Class, Method, Field, Constructor object,
you can obtain a specific annotation associated with that object
by calling the getAnnotation() on it.

```java
<A extends Annotation> getAnnotation(Class<A> annoType)
```

```java
Annotation[] get Annotations() 
```

`annoType` is a Class Object that represent the annotation
in which you are interested. the methods returns a
reference to the annotation which is an object of annoType. 
using this refernce, you can obtain the values 
associated with the annotation's members.


## Usage/Example
```java
import java.lang.annotation.*;
import java.lang.reflect.*;

@Retention(RetentionPolicy.RunTime)
@interface MyAnno{
    String str();
    int val();
}

class Meta {
    @myAnno(str = "Two Parameters", val=19)
    public static void myMeth(String str, int i){
        Meta ob = new Meta();
        try{
            Class<?> c = ob.getClass();
            Method m = c.getMethod("myMeth", String.class, int.class);
            MyAnno anno = m.getAnnotation(MyAnno.class);
            System.out.println(anno.str() + " " + anno.val());
        }
        catch(NoSuchMethodException exc){
            System.out.println("Method not found.");
        }
    }

    public static void main(String args[]){
        myMeth("test", 10);
    }
}
```


## End Note
For you to perform this reflection technique you need
to access the reflection Api contain in the java.lang.reflect package.

Actually, the methods `getAnnotation()` & `getAnnotations()` are defined
by the `AnnotatedElement interface` which is defined by `java.lang.reflect`.

This interface supports reflection for annotations
and is implrmented by the classes `Method`, `Field`,
`Constructor`, `Class`, `Package` among others.

in addition to those two, AnnotatedElement also defines several methods
```java
// returns all non-inherited annotations
Annotations[] getDeclaredAnnotations()
```
```java
// returns true it the annotation is present
default boolean isAnnotationPresent(Class<? extends Annotation> annoType)
```









# Annotations: Annotation Varieties
## Annotations with Default Values
```java
@Retention(RetentionPolicy.RUNTIME)
@interface MyAnno {
    String str() default "Testing";
    int val() default 9000;
}
```

You can give annotation member default values that will be used 
if no value is specified when the annotation is applied.

```java
// with no value
@MyAnno()

// with value
@MyAnno(val=20)
```


## Annotations as Markers
Marker annotation is a special kind tha contains no members.
Its sole purpose is to mark an item. Thus its presence
as an annotation is sufficient.

```java
@Retention(RetentionPolicy.RUNTIME)
@interface MyAnno{}
```

The Best way to determine if a marker annotation is present
is to use the method `isAnnotationPresent()` defined by
the `AnnotatedElement`.


## Annotations with single member
Single member annotations contains only one members.
It works like a normal annotation except that 
it allows a shorthand form of specifying the value of the member.

```java
// declaration
@Retention(RetentionPolicy.RUNTIME)
@interface MySingle {
    int value();
}

// usage
@MySingle(100)
```
you can use the single-value syntax when applying an annotation
that has other members, but those other members must all have default values.


## Built-In Annotations
there are many built-in annotations
most are specialized , but Nine are general purpose

Four are imported from `java.lang.annotations`
`@Retention`, `@Documented`, `@Target`, `@Inherited`

Five are included in `java.lang`
`@Override`, `@Deprecated`, `@FunctinalInterface`, 
`@SafeVarargs`, `@SuppressWarning`

Visit [Built-In-Annotations](./Extra-Notes/Built-In-Annotations.md) for more info on each of them.


## @Target Built-In Annotation
we would talk about the @Target annotation here,
because you will need it to understand the coming concept.

it is designed to be used only as an annotation to another annotation.
it specifies the `type` of the items 
to which an annotation will be applied.

for example it informs that the declaration an annotation 
must be applied to is a constructor or method e.t.c

```java
@Target(ElementType.Method)
@interface Recommended {}
```

@Target takes one argument, a constant or an array of constants.
this constants are of the `ElementType enumeration`
some example of this constants are `ANNOTATION_TYPE`,
C`ONSTRUCTOR`, `FIELD`, `METHOD`, `TYPE`, `TYPE_USE`, `TYPE_PARAMETER`









# Annotations: Type Annotation
## The Concept
Annotations were originally allowed only on declarations however, 
for modern versions of JAVA Annotatins can also be specified 
in most cases in which a type is used.

For Example, You can Annotate:
* the return type of a method
* the type of `this argument` of a method
* a cast
* array levels (2D, 3D)
* an inherited class
* a `throws` clause
* generic types (generic type parameter bonds & generic type argument)

Type annotations are important because they enable tools 
to perform additional checks on code to help prevent errors.
Understand that, as a general rule, 
javac will not perform these checks itself.


## A Quick Look
Type Annotation must include the `@Target` annotation,
specifying the constants `ElementType.TYPE_USE` as a target

```java

@Target(ElementType.TYPE_USE)
@interface TypeAnno { }

```
A Type Annotation applies to the type that the annotation precedes.
check out how we make @TypeAnno annotate the NullPointerException
in the throws clause.

```java

void myMeth() throws @TypeAnno NullPointerException { ... }

```

Check out how we make `@TypeAnno` annotate the `this argument` of a method.
`this` is an implicit argument to all instance methods
and it refers to the invoking object. by explicitly 
declaring it, you can now annotate its type.

```java

class SomeClass {
  int myMeth( @TypeAnno SomeClass this, int i){
    ...
  }
}

```

Note that if you do explictly declare `this`, it must be the first parameter.


## Usage/Example
```java
import java.lang.annotation.*;
import java.lang.reflect.*;

@Target(ElementType.TYPE_USE)
@interface TypeAnno {}

@Target(ElementType.TYPE_USE)
@interface NotZeroLen {}

@Target(ElementType.TYPE_USE)
@interface Unique {}

@Target(ElementType.TYPE_USE)
@interface EmptyOk {}

@Target(ElementType.TYPE_USE)
@interface Recommended {}

@Target(ElementType.TYPE_USE)
@interface MaxLen {
  int value();
}

@Target(ElementType.TYPE_PARAMETER)
@interface What {
  String description();
}
```

```java

// type annotate the class type parameter
class TypeAnnoDemo<@What(description="Generic data type") T>{

  // type annotate on the constructor
  public @Unique TypeAnnoDemo() {}

  // type annotate the datatype String, not the field
  @TypeAnno String str;

  // annotates the field "test"
  @EmptyOk String test;

  // type annotate the "this" argument
  public int f(@TypeAnno TypeAnnoDemo<T> this, int x) { return 10; }

  // type annotate the return type
  public @TypeAnno Integer f2(intj, intk) { return j+k; }

  // annotate the method declaration
  public @Recommended Integer f3(String str){ return str.length() / 2; }

  // type annotate the throws clause
  public void f4() throws @TypeAnno NullPointerException { // ... }

  // annotate array levels
  String @MaxLen(10) [] @NotZeroLen [] w;

  // type annotate the type of an array
  @TypeAnno Integer[] vec;

}
```

```java
public static void myMeth(int i){
  // type annotation on a type argument
  TypeAnnoDemo<@TypeAnno Integer> ob = new TypeAnnoDemo<@TypeAnno Integer>();

  // type annotation on object instantiation
  @Unique TypeAnnoDemo<Integer> ob2 = new @Unique TypeAnnoDemo<Integer>();

  // type annotation on a cast
  Object x = Integer.valueOf(10);
  Integer y = (@TypeAnno Integer) x;

}

public static void main(String[] args){ myMeth(10); }

// type annotation a superclass
class SomeClass extends @TypeAnno TypeAnnoDemo<Boolean> {}
```
## No Ambiguities
Pay attention to this two method declaration
```java
  // type annotate the return type
  public @TypeAnno Integer f2(intj, intk) { 
    return j+k; 
  }

  // annotate the method declaration
  public @Recommended Integer f3(String str){
    return str.length() / 2; 
  }
```
Notice that in both cases, 
an annotation precedes the method's return type (Integer)
however, the two annotations annotate two different things.

The differnce is in the specification of the `@Target` annotation.
`@TypeAnno` annotate f2's return type because
its targe specified `ElementType.TYPE_USE`.
`@Recommended` annotates the method declaration because
it has it's taget specified as `Element.METHOD`.

Therefore, the target specification


## End-Note
Therefore, the target specification is used to 
eliminate what, at first glance appears to be an ambiguity
between the annotation of a method declaration
and the annotation of a method type.









# Annotations: Repeating Annotation
## The Idea
the idea of Repeating Annotation is to have two or more objects
of an annotation class and attach them all to a declaration
```java
@MyAnno(str="first annotation", val=-1)
@MyAnno(str="Second annotation", val=100)
public static void myMeth(String str, int i) {
  //...
}
```


## The Setup
One way JAVA implements this is that you would create two different annotations
* Annotation A
* Annotation B

if you repeate Annotation A on a declaration, JAVA would store 
all objects of the annotation you added to the declaration in an array variable. 

You would separately create Annotation B which will only have one member,
which would be the the array variable. have a look
```java
@interface MyRepeatedAnnos{
  MyAnno[] value();
}
```

Lastly, we need to connect Annotation B  to Annotation A
stating that it will be a container to store all Annotation A
if it is repeated on a declaration.
```java
@Repeatable( MyRepeatedAnnos.class )
@interface MyAnno {
  String str() default "Testing";
  int val() default 9000;
}
```
This is where the Built-in Annotation `Repeatable` comes in.
it specifies a particular annotation (Annotation A) is repeatable,
and also specifies the container annotation (Annotation B).


## The Access
We access the repeated annotation using a method such as `getAnnotation()`
In this case, we use the container annotation (Annotation B),
not the repeatable annotation itself (Annotation A)

```java
  ...
  Annotation anno = m.getAnnotation(MyRepeatedAnnos.class);
  ...
```

Another way to obtain the repeated annotations 
is to use one of the methods in AnnotatedElement
that can operate directly on a repeated annotation

these are `getAnnotationByType()` and `getDeclaredAnnotationsByType()` with signature 
`default <T extends Annotation> T[] getAnnotationsByType(Class<T> annoType)`

```java
Annotation[] annos = m.getAnnotationsByType(myAnnos.class);
for (Annotation a:annos){
  System.out.println(a);
}
```

## Usage/Example

```java
@Retention(RetentionPolicy.RUNTIME)
@Repeatable(MyRepeatedAnnos.class)
@interface MyAnno {
  String str() default "Testing";
  int val() default 9000;
}

@Retention(RetentionPolicy.RUNTIME)
@interface MyRepeatedAnnos {
  MyAnno[] value();
}
```
```java
class RepeatAnno { 
  @MyAnno(str="first annotation", val=-1)
  @MyAnno(str="Second annotation", val=100)
  public static void myMeth(String str, int i) {
    RepeatAnno ob = new RepeatAnno();

    try{
      Method m = ob.getClass.getMethod("myMeth", String.class, int.class);
      Annotation anno = m.getAnnotation(MyRepeatedAnnos.class);

    } catch (NoSuchMethodException exc) {
      System.out.println("Method Not Found")
    }
  }

  public static void main(String[] args){
    myMeth("test", 10);
  }

}
```









# Annotations: Annotation Restrictions
* No annotation can inherit another
* All method declared by an annotation must be withous parameters
* All methods declard by an annotation must return one of the following
  * A primitive type
  * An object of type `String` or `Class`
  * An object of an `enum` type
  * An object of another annotation type
  * An Array of a legal type
* Annotations cannot be generic
* Annotation methods cannot specigy a `throws` clause


Part 1: Basic Annotation Concepts
Explanation of the concept of annotations
Syntax and structure of annotations
Types of annotations
Annotations vs. Javadoc comments
The Package Annotation
Explanation of package-level annotations
Examples of package-level annotations
Use cases for package-level annotations
The Retention Annotation
Explanation of retention policies in Java annotations
Examples of retention policies
Use cases for retention policies
Part 2: Reflection and Annotations
Explanation of the reflection API
Use cases for reflection with annotations
The Reflection Package
Overview of the reflection API package
Explanation of the key classes in the reflection package
Examples of using the reflection package with annotations
Reflection with Built-in Annotations
Explanation of how to use reflection with built-in annotations
Examples of using reflection with built-in annotations
Part 3: Advanced Annotation Concepts
Explanation of advanced annotation concepts in Java programming
Annotations with Default Values
Explanation of default values in annotations
Examples of using annotations with default values
Annotations as Markers
Explanation of using annotations as markers in Java programming
Examples of using annotations as markers
Annotations with Single Members
Explanation of using annotations with single members
Examples of using annotations with single members
Type Annotations
Explanation of type annotations in Java programming
Examples of using type annotations
Repeating Annotations
Explanation of repeating annotations in Java programming
Examples of using repeating annotations
Conclusion
Recap of key concepts and use cases for annotations in Java programming
Next steps for learning more about annotations in Java programming

