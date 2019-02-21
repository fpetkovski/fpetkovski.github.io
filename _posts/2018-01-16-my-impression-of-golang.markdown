---
layout: post
title: My impression of Go
tags: [go]
image: '/images/posts/go-lang-game.png'
---

#### Introduction

The Go language was developed by Google to solve specific problems in the domain of infrastructure software development.
As a result of being designed with very specific objectives in mind, it ended up being an industrial-grade language with peculiar combination of features, instead of an academic language testing and probing the boundaries of programming paradigms.

Contrary to many people's expectations, Go did not make a dramatic entrance to the scene, and did not sieze the market overnight. Since its official release in 2012, it has been gaining popularity relatively slowly, and is nowadays most notably represented by projects like Docker, Kubernetes, the HashiCorp suite, and pretty much any other infrastructure management software solution out there. 

What I did notice lately though, is that there there has been a slow adoption of the language for solving problems
in domains other than infrastructure automation, with companies starting to use it for automatic business domain logic and business processes as well. 
This adoption is probably further amplified by the trend of moving to microservices, where Go is a good fit due to its small footprint as well as the statically linked, single binary compilation target.

As a result of the mixed feelings people had for the language, I decided to give Go a go, and see for myself what the fuss is about. 

#### A refreshing view on object orientation

What I immediatelly loved about the language is its strong focus on fundamental object orientation, similar to what was initially envisioned by Alan Kay. While this outcome might not have been the end goal of the authors of the language, Go ended up with a design which closely resembles what object orientation is supposed to be about: **objects** passing **messages** to other **objects**. When classes and type inheritance are left completely out of the equation, thinking in terms of composition and delegation becomes very natural. 

One detail which I liked in particular was the fact that the compiler assists you with implementing delegation by generating boilerplate code for you. To illustrate this idea, take a look at the code snipped bellow as an example.

```
type Point struct {
	X int
	Y int
}

func (point *Point) Translate(dx int, dy int) {
	point.X += dx
	point.Y += dy
}

type Circle struct {
	Point
	Radius int
}
```

While it might be obvious even to programmers who are not familiar with Go that the `Translate` method can be applied to a `Point` type, what made my day was the fact that the same method can be applied to a `Circle` type as well, and it will be delegated to the embedded `Point` instance automatically. In other words, the compiler will generate the following code snipped without any additional instructions:

```
func (circle *Circle) Translate(dx int, dy int) {
	circle.Point.Translate(dx, dy)
}
```

For me personally, this this combination of features is a good replacement for classes and hierarchies.

#### Implicitly confusing interfaces

In most traditional OO languages, a class must, in one form or another, declare all of the interfaces that it implements.
With Go on the other hand, a type can implement an interface implicitly, without declaring the interface in its signature. 
For example, given the interface:
```
type Runnable interface {
	Run()
}
```
all a type `Foo` has to do to implement the interface is to implement the methods themselves, like so:
```
func (foo Foo) Run() {}
```

I must admit that the decision to go for such a design struck me quite odd, and I am still conflicted on the entire idea. 
Just because a type exposes methods which match the signature of a particular interface, does not mean that the type fulfills the semantics of the interface. As a result, a type might accidentally fulfil a particular interface, even if that was not the intention. 

What implicit interfaces allow you to do on the other hand, is to make types from external modules satisfy an interface, withough changing the code for those modules.

Whatever the reasons for deciding to go this route, I am not convinced that the benefits of implicit interfaces outweigh their drawbacks, and I would have prefered to have explicit interfaces instead.

#### Exception management

This could be one of the most controversial features of the language, and it has been debated over and over again.
Go has decided to opt out of exceptions as we know them in traditional languages (think Java) and rewind back to the roots.
Instead of throwing exceptions, Go functions return errors. The reasoning behind the decision is best explained by the following paragraph, written by Andrew Gerrand, one of the makers of the language [[1]](https://news.ycombinator.com/item?id=4159672):

```
The reason we didn't include exceptions in Go is not because of expense. It's because exceptions thread an invisible second control flow through your programs making them less readable and harder to reason about.
In Go the code does what it says. The error is handled or it is not. You may find Go's error handling verbose, but a lot of programmers find this a great relief.

In short, we didn't include exceptions because we don't need them. Why add all that complexity for such contentious gains?
```

Reading through the explanation, I could see why one would find exceptions to be intrusive and why they would clutter the happy path, but I don't think returning error codes and handling them explicitly after every line makes things better. 

On the upside, Go 2 will have a slightly upgraded mechanism of handling errors, with native `check` statements, which will probably make the developer experience a bit better. In addition, since the error value usually comes as an additional return parameter from a function, it is far less likely for you as a developer to forget to handle it.

#### Concurrency

Go exposes two interfaces for managing concurrency, the more traditional one - communcation using shared memory, and CSP (communicating sequential processes), a model in which values are passed between concurrent activities. The former is widely adopted and used throughout different languages, but the latter, exposed through goroutines and channels, is not something you encounter very often. While Haskell has a similar concept with sparks, and Erlang and the OTP platform support the CPS model on a much stricter level, there are additional constraints which those languages impose on the developer so that the model can actually work. Go takes a less safe approach and allows you to shoot yourself in the foot (by passing pointers through channels, therefore sharing memory), but that is still better than not giving you the opportunity to use the model if you know what you are doing. 

There are countless articles I ran into which are bashing goroutines and channels[[2]](https://medium.com/@sargun/go-concurrency-considered-harmful-26499a422830)[[3]](https://www.jtolio.com/2016/03/go-channels-are-bad-and-you-should-feel-bad), but I cannot agree with the sentiment they convey. The CSP implementation is merely a tool
you can use to solve a problem for which it ends up being a good fit. No one is forcing you to use it, and if you feel there is a better tool for your problem, you should go ahead and use that tool instead. And yes, there might be better implementations in languages like Erlang and Haskell, but try hiring for people who can actually be productive writing Haskell on remotely the same scale as they could be by writing Go. 

Goroutines, by being such a central feature of Go, make a strong impact on the entire ecosystem by democratising CSP. I belive that with time, we will start to see implementations
of the model in more traditional languages, which would be an amazing outcome in and of itself.

#### Conclusion

Overall, I like the language so far and I appreciate the simplicity it tries to maitain. In addition to less verbose exception handling, Go 2 also promises generics, which will further increase the type safety of the language. The authors have decided to allow to communicity to drive the further development of Go, and am looking forward to see how the language will evolve in the future.

#### References
* [1]<https://news.ycombinator.com/item?id=4159672>
* [2]<https://medium.com/@sargun/go-concurrency-considered-harmful-26499a422830>
* [3]<https://www.jtolio.com/2016/03/go-channels-are-bad-and-you-should-feel-bad>