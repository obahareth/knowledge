# Syntax

### Entry Point

You should always have a `main` function as your entry point.

```go
package main

func main() {
}

```

### Type Declarations

Type declarations always go to the right of variable or function names, e.g.

```go
var i int = 1
```

_This isn't necessary in this case as it can be inferred from the right hand side_

#### Type Inference

Go has the `:=` operator which can infer the type and assign it to a variable at the same time. Using it we can write the same example as above and omit the `var` and `int`.

```go
i := 5
```

### Function Declarations

Function declarations are actually quite similar \(the return type is to the right of the function name\), here's a function that just returns a string when called.

```go
func returnHello() string {
	return "Hello"
}
```

Here's the same version but this time it takes in a name as well

```go
package main

import "fmt"

func main() {
	fmt.Println(returnHello("Omar"))
}

func returnHello(name string) string {
	return fmt.Sprintf("Hello %s", name)
}

```

### Conditionals

Conditionals are quire straightforward in Go, they're similar to most languages but without the parentheses

```go
package main

import "fmt"

func main() {
	name := "Omar"

	if name == "Omar" {
		fmt.Println("It's me")
	} else {
		fmt.Println("Hey there stranger!")
	}
}
```

### Loops

There's only one type of loop in Go, a `for` loop.

Here's a loop that prints "Hello" five times.

```go
package main

import "fmt"

func main() {
	for i := 0; i < 5; i++ {
		fmt.Println("Hello")
	}
}
```

Here's an infinite loop \(useful for servers, game engines, etc.\)

**Warning:** You have to kill the program to end this

```go
package main

import "fmt"

func main() {
	for {
		fmt.Println("Hello")
	}
}
```

This form can also be used to make a finite loop when combined with the `break` keyword, this will print 0 to 4.

```go
package main

import "fmt"

func main() {
	i := 0

	for {
		if i >= 5 {
			break
		}

		fmt.Println(i)
		i++
	}
}
```

There's also a form of the loop that you only need to provide with the condition, this will print 0 to 5. \(This is used to **stop it before the next run**\)

```go
package main

import "fmt"

func main() {
	i := 0
	isLessThanFive := true

	for isLessThanFive {
		if i >= 5 {
			isLessThanFive = false
		}

		fmt.Println(i)
		i++
	}
}
```

### Structs

Structs are a useful way to encapsulate and reuse objects, and they can even have methods. They're similar in some ways to classes in other programming languages.

Here's how to declare a struct \(can be done outside of `main`\)

```go
package main

import "fmt"

// Struct declaration
type Person struct {
	name string
	age  int
}

func main() {
    // Struct usage
	omar := Person{name: "Omar", age: 27}
	fmt.Println(omar)
}
```

You can add methods to structs by using [method receivers](https://spino.tech/blog/method-receiver-types-in-go/).

```go
package main

import "fmt"

type Person struct {
	name string
	age  int
}

func (p Person) description() string {
	return fmt.Sprintf("%s is a Person who is %d years old", p.name, p.age)
}

func main() {
	omar := Person{name: "Omar", age: 27}
	fmt.Println(omar.description())
}
```

This syntax was the most confusing to me whenever I saw Go code, the `(p Person)` part. This basically says that the type that can receive this method call is of type `Person` and that within that function we want to name that receiver `p`.

### Interfaces

Interfaces add a bit of genericness to Go, they're quite similar to other languages except that you don't need to specifically declare that your struct implements an interface, the compiler infers it if the signature of your methods match your interface method's signature.

```go
package main

import "fmt"

// Interface declaration
type Eater interface {
	eat() string
}

type Cat struct {
	name string
}

func (c Cat) eat() string {
	return fmt.Sprintf("%s likes to eat cat food.", c.name)
}

type Dog struct {
	name string
}

func (d Dog) eat() string {
	return fmt.Sprintf("%s likes to eat dog food.", d.name)
}

func printEatResult(e Eater) {
	fmt.Println(e.eat())
}

func main() {
	cat := Cat{name: "Felix"}
	dog := Dog{name: "Spark"}

	printEatResult(cat)
	printEatResult(dog)
}
```

This slightly lengthy example allowed us treat both `Cat` and `Dog` as `Eater`s in the `printEatResult` function. Notice how we didn't have to do anything to explicitly say that Cats and Dogs implement the Eater interface, the compiler just figured it out because the signature of the `eat` function on each of those structs matched the `Eater` 's version of it.

_It's a convention to name interfaces that have only one method \(e.g. `eat`\) to end with the name of that method plus `er`, so `eater`._

### Arrays and Slices

#### Arrays

It's quite simple to declare and use an array \(_the size of the array goes to the left of the type_\).

```go
package main

func main() {
	var names [2]string
	names[0] = "Omar"
	names[1] = "John"
	fmt.Println(names)
}
```

However with arrays you must always declare the size, if you want to use something with flexible size, that's what slices are for.

#### Slices

Slices are a type built on top of arrays to provide more convenience and flexibility, most array programming in Go is done using slices.

```go
package main

func main() {
	var names []string
	names = append(names, "Omar")
	names = append(names, "John")
}
```

_In the example above, the size is omitted \(you don't need to know the size of your data when declaring a slice\)_

