---
title: Slicing Rubies
date: 2015-05-13
tags: core, enumerable
commit: ecd7d03bab149ac62fb5028b83fde82ebd13dc2c
---

Ruby puts a lot of effort into its [Enumerable](https://ruby-doc.org/core/Enumerable) module, offering a lot of different ways of iterating through collections. It is one of the reasons for Ruby's success, but you can also call it idiosyncratic, sometimes. This episode portraits enumerables' three handy `slice_*` methods.

ARTICLE

## Slicing Enumerables

Slicing lets you divide a collection into sub-collections. Take this array:

    a = %w[Ruby in the back-end, React in the front-end]

Let's say you want to divide it into smaller arrays. The simplest option would be passing a regex to `slice_before` describing where to slice:

    a.slice_before(/,/).to_a
    # => [["Ruby", "in", "the"], ["back-end,", "React", "in", "the", "front-end"]]

A variation of this is method is `slice_after`, which puts the separating element into the earlier sub-collection, which makes more sense in this case:

    a.slice_after(/,/).to_a
    # => [["Ruby", "in", "the", "back-end,"], ["React", "in", "the", "front-end"]]

Slicing uses [===](https://ruby-doc.org/core/Object.html#method-i-3D-3D-3D) to compare each element with the given separator, so you can pass in a class as well:

    ["A", "B", 1, "C", "D", 2].slice_after(Integer).to_a
    # => [["A", "B", 1], ["C", "D", 2]]

For more complex, and more real-world problems, you will need to pass in a block, like for separating ascending numerical sequences:

    previous = Float::NAN
    [0, 2, 3, 4, 6, 7, 9].slice_before{ |current|
      previous, slice = current, current != previous + 1
      slice # is false if previous + 1 is not current -> start new slice
    }.to_a
    # => [0], [2, 3, 4], [6, 7], [9]

Keeping around additional variables is a little annoying, but fortunately, we can rewrite the previous example using the newer `slice_when` method:

    [0, 2, 3, 4, 6, 7, 9].slice_when{ |previous, current|
      current != previous + 1
    }.to_a
    # => [0], [2, 3, 4], [6, 7], [9]


## Resources

- [RDoc: Enumerable#slice_*](https://ruby-doc.org/core/Enumerable.html#method-i-slice_after)
- [#slice_when discussion](https://bugs.ruby-lang.org/issues/9826)
