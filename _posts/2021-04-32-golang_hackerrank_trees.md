---
layout: default
author: Fulton Byrne
title: "HackerRank Fail: Subtrees and Paths"
---

I have put myself on a path towards improving my ability to tackle 
coding challenges. Many companies use the sort of questions one may
find on HackerRank to assess candidates. I was once in the party
that abstained from these questions; forsaking them as trivial tests
that overlook practical knowledge (Containers/Networking/OS/Kubernetes)
and pressure cook candidates in an unreasonable time frame. What changed my mind?
A few things:

1. There are many exciting opportunities that actually apply these skills.
2. As someone who conducts interviews I can see that this is a really great
way to see how well someone knows a language and their Computer Science fundamentals.
3. Gayle Laakmann McDowell's "Cracking the Coding Interview" reframed the expectations
for me.

Before reading "Cracking the Coding Interview" (CCI) I imagined the best approach
was to hurl myself at HackerRank challenges and memorize the solutions to each one
in the hopes I will be able to verbatim reproduce the solution during an interview.
This is the worst way to improve one's ability to handle these coding challenges.
Memorization means you don't understand the code or the solution. The most valuable
part of the interview comes from planning and discussing your solution: showing that
you know CS fundamentals and can apply them. Most interviews are too short to code
a proper solution. The code is mostly show your knowledge of the language and your
ability to code cleanly.

I was attempting the problem [_Subtrees and Paths_]
on HackerRank today. I failed miserably on a number of fronts and wanted to review my
performance using [CCI's 7 step framework].

## 1. Listen

On face value [_Subtrees and Paths_] its very
easy to get locked into parsing the input of the
problem and it's not unreasonable to do so.

I wouldn't try playing with any inputs at this stage
or walking through anything in hindsight. Also,
the worst thing I do is try to start coding at this
stage. I spent a good amount of time coding
how to parse the input before I fully understood the
problem.

This lead to a huge gaffe where I _misunderstood_
the max function the challenge asks you to implement
to be the _sum_ of all nodes in a subtree. This
is entirely incorrect. It wants the _max_ node between
any two arbitrary nodes within the entire tree.

Other items I missed:
1. Node 1 is the root of the tree
2. The challenge does not specify if the
tree is ordred or not, but it seems like it might
be.
3. The challenge does not indicate that it is a binary
search tree, but it seems as if it may be.
   + Though the discussion section indicates it is 
   likely not binary.

### How to improve?

First, focus on the interesting parts of the problem.
Input parsing is not interesting. Look for the
opportunity to discuss data structures and algorithms
with the interviewer. This is how to score big points
very early on.

For instance discussing how I would find a subtree
and then walk its nodes when processing the `add`
command. What algorithm would I use? What is its time
complexity?

I realized I was very unready for this sort of
conversation. My main takeway is to review trees
thoroughly. Especially different search algorithms
involving multi-child/unordered trees.

## 2. Example
HackerRank usually provides a solid example to help
understand the problem better.

I did OK here. For example my `insert` function to 
add nodes works properly I believe mostly due
to my walking through the example and noticing that
there was no ordering to each row element. 

I opted to check if either node existed and then insert 
based on that. For example, `1 2` is inserted first and 
then later `5 1`. This may also indicate that edges
should be bidirectional which may help for the `max`
function. 

If I had read the part about `1` being the
root I would definitely understand that `5 1` attaches
`5` as a child of `1`, but what if it were `5 2`?
This problem is very ambiguous and would be good to
discuss with the interviewer more. This would
indicate an attention to detail and prevent expensive 
errors further down.

### How to improve?
First off, the errors that occurred during the
listening section made the example section far worse.

I did not walk through `add` or `max` at all.
I should have written down a few different examples
on paper or in comments.


## 3. Brute Force
First you need a tree structure. I was writing in 
Go so I opted for a new `type` that held the
`key`, `value`, and `children` of the node.

`add` is fairly simple. Find a `node` and then
iterate recursively through all its children.
I would need to know more characteristics of the tree
to discuss run times, but if there's no order
it's probably `O(N)` as the worst case. You're just
iterating through all nodes until you find the
designated root. Then you iterate through it and
its children adding the value to each.

`max` is a good bit different. You have to find
the least common ancestor (LCA) for 2 nodes, keeping 
track of the `max` while you look for the LCA.
I'm not sure of the run time here or the implementation.

### How to improve?
1. Read up on tree search algorithms.
2. Read up on least common ancestory algorithms.

## 4. Optimize

I would have imagined I would have written some
code at this point, but nope!

I should:
1. Look for unused info! ****
2. Solve it manually on an example.
 Maybe a different example.
3. Solve it "incorrectly"

### How to improve?
CCI has a lot of walkthroughs for the Optimize step
that review more in depth techniques for optimizing.
Walk through these.

## 5. Walk Through
Review everything and ensure I understand what I'm
building in detail.

## 6. Implement

Write beautifuly code. Know the language and libraries
better.

Will the interviewer be OK with me using a library
to back my data structures and their algorithms?
Then use [GoDs](https://github.com/emirpasic/gods).

If I have to implement different procedures like
Breadth/Depth First Search I should know how to do
those cleanly.

Also, read a book on Go. Know the language in depth
and show that I know it deeply.

## 7. Test

1. Code Review
2. Have a check list of common go issues handy.
   + nil pointers
   + pointer mistakes
3. Small test cases
4. Special Cases


## Bonus

I learned that there was quite a bit about Go I did
not know as I was approaching this problem. Here's
the list:

1. If passing a slice to a function and you expect
to append elements the slice you should pass a pointer
to the slice if you want to modify the slice in place.
([Source](https://medium.com/swlh/golang-tips-why-pointers-to-slices-are-useful-and-how-ignoring-them-can-lead-to-tricky-bugs-cac90f72e77b))

There were some good blog posts about writing trees
in Golang:

* https://ieftimov.com/post/golang-datastructures-trees/
* https://flaviocopes.com/golang-data-structure-binary-search-tree/

And Geeks for Geeks had some good info on BFS

https://www.geeksforgeeks.org/print-the-path-between-any-two-nodes-of-a-tree-dfs/



[_Subtrees and Paths_]: https://www.hackerrank.com/challenges/subtrees-and-paths/problem
[CCI's 7 step framework]: https://youtu.be/GKgAVjJxh9w