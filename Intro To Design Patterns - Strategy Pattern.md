# The simUDuck Application

The simUDuck App is a simple app that can show you ducks that can do 2 things: quack & swim. The current setup is the following

![alt text](<sources/Intro To Design Patterns - Strategy Pattern/Screenshot 2026-02-19 at 2.01.40 PM.png>)

It's a relatively simple setup. We have an abstract super class that defines the two things the ducks can do along with an extra method that defines how to visualize the duck.

# A New Feature

A new requirement has come by: we need to show the ducks flying. A naive solution here would be to add an abstract method in the `Duck` superclass. Now, this will work. The issue comes when you have heaps and heaps of other kinds of duck. Particularly, what if the ducks cannot fly? An example (a silly one) is a rubber duckie. It cannot `quack()` nor `fly()` but it can technically `swim()`. You'll find yourself doing a lot of work overriding the functions and keep on checking them for every new ducks created. Furthermore, it'd be way nicer if those methods are not even provided in the first place.

Something else you can try is to implement an interface. Something along the lines of `Flyable` and `Quackable`. The idea being, those ducks that can fly will implement `Flyable`, and similarly with the other attributes of interest and have those methods only for the ducks that can indeed, for example, fly. The issue with this is duplicated code. You'll have to reimplement the same piece of codes in ALL of the duck subclasses; we've made it too separate. Furthermore, imagine if a bunch of class follows similar methods and you need to make a small change on it. You'll have to track them all down.

# An OOP Solution

> **Design Principle**: Identify the constants in your application and separate them from the varying

This for the most part is just a good practice in writing code. For our case, the varying parts would be the methods `quack()` and `fly()`. We'll have to pull them out and encapsulate them.

But how? One way is to just rip them out and turn them into classes. An example would be `Squeaking` and `FlyFast`. We can further create two more interfaces to organize the behaviours together (and so that we can utilise polymorphism later): `FlyBehaviour`, `QuackBehaviour` and have our concrete behaviour implementation implements the interfaces.

![alt text](<sources/Intro To Design Patterns - Strategy Pattern/Screenshot 2026-02-19 at 2.35.53 PM.png>)

A cool thing about this design is that you can dynamically set behaviours during runtime as well.

What we're doing now also follows this principle:

> **Design Principle**: Program to an interface, not an implementation

This is basically saying that we should be more concerned with what a certain something can do and not how it does the job. When we do so, our ducks are untouched whenever we want to change something in our code creating a sort of independence between the two.

# Implementation

## Interfaces & Abstract Classes

```java
public abstract class Duck{
    Flybehaviour flyBehaviour;
    QuackBehaviour quackBehaviour;

    public void fly(){
        flyBehaviour.fly();
    }

    public void quackBehaviour(){
        quackBehaviour.quack();
    }

    public abstract void swim();
    public abstract void display();

    // getters, setters...
}

public interface FlyBehaviour{
    void fly(); // Or whatever type you want
}

public interface QuackBehaviour{
    void quack(); // Or whatever type you want
}
```

## Concrete Implementation

We'll just do the simple rubber ducky one

```java
public class RubberDuckie extends Duck{
    public RubberDuckie(FlyBehaviour flyBehaviour, QuackBehaviour quackBehaviour){
        this.flyBehaviour = flyBehaviour;
        this.quackBehaviour = quackBehaviour;
    }

    public void swim(){
        System.out.println("Rubber duckie floating...");
    }

    public void display(){
        System.out.println("Showing Rubber duckie...");
    }
}

public class NoFlyBehaviour implements FlyBehaviour{
    public void fly(){
        System.out.println("can't fly!");
    }
}

public class Squeak implements QuackBehaviour{
    public void squeak(){
        System.out.println("squeak squeak");
    }
}
```

## Using The Classes

```java
class Runner{
    public static void main(String[] args){
        RubberDuckie rubberDuckie = new RubberDuckie(new NoFlyBehaviour(), new Squeak());

        rubberDuckie.fly();
        rubberDuckie.quack();
    }
}
```

# The Big Picture: HAS-A vs IS-A

The big thing that we've done here is on the discussion of when to use IS-A or HAS-A relationship. As shown, HAS-A can be more flexible than IS-A because we're simply setting values. IS-A is more concrete and is still a good thing for when you're sure about something that's unchanging.

The thing that we just did, constructing a duck object by assembling it's parts/behaviour is what's called composition. In the world of design pattern, this one's preferred over HAS-A.

> **Design Principle**: Favor composition over inheritance.

Now, no matter what kind of ducks they throw at us, we can cope better

# References

1. Freeman, E. (2004). Head First Design Patterns. Sebastopol, CA: O’Reilly Media.
