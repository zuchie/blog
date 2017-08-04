---
layout: post
title:  Value and reference semantics in for-in loop 
date:   2017-08-03 01:34:00 -0700
comments: true
categories: Swift 
---

![for-in](/assets/img/for-in-loop/for-in.png)

At the beginning of the book `RxSwift - Reactive Programming with Swift`, it asks what's the prints from this code snippet: 
```swift
var array = [1, 2, 3]
for number in array {
  print(number)
  array = [4, 5, 6]
}
print(array)
```
I wasn't sure about the correct answer at that time. So I took some time looked into the `for-in` loop.
<!--more-->

Types that conform to Sequence protocol can use for-in to iterate over their items. Like the example illustrated from Apple documentation: 
![for-in-apple](/assets/img/for-in-loop/for-in-apple.png)

*Behind the scenes, Swift uses the animals array's iterator to loop over the contents of the array.*

![for-in-iterator](/assets/img/for-in-loop/for-in-iterator.png)

So, what do those instance methods `makeIterator()` and `next()` look like? 
Let's take a look at an implementation from Apple's IteratorProtocol:

![iterator](/assets/img/for-in-loop/iterator.png)

Together with this implementation, we realize that because `array` is of value type, it's been copied for the for-in loop, and it's the copy that's been iterated over. As a result, any mutations to the instance `array` inside of for-in don't affect the iteration of the copy and its items. So the results should be:
```swift
1
2
3
[4, 5, 6]
```
If we want to mutate the array inside for-in loop, here is a way:
```swift
var array = [1, 2, 3]
for i in 0..<array.count {
  print(array[i])
  array[1] = 0
}
print(array)

1
0
3
[1, 0, 3]
```

Also, because the `Countdown` is of type struct. It has value semantics. The statement `countdown0/1.start = 0` inside the for-in don't affect the iteration and its items. So we could expect the prints should be:
```swift
3
2
1
********
3
2
1
```
What if we change `Countdown` from struct to class?

![iterator-class](/assets/img/for-in-loop/iterator-class.png)

Since now it's with reference semantics instead of value semantics of `Countdown`, the results changed to:
```swift
3
********
3
```
because of the statement `countdown0/1.start = 0`. 
