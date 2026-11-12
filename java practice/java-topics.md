# Java Advanced – Topic List

## 1. Functional Programming
- [ ] Lambdas & method references
- [ ] Built-in functional interfaces (`Function`, `Supplier`, `Consumer`, `Predicate`…)
- [ ] Custom `@FunctionalInterface`, `default`/`static` interface methods
- [ ] Function composition (`andThen`, `compose`, `and`, `negate`)

## 2. Streams
- [ ] Lazy evaluation, intermediate vs terminal ops
- [ ] `map`, `flatMap`, `filter`, `reduce`, `collect`
- [ ] Collectors: `groupingBy`, `partitioningBy`, `toMap`, `joining`, `teeing`
- [x] Optional
- [ ] Parallel streams & pitfalls
- [ ] Newer: `takeWhile`, `toList()`, `mapMulti`, Gatherers

## 3. Modern Type Features
- [ ] Advanced enums, `EnumMap` / `EnumSet`
- [ ] Records
- [ ] Sealed classes / interfaces
- [ ] Pattern matching (`instanceof`, `switch`, record deconstruction)
- [ ] Switch expressions & `yield`
- [ ] `var`, text blocks

## 4. Less-Common but Important
- [x] Generics (bounds, wildcards, PECS, erasure, raw types)
- [ ] Inner / static nested / local / anonymous classes, effectively final
- [ ] try-with-resources, `AutoCloseable`, suppressed exceptions
- [ ] Immutable collections (`List.of`, `copyOf`) vs `unmodifiableList`
- [ ] `equals` / `hashCode` contracts
- [ ] Varargs & `@SafeVarargs`
- [ ] Annotations & reflection basics

## 5. Concurrency
- [ ] `CompletableFuture`
- [ ] Virtual threads
- [ ] Structured concurrency & scoped values
