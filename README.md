# Gremlin Graph Traversal

### Q1 - Creating a graph

#### Query
```
g = TinkerGraph.open().traversal()

g.addV('course').property(id, 'CS101').as('CS101').
  addV('course').property(id, 'CS201').as('CS201').
  addV('course').property(id, 'CS220').as('CS220').
  addV('course').property(id, 'CS420').as('CS420').
  addV('course').property(id, 'CS334').as('CS334').
  addV('course').property(id, 'CS681').as('CS681').
  addV('course').property(id, 'CS400').as('CS400').
  addV('course').property(id, 'CS526').as('CS526').
  addE('requires pre-req').from('CS201').to('CS101').
  addE('requires pre-req').from('CS220').to('CS201').
  addE('requires pre-req').from('CS420').to('CS220').
  addE('is a co-req of').from('CS420').to('CS220').
  addE('requires pre-req').from('CS334').to('CS201').
  addE('requires pre-req').from('CS681').to('CS334').
  addE('requires pre-req').from('CS400').to('CS334').
  addE('requires pre-req').from('CS526').to('CS400').
  addE('is a co-req of').from('CS526').to('CS400')
```

#### Check Result
```
gremlin> g
==>graphtraversalsource[tinkergraph[vertices:8 edges:9], standard]
```

#### Explanation
- `TinkerGraph.open()` creates an empty instance of a in-memory TinkerGraph with 0 vertex and 0 edge.
- The query chains the `open()` call (which returns the empty graph instance) with `traversal()` to get the `GraphTraversalSource` instance of the graph. 
- Since we have the traversal object, we can start adding vertices and edges. 
- Adding a vertex takes the form of `addV('vertex label').property(id, 'course id').property('name', 'course id').as('course id')`.
- `id` is a special property of TinkerGraph. In TinkerGraph, directly setting the `id` values are allowed. Therefore, we set the `id` values to course IDs.
- For each vertex, `as()` is used to create a reference variable to the vertex. This reference is used to create edges in a chained manner. 
- Two types of edges are added between vertices - **'requires pre-req'** and **'is a co-req of'**. These types are differentiated by edge labels.  
- Adding an edge takes the form of `addE('edge label').from('vertex reference').to('vertex reference')`.
- All `addV()` and `addE()` calls are chained together. This is possible since each call returns the graph traversal object `g`. 



### Q2 - Find doubly-connected vertices

#### Query
```
g.V().as('a').out().as('b').groupCount().by(select('a', 'b')).unfold().filter(select(values).is(eq(2))).select(keys)
```

#### Output
```
==>[a:v[CS526],b:v[CS400]]
==>[a:v[CS420],b:v[CS220]]
```

#### Explanation
- 



### Q3 - Output all ancestors of a given vertex

#### Query
```
g.V('CS526').repeat(__.out('requires pre-req')).emit()
```

#### Output
```
==>v[CS400]
==>v[CS334]
==>v[CS201]
==>v[CS101]
```

#### Explanation
- `g.V()` is a one of the two traversal methods provided by `GraphTraversalSource` object. It generates a traversal method starting at the vertices of the graph (if no `id` specified - from all vertices, if an `id` is specified - from the specified vertex). 
- Our query begins traversal at the *CS526* vertex since we specified the `id` when calling `g.V('CS526')`. 
- `repeat()` is used to loop over a traversal. It can be used with a break predicate (an exit condition). In the query above, there is no exit condition. This will loop until no more traversal path is found. 
- The `__.out()` call represents an anonymous traversal towards outgoing vertices. It is used when passing a traversal as an argument to a parent step. Note that `__.` is not required in the gremlin console for `__.out()`. However, it is added here for clarity.  
- Notice the `out('requires pre-req')` expression inside `repeat()`. This makes the traversal to object only traverse through edges that have **'requires pre-req'** label. This is to avoid double traversals between vertices that are connected by **'is a co-req of'** edges.
- Since no break predicate is placed on the query, it will traverse until the highest ancestor. 
- `emit()` is a *step modulator* of `repeat()` where it emits the vertex object during traversal. In our case, the emitted vertices are logged to the console. 



### Q4 - Find maximum depth starting from a given vertex

#### Query
```
g.withSack(1).
  V('CS101').
  repeat(__.in('requires pre-req').
    sack(sum).by(constant(1))
  ).
  emit().
  sack().
  max()
```

#### Output
```
==>5
```

#### Explanation
- A **sack** is a data structure relative to each traverser. 
- Since a **sack** is relative to each traverser, each traversal path will contain its own **sack**, enabling an easier data aggregation. 
- Without a **sack**, the traversal object would have to emit all paths, and perform reduction. An example of this type of query is `g.V('CS101').repeat(__.in('requires pre-req')).emit().path().count(local).max()`. However, using a **sack** is more efficient. 
- `withSack()` defines a sack containing an initial value of 1. 
- As explained in **Q3**, `g.V('vertex id')` generates a traversal method starting at the *vertex id*. In the query above, the traversal method starts traversing at **CS101** vertex. 
- `.repeat(__.in('requires pre-req'))` tells the traversal object to loop, traversing towards incoming vertices of **'requires pre-req'** edges. 
- The `repeat()` call is chained with `sack(sum).by(constant(1))`, which adds 1 to each corresponding **sack** during traversal. 
- `emit()` emits the vertex on each traversal. As an example, `g.withSack(1).V('CS101').repeat(__.in('requires pre-req').sack(sum).by(constant(1))).emit()` will output the following.
```
==>v[CS201]
==>v[CS220]
==>v[CS334]
==>v[CS420]
==>v[CS681]
==>v[CS400]
==>v[CS526]
```
- Calling `sack()` on each emitted vertex will output the value of current **sack** at each point. `g.withSack(1).V('CS101').repeat(__.in('requires pre-req').sack(sum).by(constant(1))).emit().sack()` will output the following. 
```
==>2
==>3
==>3
==>4
==>4
==>4
==>5
```
- `max()` takes the maximum value out of the **sack** values. In the case above, the max value is 5. 
