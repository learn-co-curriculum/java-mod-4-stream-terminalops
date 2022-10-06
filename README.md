# Stream Terminal Operations

## Learning Goals

- Use terminal operations that return an int: count, max, min, 
- Use terminal operations that return a boolean: allMatch, anyMatch, noneMatch
- Use terminal operations that work with a consumer: forEach
- Use terminal operations that return an array: toArray

## Introduction

A stream pipeline consists of zero or more intermediate operations followed by a terminal operation.
Each intermediate operation returns a new stream that is consumed by the next operation in the pipeline.
A terminal operation ends a pipeline and may traverse a stream to produce a result or a side-effect. 
A stream is consumed and can no longer be used after the terminal operation.
A new stream must be created to traverse the same data source again.

## count

The terminal operation `count` returns a `long` value representing the number of elements in the stream.

```java
import java.util.List;
import java.util.stream.Stream;

public class Example {
    public static void main(String[] args) {
        List<Integer> numbers = List.of(-50, 20, 12, 4, -9);
        long numCount = numbers.stream().count();
        System.out.println(numCount); //5

        Stream<String> stream2 = Stream.of("hello", "goodbye");
        System.out.println(stream2.count()); //2

    }
}
```

## max and min

The terminal operation `max` takes a `Comparator` as a parameter and returns a `Optional<T>` representing
the maximum element of the stream according to the provided Comparator.

- `T` is the type of the elements in the stream's data source.
- The `Comparator` corresponds to the data source type `T`, i.e. `Integer::compare` for an `Integer` array or list.
- The return type is `Optional<T>` since the operation could be called on an empty collection, in which case there is no max value.

```java
import java.util.ArrayList;
import java.util.List;
import java.util.Optional;

public class Example {
    public static void main(String[] args) {
        List<Integer> ages = List.of(12, 55, 37, 9);
        Optional<Integer> maxNumber = ages.stream().max(Integer::compare);
        System.out.println(maxNumber);   //Optional[55]

        //Try with an empty list, there is no max value!
        List<Double> prices = new ArrayList<Double>();
        Optional<Double> maxPrice = prices.stream().max(Double::compare);
        System.out.println(maxPrice);    //Optional.empty
    }
}
```

The `min` operation is similar in that it takes
a `Comparator` as parameter and returns `Optional<T>`, the minimum element
in the stream based on the `Comparator`.

## forEach

The `forEach` operation takes a `Consumer` as a parameter
and performs the consumer function on each element in the stream.

In the example below, a lambda expression is passed as the consumer
into the first call to `forEach`, while a method reference is passed
into the second call.

```java
import java.util.List;

public class Example {
    public static void main(String[] args) {
        List<Integer> ages = List.of(12, 55, 37, 9);
        
        System.out.println("Pass lambda expression as consumer");
        ages.stream().forEach(s -> System.out.println("next number is " + s));
        
        System.out.println("Pass method reference as consumer");
        ages.stream().forEach(System.out::println);
    }
}
```

The program prints:

```text
Pass lambda expression as consumer
next number is 12
next number is 55
next number is 37
next number is 9
Pass method reference as consumer
12
55
37
9
```

## allMatch, anyMatch, noneMatch

- The `allMatch` operation takes a `Predicate` parameter and returns a `boolean` 
  indicating whether all elements match the predicate.
- The `anyMatch` operation takes a `Predicate` parameter and returns a `boolean`
  indicating whether at least one element matches the predicate.
- The `noneMatch` operation takes a `Predicate` parameter and returns a `boolean`
  indicating whether no elements match the predicate.

```java
import java.util.function.Predicate;

public class Example {
    public static void main(String[] args) {
        List<Integer> ages = List.of(22, 55, 37, 19);
        Predicate<Integer> atleast18 = i -> i >= 18;
        Predicate<Integer> allOdd = i -> i % 2 == 1;
        Predicate<Integer> over60 = i -> i > 60;

        System.out.println("All at least 18? " + ages.stream().allMatch(atleast18));  //true
        System.out.println("All odd? " + ages.stream().allMatch(allOdd)); //false
        System.out.println("Any odd? " + ages.stream().anyMatch(allOdd)); //true
        System.out.println("None over 60? " + ages.stream().noneMatch(over60)); //true
    }
}
```

The program prints:

```text
All at least 18? true
All odd? false
Any odd? true
None over 60? true
```

### matching with primitive data types

Recall that we create primitive streams `IntStream`, `DoubleStream`, and `LongStream`
when the elements are primitive values (int, double, long).  

Stream matching operations require a primitive predicate `IntPredicate`, `DoublePredicate`, and `LongPredicate`
when the stream elements are primitive values.

```java
import java.util.Arrays;
import java.util.function.IntPredicate;
import java.util.stream.IntStream;

public class ExampleIntPredicateMatch {
    public static void main(String[] args) {
        int[] grades = {90, 75, 100, 58};
        IntPredicate isPassing = i -> i >= 60;
        IntStream gradesStream = Arrays.stream(grades);
        System.out.println("All at least 18? " + gradesStream.allMatch(isPassing));  //false
    }
}
```

## toArray

The `toArray` operation returns an array of elements in the stream.

### Calling toArray on primitive streams

Calling `toArray` on a primitive stream produces an array of the underlying primitive data type.

In the example below, the stream has type `IntStream` as the underlying data source is an array of `int`.
The `toArray` operation produces a new array of `int`.

```java
import java.util.Arrays;
import java.util.stream.IntStream;

public class ExampleToIntArray {
    public static void main(String[] args) {

        //int[] --> IntStream --> int[]

        int[] array1 = {28, 43, 19, 100};
        //create IntStream since the array holds primitive int
        IntStream stream = Arrays.stream(array1);

        //create a new array from the stream
        int[] array2 = stream.toArray();

        //confirm 2 separate arrays
        array1[0] = 99;
        array2[0] = 12;
        System.out.println(Arrays.toString(array1));  //[99, 43, 19, 100]
        System.out.println(Arrays.toString(array2));  //[12, 43, 19, 100]

    }
}
```

### Calling toArray on object streams

Calling `toArray()` on a stream with elements of a class type will create an array `Object[]`.

In the example below, the stream's data source is a list of `Integer`, thus the `toArray`
returns a value of type `Object[]`.

```java
import java.util.Arrays;
import java.util.List;
import java.util.stream.Stream;

public class ExampleToObjectArray {
    public static void main(String[] args) {

        //List<Integer> --> Stream<Integer> --> Object[]

        List<Integer> list = List.of(12, 55, 37, 9);
        Stream<Integer> stream = list.stream();

        //toArray by default returns an array of Object when stream elements are not primitive data type
        Object[] objectArray = stream.toArray();
        System.out.println(Arrays.toString(objectArray));
    }
}
```

To return an array `Integer[]` rather than `Object[]`, we must pass a generator function `Integer[]::new`:

```java
import java.util.Arrays;
import java.util.List;
import java.util.stream.Stream;

public class Example {
    public static void main(String[] args) {

        //List<Integer> --> Stream<Integer> --> Integer[]

        List<Integer> list = List.of(1,2,3,4);
        Stream<Integer> stream = list.stream();

        //pass a type generator function into toArray
        Integer[] intArray = stream.toArray(Integer[]::new);
        System.out.println(Arrays.toString(intArray));

    }
}
```

### IllegalStateException

Once a terminal operation completes, an `IllegalStateException` is thrown if additional operations are called on the stream.
For example, the following code attempts to call `forEach` after calling `count` on the stream:

```java
import java.util.stream.Stream;

public class Example {
  public static void main(String[] args) {
    String[] greetings = {"hi", "hello", "howdy"};
    Stream<String> greetStream = Arrays.stream(greetings);

    //Consume greetStream
    System.out.println(greetStream.count()); //3

    //Throws java.lang.IllegalStateException
    greetStream.forEach(System.out::println);
  }
}
```

Running the program produces as output:

```text
3
Exception in thread "main" java.lang.IllegalStateException: stream has already been operated upon or closed   
```

A new stream must be created if further processing of the data source is required:

```java
import java.util.Arrays;
import java.util.stream.Stream;

public class ExampleNoException {
    public static void main(String[] args) {
        String[] greetings = {"hi", "hello", "howdy"};
        Stream<String> greetStream = Arrays.stream(greetings);

        //Consume greetStream
        System.out.println(greetStream.count()); //3

        //Create another stream
        greetStream = Arrays.stream(greetings);
        greetStream.forEach(System.out::println);  //hi  hello  howdy
    }
}
```

## Resources

- [Java 11 Stream](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/stream/Stream.html)