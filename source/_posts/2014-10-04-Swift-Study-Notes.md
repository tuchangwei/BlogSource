title: Swift Study Notes
date: 2014-10-04 21:17:32
tags: Swift
---

# 402-hd-introduction_to_swift.mov

1. This is a general principle in Swift, that we prefer immutability or constants by default and only really opt into mutablility or variables where things actually need to change. Now this makes your code safer in a multi-threaded environment. It also means that Swift can optimize your code more effectively because it knows what isn't going to change and it just generally makes your code more readable, makes your intent clearer that you're saying what is and isn't going to vary.


1. Swift initializers don't return a value. Rather, their main role to make sure that every stored property has a value by the time its initialization completes.




