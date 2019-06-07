# Program Structure
## Names
* Variable names starts with a letter or underscore. It can contain any number 
of letter, number, underscore. GoLang is case sensitive.
* Keywords like **break**, **if**, **case** are not usable as names.
* Constants(like **true**, **false**); types(like **float32**, **int8**); 
functions(like **make**, **cap**) are not reserved words. They can be redeclared 
even.
* If a name is declared in a function, it is *local* to that function.
* If a name is declared outside a function, it is *local* to the files that 
belong to the same package.
* If a name starts with uppercase letter, it is *exported*, meaning it can be 
accessable from the outside of the package. Package themselves are lowercase.
* The larger the scope of a name, the longer its name should be.
* Go programmers use camel case. Acronyms are all in same caps like **htmlReader**,
**ASCIIConverter** 

## Declarations
A declaration names a program entity, specify some or all of its properties. Go 
programs are stored in files with extension **.go**. Each go file starts with a
package declaration that specifies which package current file belongs to. The
package declaration is followed by list of import declarations used in file.
Then comes the package level declarations of types, variables, constants and 
functions in any order.

## Variables
A *var* declaration creates a variable of particular type, attaches a name to
it and sets its initial value. It is in the general form of;
`var name type = expression`

If the type is omitted, it can be inferred from expression. If the *= expression*
is omitted, the variable will be initialized to the zero value for that type.
There is no variable in Go that is uninitialized at any given time, preventing
many potential failures. Below usage is also permitted;
`var i, j, k int             // All has int type`
`var b, f, s = true, 2.3, 45 // bool, float64, int`
`var f, err = os.Open(name)  //os.Open returns a file and an error`

*Short* variable declarations can be used to declare and initialize a local 
variable. It has the form of `name := expression` and the type is inferred
by the expression.
```
t := 0
freq := rand.Float64() * 3.0
anim := gif.GIF(LoopCount: nFrames)
```

Short var declarations are popular. Long var declaration is reserved for local 
variables that explicit type definition other than initializer expression
(like forcing a var to be float32 with float64 literal) or when the value
will be assigned later.
```
i := 100
i, j := 0, 1
f, err := os.Open(name)
```

Remember that **=** is assignment, **:=** is declaration. Also, short declaration
does not mean all variables at the left hand side will be declared. If one of
variables the left side is declared before, short var declaration becomes 
assignment for that variable. But in grouped short var declarations, at least
one of the variables should be declared fresh.

### Pointers
A pointer value is the address of a variable. Not every value has an address but
all variables has one. Like **C**, an address of a variable is `&varName`. The 
value of the variable that is pointed by pointer is `*pntName`.

The zero value for a pointer is **nil**. Pointers can be compared, they are equal
if both of them points to the same variable(not the value).

You can safely return an address of a local variable from a function in Go. With
each call, there will be different local variable's address will be returned.

### The new Function
The expression `new(T)` creates a variable of type T, initializing to the zero
value and returning a pointer to that anonymous variable. It is no different 
than declaring a named variable and using its address.

### Lifetime of Variables
* Package level variables have lifetime of whole program execution.
* Local variables have dynamic lifetime. It might end with the enclosing 
function ending or may continue if they are reachable outside of the function.
For example, you can return a pointer to a local variable and it lives as long
as that pointer lives.
* A loop variable may also outlive the loop body or even the function body.
* Although garbage collector eases development, you still have to be efficient
with the memory. Do not let short life variables to become long life ones by
keeping a reference to them unnecessarily.

## Assignments
The value held by a variable is updated by an assignment statement, which in
its simplest form has a var iable on the left of the = sign and an expression
on the right.

Tuple assignments are used to update multiple variables at once. It is safe,
all right hand expressions are evaluated before updating left hand vars.
```
x, y = y, x
a[i], a[j] = a[j], a[i]
f, err = os.Open("foo.txt") // function call returns two values
```

Do not overuse tuple, sometimes separating is more understandable. If a function
returns multiple results, you should use tuple assignment like above. If you do
not care about any of the values, give them blank identifiers;
`_, err = io.Copy(dst, src) // discard byte count`

An assignment is legal if type of both left hand side and right hand side 
operand is the same.

## Type Declarations
The type of a variable or expression defines the characteristics of the values it
may take on such as their size, how they are represented internally, the intrinsic
operations that can be performed on them and the methods associated with them.

A type declaration defines a new name type from an underlying type. But even
though underlying types are the same, declared types will be treated different.
They are not comparable to each other, can not be applied with same arithmetic
operations. It prevents errors coming from implicit conversions. Check the 
example below;
```
package tempconv

import "fmt"

type Celsius float64
type Fahrenheit float64

const (
    AbsoluteZeroC Celsius = -273.15
    FreezingC Celsius = 0
    BoilingC Celsius = 100
)
func CToF(c Celsius) Fahrenheit { return Fahrenheit(c*9/5 + 32) }
func FToC(f Fahrenheit) Celsius { return Celsius((f - 32) * 5 / 9) }
```

**Celsius** and **Fahrenheit** are different types now. To convert them to each
other, you need to use CToF or FToC. If you want to convert regular float64
values to these types, you have to use **Celsius(T)** and **Fahrenheit(T)**
converters. If a value is assignable to type T, conversion is redundant. For
example, you dont have to convert 100.0 to Celsius type before assigning.

To compare or combine different types, you have to convert one of them to
other with converters.

## Packages and Files
Packages in Go serve the same purpose of encapsulation, modularity, separate
compilation and reuse as in other language library and module structures.
The source code for a package resides in one or many **.go** files. Package
structures generally resembles their directory structure.

Packages serve as separate namespaces. You need to add package specifier if
you want to specify a certain name in that namespace like `image.Decode` or
`utf16.Decode`.