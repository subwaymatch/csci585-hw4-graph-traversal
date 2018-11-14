# Gremlin Graph Traversal

### Q1 - Creating a graph

#### Query
```
g = TinkerGraph.open().traversal()

g.addV('course').property(id, 'CS101').property('name', 'CS101').as('CS101').
  addV('course').property(id, 'CS201').property('name', 'CS201').as('CS201').
  addV('course').property(id, 'CS220').property('name', 'CS220').as('CS220').
  addV('course').property(id, 'CS420').property('name', 'CS420').as('CS420').
  addV('course').property(id, 'CS334').property('name', 'CS334').as('CS334').
  addV('course').property(id, 'CS681').property('name', 'CS681').as('CS681').
  addV('course').property(id, 'CS400').property('name', 'CS400').as('CS400').
  addV('course').property(id, 'CS526').property('name', 'CS526').as('CS526').
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



### Q2 - Find doubly-connected nodes

#### Query
```
g.V().as('a').out().as('b').groupCount().by(select('a', 'b')).unfold().filter(select(values).is(eq(2))).select(keys)
```

#### Explanation
- 



### Q3 - Output all ancestors

#### Query
```
g.V('CS526').repeat(out('requires pre-req')).emit()
```

#### Explanation
- `g.V()` is a one of the two traversal methods provided by `GraphTraversalSource` object. It generates a traversal method starting at the vertices of the graph (if no id specified - from all vertices, if an id is specified - from the specified vertex). 
- Our query begins traversal at the *CS526* vertex. 
- `repeat()` is used to loop over a traversal. It can be used with a break predicate (an exit condition). 
- Since no break predicate is placed on the query, it will traverse until the highest ancestor. 
- Notice the `out('requires pre-req')` expression inside `repeat()`. This makes the traversal to object only traverse through edges that have **'requires pre-req'** labels. This is to avoid double traversals between vertices that are connected by **'is a co-req of'** edges. 
- `emit()` is a *step modulator* of `repeat()` where it emits the vertex object during traversal. In our case, the emitted vertices are logged to the console. 



### Q4 - Find maximum depth

#### Query
```
g.V('CS526').repeat(__.out()).emit().path().count(local).max()
```

TODO: find the difference between __.out() and out()

#### Explanation
- 
