###### 1. Collector
```
/**
 * 自定义mapper的Collector
 *
 * @param resultMapper resultMapper
 * @param <T>          <T>
 * @param <R>          <R>
 * @return list
 */
public static <T, R> Collector<T, ?, List<R>> mapperCollector(Function<T, R> resultMapper) {
    BiConsumer<List<R>, T> biConsumer = (list, i) -> list.add(resultMapper.apply(i));
    BinaryOperator<List<R>> combiner = (a, b) -> {
        a.addAll(b);
        return a;
    };
    return Collector.of(ArrayList::new, biConsumer, combiner, i -> i);
}


/**
* collector取一个（最后一个）
*
* @param secondMapper
* @param resultMapper
* @param <T>
* @param <S>
* @param <R>
* @return Collector
*/
private static <T, S, R> Collector<T, ?, Map<S, R>> collectorSingle(Function<T, S> secondMapper
		, Function<T, R> resultMapper) {
	return Collectors.toMap(secondMapper, resultMapper, (key1, key2) -> key2);
}


/**
* collector取集合
*
* @param compareMapper
* @param <T>
* @param <S>
* @return
*/
public static <T, S> Collector<T, ?, Map<S, List<T>>> collectorList(Function<T, S> compareMapper) {
    return Collectors.groupingBy(compareMapper, Collectors.toList());
}


/**
* customize collector
*/
Collectors.of(Supplier<A> supplier,
                BiConsumer<A, T> accumulator,
                BinaryOperator<A> combiner,
                Function<A, R> finisher,
                Characteristics... characteristics)


/**
*  group by multiple fields(single level map)
*/
public static <T> Map<String, List<T>> groupByMultipleField(List<T> data, List<Function<T, ?>> groupFields) {
	Function<T, String> groupFunction = i -> {
		StringBuilder key = new StringBuilder();
		groupFields.forEach(v -> key.append(v.apply(i)));
		return key.toString();
	};
	if (data != null) {
		return data.stream().collect(Collectors.groupingBy(groupFunction));
	} else {
		return new HashMap<>(0);
	}
}


/**
*  group by multiple fields(multiple level map)
*/
public static <F, S, T> Map<F, Map<S, List<T>>> groupByMultipleField(List<T> data, Function<T, F> groupField1, Function<T, S> groupField2) {
	return data.stream().collect(Collectors.groupingBy(groupField1, Collectors.groupingBy(groupField2)));
}

```

###### 2. Comparable

```
/**
* 排序（允许空值）
*
* @param list          list
* @param compareMapper compareMapper
* @param resultMapper  resultMapper
* @param sort          DESC或者ASC（lambda默认为ASC）
* @param <T>           <T>
* @param <R>           <R>
* @return list
*/
public static <T, R, U extends Comparable<? super U>> List<R> sort(List<T> list, Function<T, U> compareMapper
        , Function<T, R> resultMapper, Sort.Direction sort) {
    Comparator<? super T> comparator = Comparator
        .comparing(compareMapper, Comparator.nullsFirst(Comparator.naturalOrder()));
    comparator = sort == Sort.Direction.DESC ? comparator.reversed() : comparator;
    return getNonEmptyList(list).stream().sorted(comparator).map(resultMapper).collect(Collectors.toList());
}
```

###### 3. Predicate

```
/**
* batch filter
*
* @param sourceList sourceList
* @param listFilter listFilter
* @param <T>        <T>
* @return list
*/
public static <T> List<T> filter(List<T> sourceList, List<Predicate<T>> listFilter) {
    return Optional.of(sourceList).map(list -> {
        Stream<T> stream = list.stream();
        for (Predicate<T> filter : listFilter) {
            stream = stream.filter(filter);
        }
        return stream.collect(Collectors.toList());
    }).orElse(new ArrayList<>());
}


```

###### 3. Function
```
/**
* return i->i
*/
Function.identity();
```

