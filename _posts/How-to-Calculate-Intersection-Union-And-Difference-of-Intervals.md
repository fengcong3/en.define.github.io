---
title: How to Calculate Intersection, Union, Difference of Intervals
published: true
---

## [](#header-1)Intersection
```python
def intervalIntersection(*intervals):
    """
    interval1: (a, b)
    interval2: (c, d)

    a--------b
           c-------------d
    return (c,b)

    a---------b
              c------d

    return (c,b)

    a------b
               c--------d
    
    return None
    """
    ###########
    interval_list = list(intervals)
    interval_list.sort(key=lambda x: x[0])
    ## judge if the interval is valid
    for interval in interval_list:
        if interval[1] < interval[0]:
            sys.stdout.write("Interval Error\n")
            return None
    ## get the intersection
    intersection = ()
    for i in range(len(interval_list)):
        if i == 0:
            intersection = interval_list[i]
        else:
            if interval_list[i][0] <= intersection[1]:
                intersection = (interval_list[i][0], min(interval_list[i][1], intersection[1]))
            else:
                intersection = None
                break
    return intersection

```
You can call this function with multiple intervals. like this: 
```python
intervalIntersection((1, 3), (2, 6), (8, 10), (15, 18))
# None
intervalIntersection((1, 3), (2, 6), (-1, 99), (-5, 5))
# (2,3)
```

## [](#header-2)Union
```python
def intervalUnion(*intervals):
    """
    interval1: (a, b)
    interval2: (c, d)

    a--------b
           c-------------d
    return [(a,d)]

    a---------b
              c------d

    return [(a,d)]

    a------b
               c--------d
    
    return [(a,b),(c,d)]
    """
    ###########
    interval_list = list(intervals)
    interval_list.sort(key=lambda x: x[0])
    ## judge if the interval is valid
    for interval in interval_list:
        if interval[1] < interval[0]:
            sys.stdout.write("Interval Error\n")
            return None
    ## get the union
    union = []
    for i in range(len(interval_list)):
        if i == 0:
            union.append(interval_list[i])
        else:
            if interval_list[i][0] <= union[-1][1]:
                union[-1] = (union[-1][0], max(union[-1][1], interval_list[i][1]))
            else:
                union.append(interval_list[i])
    return union

```

Also, you can call this function with multiple intervals. like this: 
```python
intervalUnion((1, 3), (2, 6), (8, 10), (15, 18))
# [(1, 6), (8, 10), (15, 18)]
```

## [](#header-3)Difference

```python
def intervalDifference(interval1, interval2):
    """
    interval1: (a, b)
    interval2: (c, d)

    a--------b
           c-------------d
    return [(a,c-1)]

    a---------b
              c------d

    return [(a,b-1)]

    a------b
               c--------d
    
    return [(a,b)]

    a---------------b
      c--------d

    return [(a-c-1),(d+1,b)]
    """
    ###########
    interval_list = [interval1, interval2]
    interval_list.sort(key=lambda x: x[0])
    ## judge if the interval is valid
    for interval in interval_list:
        if interval[1] < interval[0]:
            sys.stdout.write("Interval Error\n")
            return None
    ## get the interval1 difference with interval2
    difference = []
    interval1_index = interval_list.index(interval1)
    if interval1_index == 0:
        if interval2[0] <= interval1[1]:
            if interval1[0] == interval2[0]:
                pass
            else:
                difference.append( (interval1[0], interval2[0]-1) )
            if interval2[1] >= interval1[1]:
                pass
            else:
                difference.append( (interval2[1]+1, interval1[1]) )
        else:
            difference.append( (interval1[0], interval1[1]) )
    else:
        if interval1[0] <= interval2[1]:
            if interval1[1] <= interval2[1]:
                pass
            else:
                difference.append( (interval2[1]+1, interval1[1]) )
        else:
            difference.append( (interval1[0], interval1[1]) )    
    return difference
    
```
If you want to calculate `interval1 - interval2`, you can call this function with two intervals. like this: 
```python
intervalDifference((1, 3), (2, 6))
# [(1,2)]
```

## [](#header-4)Difference between two interval sets

If you have two interval sets , `A` and `B`, which A is like `[(1, 3), (2, 9), (8, 10), (9, 18)]` and B is like `[(2, 6), (15, 18)]`, and mybe they have overlap within the sets. 
Now, you wana calculate the difference between them(like `A-B`).

* step1: calculate the union of `A` and `B` respectively.
* step2: calculate the difference between each elements of `A` and each elements of `B`
* step3: calculate the union of the result of step2

```python
def intervalListDifference(interval_list1, interval_list2):
    ## judge if the interval is valid
    for interval in interval_list1:
        if interval[1] < interval[0]:
            sys.stdout.write("Interval Error\n")
            return None
    for interval in interval_list2:
        if interval[1] < interval[0]:
            sys.stdout.write("Interval Error\n")
            return None
    ## get the union of interval_list1 and interval_list2
    union_interval_list1 = intervalUnion(*interval_list1)
    union_interval_list2 = intervalUnion(*interval_list2)
    ## get the difference between each elements of interval_list1 and each elements of interval_list2
    difference_interval_list1_interval_list2 = []
    for interval1 in interval_list1:
        tmp_list = []
        for interval2 in interval_list2:
            tmp_list.extend(intervalDifference(interval1, interval2))
        difference_interval_list1_interval_list2.append(intervalIntersection(*tmp_list))
    ## get the union of the result of step2
    union_difference_interval_list1_interval_list2 = intervalUnion(*difference_interval_list1_interval_list2)
    return union_difference_interval_list1_interval_list2

```
Then, you can call this function with two interval lists. like this: 
```python
intervalListDifference([(1, 3), (2, 9), (8, 10), (9, 18)], [(2, 6), (15, 18)])
# [(1, 1), (7, 14)]
```

