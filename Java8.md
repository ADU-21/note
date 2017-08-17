# Java 8 新特性

## Lambda function

1. **functional interface**

Like this: ```input -> body ```

More like this:

```
(arg1, arg2...) -> { body }

(type1 arg1, type2 arg2...) -> { body }
```

For example：

```
() -> 42

a -> return a*a

(int a, int b) -> {  return a + b; }

() -> System.out.println("Hello World");

(String s) -> { System.out.println(s); }
```

2. **foreach**

```
//Old way:
List<Integer> list = Arrays.asList(1, 2, 3, 4, 5, 6, 7);
for(Integer n: list) {
   System.out.println(n);
}

//New way:
List<Integer> list = Arrays.asList(1, 2, 3, 4, 5, 6, 7);
list.forEach(n -> System.out.println(n));


//or we can use :: double colon operator in Java 8
list.forEach(System.out::println);
```

3. **map**

```
// old way
Set<String> keySet = map.keySet();
for (String s : keySet) {
    System.out.print(s + ":" + map.get(s));
}

// java8
map.forEach((k, v) -> System.out.print(k + ":" + v));
```

## stream


