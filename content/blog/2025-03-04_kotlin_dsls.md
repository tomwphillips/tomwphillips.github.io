+++
title = 'Understanding Kotlin DSLs'
date = 2025-03-04T10:02:35Z
description = "A step-by-step explanation of how type-safe builders, or Kotlin DSLs, work."
[params]
	licence = "CC-BY-4.0"
+++

I'm learning Kotlin for a new job and I was confused by [type-safe builders](https://kotlinlang.org/docs/type-safe-builders.html). They're used to create domain specific languages (DSLs) in Kotlin. Two common use cases are [configuring routes in Ktor server](https://ktor.io/docs/server-routing.html) or [building HTML](https://github.com/Kotlin/kotlinx.html). I think they're confusing because they combine a bunch of Kotlin concepts. Here's how I broke it down to understand it.

Let's start with a data class to describe a pet. Note the member properties are mutable and have default values. I'll come back to why at the end.

```
data class Pet(var name: String? = null, var description: String? = null)
```

Next we need a *builder function*. It takes one argument, `init`, which is a [function type with receiver](https://kotlinlang.org/docs/lambdas.html#function-types). In other words, `init` takes no arguments, and operates in the context of a `Pet` instance, and returns `Unit`. The builder function returns an instance of `Pet`.

```
fun pet(init: Pet.() -> Unit): Pet {  
    val pet = Pet()  
    pet.init()  
    return pet  
}
```

You use it to create an instance of `Pet` like this:

```
var jessica = pet {  
    name = "Jessica"  
    description = "Black and white cat"  
}
```

There are two confusing things going on here.

First, there's no `()` calling the pet function. Instead, there's a *[trailing lambda](https://kotlinlang.org/docs/lambdas.html#passing-trailing-lambdas)*. In Kotlin, when the last argument to a function is a function, then you can omit the parentheses and pass the function as a lambda expression in curly brackets.

Second, how do the assignments to `name` and `description` end up as member properties of `jessica`? Previously I said `init` is a function type with receiver, and inside these types of function, the [receiving object can be accessed via `this`](https://kotlinlang.org/docs/lambdas.html#function-literals-with-receiver). But where's the `this`? You don't need it because Kotlin infers that `name` is actually `this.name`.

We can then nest another class inside `Pet`. Let's add information about the `Owner` of the pet.

```
data class Owner(var name: String? = null)  
  
data class Pet(var name: String? = null, var description: String? = null, val owners: MutableList<Owner> = mutableListOf<Owner>()) {  
    fun owner(init: Owner.() -> Unit): Owner {  
        val owner = Owner()  
        owner.init()  
        owners.add(owner)  
        return owner  
    }  
}
```

We've added a member property called `owners` and a member function called `owner`. This is another builder, like `pet`, that creates an instance of `Owner`, calls `init`, then adds the owner to the pet's list of owners, and returns the `owner`.

Put this altogether to get our DSL:

```
var jessica = pet {  
    name = "Jessica"  
    description = "Black and white moggy"  
    owner {  
        name = "Tom"  
    }  
    owner {  
        name = "Kelly"  
    }  
}

// println(jessica) returns:
// Pet(name=Jessica, description=Black and white moggy, owners=[Owner(name=Tom), Owner(name=Kelly)])
```


When I defined `Pet` I noted that the member properties must be mutable and have default values. You need defaults because the builder functions create an instance without passing any arguments to the constructor. Then because `init` sets the member properties, they must be mutable too. The exception is `Pet.owners`, because we don't want to reassign the (mutable) list reference, just modify the contents of it.

[Here's the code in Kotlin Playground](https://pl.kotl.in/y_4JeGf44).

