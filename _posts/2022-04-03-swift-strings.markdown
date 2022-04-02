---
layout: post
title:  "Starting with Swift Strings"
date:   2022-04-03 14:33:17 +1300
categories: swift strings
---


# Starting with Swift Strings


## Indexing Strings
My first experience with strings in Swift was finding out I couldn't index them by an integer.

```swift
var test = "Hello world!"
print(test[3]) //won't compile

```
And then finding out via a Stack Overflow question that I could provided I added an extension!


```swift
extension String {
    subscript(offset: Int) -> Character {
        self[index(startIndex, offsetBy: offset)]
    }
}

var test = "Hello world!"
print(test[3])

```
Which seemed weird right? Why wouldn't the library writers include such a simple extension. 

Then I found this blog post 
<https://maximeremenko.com/string-random-access>
which basically answered my question. Unlike Arrays, Strings are not stored in a manner that allows quick (constant time) access to the nth Character of the String. I don't know the implementation details but I'm assuimg that in order to find the n^(th) Character in a String the library needs to iterate through all preceeding characters (as one would find the n^(th) element in a linked list) and the basically this is due to using variable length encoding for efficient unicode storage. Variale length encoding means it is impossible to know how much storage space a random character would use. 

This means that the code

```swift
let test = "Hello! World!"

for i in 0..<test.count {
    if test[i] == "!" {
        print("Exclamation mark found")
    }
}

```
probably runs in O(n^(2)) time and not O(n) as one might assume. Instead one should write something like this.

```swift
let test = "Hello! World!"
var i = test.startIndex

while i < test.endIndex {
    if test[i] == "!" {
        print("Exclamation mark found")
    }
    i = test.index(after: i)
}
```
Here I'm hoping that test.endIndex is quick to call and doesn't require iterating to the end of the string (or if it is then the call is only made once as test is marked as constant).

If one does want quick access to Characters in a string, then it is possible trade this off against greater memory usage by using an Array of Characters instead. 

## Iterating without loops

Coming from an R programming background, I was excited to learn how much can be achieved with the `map` and `reduce` methods in Swift. As an example here is simple way to count the number of differences in two strings of equal length using `reduce`.

```swift
func getDifferences(_ a: String, _ b: String) -> Int {
    assert(a.count == b.count)
    return zip(a, b).reduce(0, { numDifferences, elements in
        elements.0 == elements.1 ? numDifferences : numDifferences + 1
    })
}
```



