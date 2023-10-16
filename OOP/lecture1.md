# Algorithms hurt object thinking

> The complexiti of the object oriented code  
> remains it's primary drowback

## Intent

> "The contemporary mainstream understending of  
> objects (wich is not behavioral) is but a pale shadow  
> of the original idea and anti-ethical to the original intent"  
>  
> — Dawid West  
> Object thinking, 2004  
**The book to read !**

conversation with Dr. West:
[part I](https://www.youtube.com/watch?v=s-hdZZzMCac) and
[part 2](https://www.youtube.com/watch?v=bW5K5cJ-AVs)

> "A system is a composition of objects that  
> are abstractions, wich hide data  
> and expose behavior"  
>  
> — Yegor: "How i anderstand the original idea"

### The Object

    - composition
        the software must be a composition of the objects
    - abstraction  
        this is abstraction of the real objects,  
        must have as much data and behavior attributes  
        as it is necessary and enough for the current task
    - hide data  
        the object must hiding data as much as possible
    - expose behavior
        instead of the data sharing, object exposing bihavior

### Object properties

- Identity  
    meaning that objects are exact the same or not

```java
c1 = new Circle(r: 42)
c2 = new Circle(r: 42)
c1 != c2
```

- State  
    meaning that abjects has equal content or not

```java
c1 = new Circle(r: 42)
c2 = new Circle(r: 42)
c1.radius() == c2.radius()
```

- Behavior  
    meaning that only behavior of the object  
    is the same / equal or not

```java
c1 = new Circle(r: 42)
c2 = new Circle(r: -42)
c1.square() == c2.square()
```
