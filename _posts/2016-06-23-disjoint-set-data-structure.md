--- 
layout: post 
title: 用于不相交集合的数据结构 
category: Tech 
tags: data-structure algorithm set 
---

不相交集合数据结构（disjoing-set data structure)维护了一个不相交动态集的集合S={S1, S2, …, Sk}。我们用这个集合的某个成员来标识每个集合，该成员称为代表（representative）。  

1. MAKE_SET(x)： 建立一个新的集合，其唯一成员就是x，所以其代表也就是自己。因为各集合是不相交的，故要求x没有在其他集合中出现过。
2. UNION(x，y)： 将包含x和y的动态集合（比如说Sx和Sy）合并为一个新的集合（即这两个集合的并集）。假定这个操作之前是不相交的。在经过此操作后，所得集合的代表可以是Sx U Sy中的任何一个成员，但在UNION的很多实现细节中，都选择Sx或Sy的代表作为新的代表。由于要求各集合是不相交的，故我们“消除”集合Sx和Sy，把它们从S中删去。
3. FIND_SET(x)：返回一个指针，指向包含x的（唯一）集合的代表。













---

'''
docstring
'''
import pylab

def bfs_visited(ugraph, start_node):
    '''
    docstring
    '''
    quene = [start_node]
    visited = set([start_node])
    while quene != []:
        live = quene.pop(0)
        for neighbor in ugraph[live]:
            if neighbor not in visited:
                visited.add(neighbor)
                quene.append(neighbor)
    return visited

def cc_visited(ugraph):
    '''
    docstring
    '''
    remain = set(ugraph.keys())
    result = []
    while remain != set([]):
        visited = bfs_visited(ugraph,remain.pop())
        result.append(visited)
        remain = remain.difference(visited)
    return result 
def largest_cc_size(ugraph):
    '''
    docstring
    '''
    visited = cc_visited(ugraph)
    largest = 0
    for group in visited:
        if len(group) > largest:
            largest = len(group)
    return largest#max(map(len, cc_visited(ugraph)))

def compute_resilience(ugraph, attack_order):
    '''
    docstring
    '''
    current_ugraph = ugraph
    res = [largest_cc_size(current_ugraph)]
    for node in attack_order:
        for endpoint in current_ugraph[node]:
            current_ugraph[endpoint].remove(node)
        del current_ugraph[node]
        res.append(largest_cc_size(current_ugraph))
    return res


#print bfs_visited({1:[2], 2:[1], 3:[]}, 1)
#print cc_visited({1:[2], 2:[1], 3:[]})
#print largest_cc_size({1:[2], 2:[1], 3:[]})
print compute_resilience({1:[2], 2:[1], 3:[]}, [3,1])


## PS
算法导论中的花体S让我困惑了好几十分钟，结果原因如下：  

花体字母一般表示

1. 集合的集合；
2. 当问题中元素普遍采用大写字母时，用花体字母表示元素组成的集合。