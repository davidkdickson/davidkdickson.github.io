---
layout: post
title: The composite pattern in F#
---

Triggered by an interesting [stack overflow](http://stackoverflow.com/questions/327955/does-functional-programming-replace-gof-design-patterns) question on whether functional languages replace object oriented design patterns I decided to take a look at how you would implement various object oriented design patterns in a functional style. In this post I explore the [composite pattern.](http://en.wikipedia.org/wiki/Composite_pattern)

## Object oriented example

Say we are designing a system for a fast food company where individual menu items have a price, and combinations of menu items can be ordered as “combo meals”. In this scenario we could apply the object oriented composite pattern as follows:

{% highlight FSharp %}
type MenuItem() =
    abstract GetPrice : unit -> double

type Burger(p) =
    inherit MenuItem()
    let price = p
    override this.GetPrice() = p

type Side(p) =
    inherit MenuItem()
    let price = p
    override this.GetPrice() = p

type Drink(p) =
    inherit MenuItem()
    let price = p
    override this.GetPrice() = p

type Combo(items:List<MenuItem>) =
    inherit MenuItem()
    let menuItems = items
    override this.GetPrice() =
      menuItems |> List.fold (fun acc i -> i.GetPrice() + acc) 0.0
{% endhighlight %}

## Functional version

In F# one of the basic functional types is the [discriminated union.](http://msdn.microsoft.com/en-us/library/dd233226.aspx) Discriminated unions provide support for values that can be one of a number of named cases, possibly each with different values and types. In addition discriminated unions can be recursive. As such we can implement the composite pattern in a single discriminated union.

{% highlight fsharp %}
 type MenuItem =
    | Burger of double
    | Side of double
    | Drink of double
    | Combo of List<MenuItem>
{% endhighlight %}

One obvious difference between the functional and object oriented versions is the terseness that comes with using a discriminated union. Another is that the discriminated union publicly exposes the fact that a component is created by composition. However, this isn’t necessarily a bad design decision as it aligns with the functional emphasis on adding new functionality to existing types. This means that the GetPrice member does not need to be defined as part of the type. Instead it can be implemented independently of the type as a processing function.

{% highlight fsharp %}
let getPrice item =
    let rec calculatePrice item =
        match item with
        | Burger(price) | Drink(price) | Side(price) -> price
        | Combo(items) ->
            items |> List.fold (fun acc i -> acc + (calculatePrice i)) 0.0
{% endhighlight %}
