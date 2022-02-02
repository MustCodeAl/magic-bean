# Magic Bean
[![](https://jitpack.io/v/dev.mccue/magic-bean.svg)](https://jitpack.io/#dev.mccue/magic-bean)

A very basic library which will generate POJOs. 

Requires Java 17+.

### Maven
<details>

```xml
<repositories>
    <repository>
        <id>jitpack.io</id>
        <url>https://jitpack.io</url>
    </repository>
</repositories>
```
```xml
<dependency>
    <groupId>dev.mccue</groupId>
    <artifactId>magic-bean</artifactId>
    <version>3.0.2</version>
    <scope>provided</scope>
</dependency>
```
</details>


## What this does
This uses an annotation processor to generate a class which can
be extended to automatically derive the boilerplate code for 
- getters and setters
- equals and hashCode
- toString
- an all argument constructor

The primary goals of this library are
1. Boilerplate reduction for code that needs "dumb" POJOs
2. Demonstrate that annotation processors and source code 
generation aren't that scary
3. Cover some of the use cases of [lombok](https://projectlombok.org/) without
[the compiler hacking it does](https://github.com/projectlombok/lombok/issues/2681)

The non-goals of this library are
1. To provide a tool which fits all use cases. (conflicts with goal #2)
2. Provide a way to generate immutable value objects. Use [records](https://dev.java/learn/using-record-to-model-immutable-data/),
[immutables](https://immutables.github.io/), or elbow grease for that.
3. Support old Java versions.

PRs welcome for
- carrying over annotations to generated get and set methods.

## Usage

### Basic Example

#### I receive
```java
import dev.mccue.magicbean.MagicBean;

import java.util.List;

@MagicBean
public final class Example extends ExampleBeanOps {
    int x;
    String name;
    List<String> strs;
}
```

#### You receive
```java
sealed abstract class ExampleBeanOps permits Example {

    /**
     * Get the current value for x.
     */
    public int getX() {
        return ((Example) this).x;
    }

    /**
     * Set the current value for x.
     */
    public void setX(int x) {
        ((Example) this).x = x;
    }

    /**
     * Get the current value for name.
     */
    public java.lang.String getName() {
        return ((Example) this).name;
    }

    /**
     * Set the current value for name.
     */
    public void setName(java.lang.String name) {
        ((Example) this).name = name;
    }

    /**
     * Get the current value for strs.
     */
    public java.util.List<java.lang.String> getStrs() {
        return ((Example) this).strs;
    }

    /**
     * Set the current value for strs.
     */
    public void setStrs(java.util.List<java.lang.String> strs) {
        ((Example) this).strs = strs;
    }
    
}
```

### Complete Example

#### I receive 
```java
import dev.mccue.magicbean.MagicBean;

import java.util.List;

@MagicBean(
        generateAllArgsStaticFactory = true,
        generateEqualsAndHashCode = true,
        generateToString = true
)
public final class Example extends ExampleBeanOps {
    int x;
    String name;
    List<String> strs;
}

```

#### You receive
```java 
sealed abstract class ExampleBeanOps permits Example {

    /**
     * Creates an instance of Example.
     */
    public static Example of(
        int x,
        java.lang.String name,
        java.util.List<java.lang.String> strs
    ) {
        var o = new Example();
        o.setX(x);
        o.setName(name);
        o.setStrs(strs);
        return o;
    }

    /**
     * Get the current value for x.
     */
    public int getX() {
        return ((Example) this).x;
    }

    /**
     * Set the current value for x.
     */
    public void setX(int x) {
        ((Example) this).x = x;
    }

    /**
     * Get the current value for name.
     */
    public java.lang.String getName() {
        return ((Example) this).name;
    }

    /**
     * Set the current value for name.
     */
    public void setName(java.lang.String name) {
        ((Example) this).name = name;
    }

    /**
     * Get the current value for strs.
     */
    public java.util.List<java.lang.String> getStrs() {
        return ((Example) this).strs;
    }

    /**
     * Set the current value for strs.
     */
    public void setStrs(java.util.List<java.lang.String> strs) {
        ((Example) this).strs = strs;
    }

    @Override
    public boolean equals(Object o) {
        if (o == null || !(o instanceof Example other)) {
            return false;
        }
        else {
            return java.util.Objects.equals(((Example) this).x, other.x) && 
                   java.util.Objects.equals(((Example) this).name, other.name) && 
                   java.util.Objects.equals(((Example) this).strs, other.strs);
        }
    }

    @Override
    public int hashCode() {
        return java.util.Objects.hash(
                ((Example) this).x,
                ((Example) this).name,
                ((Example) this).strs
        );
    }

    @Override
    public String toString() {
        return "Example[" + "x=" + ((Example) this).x +
                     ", " + "name=" + ((Example) this).name +
                     ", " + "strs=" + ((Example) this).strs + "]";
    }

}
```

## Usage with frameworks
### [JPA / Hibernate](./usage/jpa.md)


## Customizing
This library is just over 300 lines of Java contained within a single file.
If it doesn't do exactly what you want, feel free to make a PR or fork and
make your own edits.
