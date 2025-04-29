# Static methods and attributes are evil

## Static Methods

<table><tr>
    <th> Bad ❌</th>
    <th> Good ✅</th>
</tr>
<tr>
<td>

```java
class Circle {
    public float radius;
}
class GeometryUtils {
    static float calcSquare(Circle c) {
        return c.radius * c.radius * 3.14;
    }
}
```

</td><td>

```java
class Circle {
    private float radius;   // State
    float square() {        // Bihavior
        return c.radius * c.radius * 3.14;
    }
}


```

</td></tr></table>

Wat's wrong with "Utils"

1. They are <u> unbreakable </u> dippendancy
    - objects knowing too mach about each other, this deep deppendancy difficult to breake
    - for testing purposes
    - for reusing
2. They are <u> eager </u>, not lasy
3. They are <u> not cohesive </u>
    - coupling: mast be as less as posible  
        class 1  <- knows ebout each other -> class2
        class 1 & class 2 has unbbrakable dependency especially during testing
    - cohesive: the higher is the better  
        hor example we have a class Circle and couple of methods to calculate a circle - so there are cohesive, but if we add some thing like delete file - it's not a cohesive

## Static Attributes

## Example Fastjson
