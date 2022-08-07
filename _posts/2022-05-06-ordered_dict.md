---
layout: default
author: Fulton Byrne
title: "Python Ordered Dict"
---
There's a very popular coding challenge where you are asked to implement a Least Recently Used Cache that has both `O(1)` get and `O(1)` insert.
`O(1)` get and put imply either a `hashmap` and we can exclude an `array` since elements must be associated by a key that may or may not lie within our cache's capacity.
"least recently used" indicates a secondary structure for prioritizing elements since `hashmap`'s do not inherently have ordering.

A `heap` may make a lot of sense as they often represent priority queues, but many operations may take `O(log N)`. Queues and Stacks are out as well because 
nodes inside the structure need to be reordered often. Frequent deletions of internal nodes should prime you to consider a doubly linked list since deletions there
take `O(1)` time. On the other hand a singly linked list takes `O(N)` time to delete a node because the algorithm must search through the entire list.

Ultimately the design comes down to a keyed `hashmap` that points to nodes in a `doubly linked` list that contain the values. The cache object also stores
references to the head and tail of the list and moves nodes around the list as the nodes are used. The least frequently used node will be at the tail and
removing it is a fairly simple job.


![ordered_dict](/assets/2022-05-06-ordered_dict/ordered_dict.png)

Whenever a `get` or `put` is performed the node affected needs to be moved to the `head` of the list.
The affected node is accessed in `O(1)` time from the dictionary and then the affected node is moved
to the `head` which we also maintain an `O(1)` reference to.

![reorder](/assets/2022-05-06-ordered_dict/reorder.png)

Finally, if the cache capacity will be exceeded by adding a new node, the tail of the LRU list must
be popped and the new node inserted at the head. Everything is done in `O(1)` because we have saved
references to all nodes, plus deletions and insertions are `O(1)` for doubly linked lists.

![pop](/assets/2022-05-06-ordered_dict/pop.png)

Instead of going through the exercise of adjusting node pointers in doubly linked lists correctly, Python
offers the option to use an `OrderedDict` which satisfies the constraints of the LRU problem. Let's 
investigate the source code to verify.


## `OrderedDict`

The source code for the Python most people know lives at https://github.com/python/cpython.

The source code for `OrderedDict` lives under
[`/Lib/collections/__init__.py`](https://github.com/python/cpython/blob/main/Lib/collections/__init__.py). The maintainers start `OrderedDict` with a comment that fully describes the implementation.

```python
'Dictionary that remembers insertion order'
# An inherited dict maps keys to values.
# The inherited dict provides __getitem__, __len__, __contains__, and get.
# The remaining methods are order-aware.
# Big-O running times for all methods are the same as regular dictionaries.

# The internal self.__map dict maps keys to links in a doubly linked list.
# The circular doubly linked list starts and ends with a sentinel element.
# The sentinel element never gets deleted (this simplifies the algorithm).
# The sentinel is in self.__hardroot with a weakref proxy in self.__root.
# The prev links are weakref proxies (to prevent circular references).
# Individual links are kept alive by the hard reference in self.__map.
# Those hard references disappear when a key is deleted from an OrderedDict.
```

Let's start digesting the `__init__` method to verify some of these claims.

```python
class OrderedDict(dict):
    def __init__(self, other=(), /, **kwds):
        '''Initialize an ordered dictionary.  The signature is the same as
        regular dictionaries.  Keyword argument order is preserved.
        '''
        try:
            self.__root
        except AttributeError:
            self.__hardroot = _Link()
            self.__root = root = _proxy(self.__hardroot)
            root.prev = root.next = root
            self.__map = {}
        self.__update(other, **kwds)
```

Looking at this code I already have a few questions that I believe requires looking into the parent class `dict`. I'm not familiar enough with
the Python repository to uncover this, but I think the `dict` type is implemented in C inside the `cpython repository.

I can't answer what `self.__root` does and why is it wrapped in a try block, but I assume this is a check on the parent object's constructor.
If the `self.__root` attribute does not exist the child constructor instantiates the necessary objects. According to the class comments `self.__root` and `self.__hardroot` both represent the special, sentinel, root element of the linked list.

The comment also mentions that `self.__root` is a _`weakref`_ to `self.__hardroot`? What does that mean? Checking out the `weakref` module
documentation shows that pointing to an object with `weakrefs` allows the garbage collector to destroy an object _if and only if_ there are
no _strong_ references (references that aren't `weakrefs`). The reason to use `weakrefs` here is so that when a node is _removed_ from the linked
list we only need to modify the pointers of its neighbors and _not_ the node being removed.

![weakref](/assets/2022-05-06-ordered_dict/weakref.png)

As much is described in the comment:

```python
# Individual links are kept alive by the hard reference in self.__map.
# Those hard references disappear when a key is deleted from an OrderedDict.
```

I'm going to take a break before covering the rest of the interesting operations because this has already been
quite interesting, and it is _too_ late in the evening to be twiddling linked list pointers.