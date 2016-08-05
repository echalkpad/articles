# Designing a State Machine without conditionals

[Original URL](http://jjcorrea.github.io/2015/05/05/designing-a-state-machine-without-conditionals/)

> 05 May 2015 I've recently worked in a project, where we had the opportunity to design and implement an SMS sending component, and I'd like to share our experience while elaborating the solution, and...

<span class="post-date">05 May 2015</span>

 I've recently worked in a project, where we had the opportunity to design and implement an **SMS sending component**, and I'd like to share our experience while elaborating the solution, and also a quick example.

The general idea was to generate calls to an external SMS sending API when certain events occur on 3 distinct web applications (a.k.a Channels). Given an event, the state transition it generates, and also depending on some rules (**State Machine**) it may be the case to generate an SMS send or not.

There were other internals which I'm going to bypass on this article. For sake of focus this article will go through the State machine internal design only, showing how we made it without handling any sort of conditionals directly.

## What is wrong with my IFs?

> Oh my god. First they avoided goto. Then they avoided switch. Then they started eliding pointers. Now they avoid ifs! - Quoted from StackOverflow

Well, this brings another good discussion to the table... I might post it later. Meanwhile, here are some references around this topic:

## The design components

Having a good design, properly **separating the concerns** and making them **explicit** for the team through a **clear communication** are some of the challenges around solving any kind of software problem, and it was not different on this case.

## Our constraints

- 5 developers on the team which were supposed to work simultaneously on this codebase
- Not much time available
- Parallelize the development
- Components isolation
- Testability
- Readability

In short, we wanted the things the more meaningfull and changeable as possible (Clean code).

![Design Components](http://jjcorrea.github.io/assets/State-Machine-Design-Components.png)

These are the main components which were designed (the state machine **core**):

- **Transitions**: Given the state machine nature, we need to someway perform state transitions.
- **Predicates**: Having a complex input type which needed to be evaluated on several different aspects, there was also a concept of predicates, which indicates whether a transition should occur or not.
- **Rules**: Predicate components.

![Design Components Interaction](http://jjcorrea.github.io/assets/State-Machine-Design-Interaction.png)

## Show me the code (C# - Simplified version)

(Assuming the statuses as strings).

### EntryPoint

```
public class EntryPoint {
 // ...
 public List<string> UpdateStatus(Input input) {
 return transitions.All(input).Where(x => x.Item1 == true)
 .Select(x => x.Item2())
 .DefaultIfEmpty(new List<string>());
 }
}
```

### Transitions

```
public class Transitions {
 public List<Tuple<bool, Func<string>>> All(Input input) {

 return new List<Tuple<bool, Func<string>>>(){
 new Tuple<bool, Func<string>>( 
 predicates.FirstCase(input), Status.NextStateFn
 ),
 new Tuple<bool, Func<string>>( 
 predicates.SecondCase(input), Status.PreviousStateFn
 )};
 }
}
```

### Predicates

```
public class Predicates {
 // ...

 public bool FirstCase(Input input) {
 return rules.EmptyStatus(input.status) && rules.IsFirstEventType(input.type);
 }

 public bool SecondCase(Input input) {
 return rules.EmptyStatus(input.status) && rules.IsSecondEventType(input.type);
 }

}
```

### Rules

```
public class Rules {
 // ... 

 public bool EmptyStatus(string status) {
 return status.Equals("-");
 }

 public bool IsFirstEventType(string eventType) {
 return EventTypes.FirstType().Contains(eventType);
 }

 public bool IsSecondEventType(string eventType) {
 return EventTypes.SecondType().Contains(eventType);
 }
}
```

### The design outcomes

- A **good design** helps the team to **code better** and more **efficiently**
- A great **communication** is as important as a great design.
- As well as continuous **involvement** during the development
- We delivered the module with more than **250 (real) unit tests**
- Giving meaningfull names through a **centralized point-of-control** made it a lot easier to handle changes and properly understanding the context.
- The **time spent** on development was much lower than we were expecting
- There were **changes** to the predicates & rules, and all the team was pretty confident to perform them due to the test coverage.

## But... This is not a silver bullet

If you thought something similar to:

> New pattern, cool I'll start using it everywhere

> This will guarantee my job security. I'll use it with my other [Unmaintainable code](https://www.thc.org/root/phun/unmaintain.html) techniques

You shouldn't. Its good to remind that this solution was created based on several considerations mainly driven by the problem nature & constraints.

On the other hand, if you look at this as a hammer, which make everything magically turn into a nail, you just got it all wrong.
