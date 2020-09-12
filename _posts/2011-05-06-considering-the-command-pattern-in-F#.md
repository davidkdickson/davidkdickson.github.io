---
layout: post
title: Considering the command pattern in F#
---

The [command pattern](http://en.wikipedia.org/wiki/Command_pattern) describes a way to represent actions in an application. It is used to encapsulate actions so that they can be invoked at some later point. We often see collections of commands that specify steps of a process or operations that the user can choose from. Below is an object oriented way of implementing this pattern.

If we consider this pattern from a functional perspective, command objects are being used as functions. Indeed they are equivalent to functions. Thus it is arguable that the command pattern is more readily adopted in object oriented programming when there is a lack of support for [higher order functions.](http://en.wikipedia.org/wiki/Higher-order_function)

## Object Oriented Example

Let’s look at a simple example. Say we are tasked to develop the software to control a robot by remote control. Following shows how we could implement this using the object oriented command pattern.

{% highlight fsharp %}
type Robot(position, rotate) =
    let mutable (x,y) = position
    let mutable rotation = rotate
    member this.Move(distance) =
      x <- x + (distance * sin (System.Math.PI/180.0 * rotation))
      y <- y + (distance * cos (System.Math.PI/180.0 * rotation))
    member this.Rotate(angle) =
    	let newRotation =
    		let nr = rotation + angle
    		match nr with
    		| n when n < 360.0 -> nr
    		| _ -> nr - 360.0
    	rotation <- newRotation

type ICommand =
    abstract Execute : unit -> unit

type Move(robot:Robot, distance) =
    interface ICommand with
    	member this.Execute() = robot.Move(distance)

type Rotate(robot:Robot, rotation) =
    interface ICommand with
    	member this.Execute() = robot.Rotate(rotation)

type RemoteControl(cmds: ICommand list) =
    let commands = cmds
    member this.RunCommands() = commands |> List.iter(fun c -> c.Execute())
{% endhighlight %}

## Functional Solution

Below shows how we could implement the robot example drawing on functional constructs:

{% highlight fsharp %}
type Robot = {Position : float * float; Rotation : float}

let move distance robot =
    let x2 = distance * sin (System.Math.PI/180.0 * robot.Rotation)
    let y2 = distance * cos (System.Math.PI/180.0 * robot.Rotation)
    {robot with Position = (robot.Position |> fst) + x2, (robot.Position |> snd) + y2 }

let rotate angle robot =
    let newRotation =
      let nr = robot.Rotation + angle
    	match nr with
    	| n when n < 360.0 -> nr
    	| _ -> nr - 360.0
    {robot with Rotation = newRotation}

let remoteControl commands robot =
    commands |> List.fold(fun w c -> c w) robot
{% endhighlight %}

You will notice that in the functional solution there is no command interface defined. The reason for this is because F# has support for higher order functions. An object oriented way to understand a function is to think of it is an interface with a single method. Thus in a functional language like F# the single method command interface becomes unnecessary.

Another difference is that in the provided functional solution the data (i.e. location of the robot) is independent of the functions that transform the data (move and rotate). The reason this is adopted is because one of the principles of functional programming is [referential transparency.](http://en.wikipedia.org/wiki/Referential_transparency_(computer_science)) That is, for a given set of inputs at any point in time, a referentially transparent expression will return the same result. The benefit is that referentially transparent expressions are deterministic by definition. In supporting this principle the functional solution above makes variables immutable, and rather than modifying arguments, the processing functions return new variables.

## Conclusion

The command pattern is used to represent some behaviour which you know needs done. In a functional language the need for this object oriented pattern is diminished because these behaviours can be encapsulated and passed around as functions. Design patterns are an interesting topic as they provide solutions to commonly occurring problems in software design. However, I really enjoy it when a language or framework can appropriately abstract me away from boilerplate code. In fact I think Paul Graham sums it up best:

<cite>"When I see patterns in my programs, I consider it a sign of trouble. The shape of a program should reflect only the problem it needs to solve. Any other regularity in the code is a sign, to me at least, that I'm using abstractions that aren't powerful enough - often that I'm generating by hand the expansions of some macro that I need to write."</cite>
