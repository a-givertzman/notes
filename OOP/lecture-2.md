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

1. They are __unbreakable__ dippendancy
2. They are __eager__, not lasy
3. They are __not cohesive__


## Static Attributes


## Example Fastjson
