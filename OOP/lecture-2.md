# Static methods and attributes are evil

## Static Methods


<table><tr>
    <th> algorithm thinking </th>
    <th> object thinking </th>
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
    float radius;
    float square() {
        return c.radius * c.radius * 3.14;
    }
}


```

</td></tr></table>

## Static Attributes


## Example Fastjson
