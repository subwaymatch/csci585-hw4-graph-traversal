# Gremlin Graph Traversal

### Q1 - Creating a graph

#### Query
```
g = TinkerGraph.open().traversal()

g.addV("course").property(id, "CS101").property("name", "CS101").as("CS101").
  addV("course").property(id, "CS201").property("name", "CS201").as("CS201").
  addV("course").property(id, "CS220").property("name", "CS220").as("CS220").
  addV("course").property(id, "CS420").property("name", "CS420").as("CS420").
  addV("course").property(id, "CS334").property("name", "CS334").as("CS334").
  addV("course").property(id, "CS681").property("name", "CS681").as("CS681").
  addV("course").property(id, "CS400").property("name", "CS400").as("CS400").
  addV("course").property(id, "CS526").property("name", "CS526").as("CS526").
  addE("requires pre-req").from("CS201").to("CS101").
  addE("requires pre-req").from("CS220").to("CS201").
  addE("requires pre-req").from("CS420").to("CS220").
  addE("is a co-req of").from("CS420").to("CS220").
  addE("requires pre-req").from("CS334").to("CS201").
  addE("requires pre-req").from("CS681").to("CS334").
  addE("requires pre-req").from("CS400").to("CS334").
  addE("requires pre-req").from("CS526").to("CS400").
  addE("is a co-req of").from("CS526").to("CS400")
```

#### Explanation
- `TinkerGraph.open()` creates an empty instance of a in-memory TinkerGraph with 0 vertex and 0 edge.
- The query chains the `open()` call (which returns the empty graph instance) with `traversal()` to get the `GraphTraversalSource` instance of the graph. 
- Since we have the traversal object, we can start adding vertices and edges. 
- Adding a vertex takes the form of `addV('vertex type').property(id, 'course id').property('name', 'course name').as('course name')`.
- `id` property is a special property of TinkerGraph. In other graph databases, it won't support directly setting the value. However, this example sets the `id`s to course IDs for easier reference. 
- The `name` property is not required in this assignment. However, it can be useful if a course is cross-listed between departments. 


### Q2 - Find doubly-connected nodes

```
g.V().as('a').out().as('b').groupCount().by(select('a', 'b')).unfold().filter(select(values).is(eq(2))).select(keys)
```

### Q3 - Output all ancestors

```
g.V('CS526').repeat(out('requires pre-req')).emit()
```

### Q4 - Find maximum depth

```
g.V('CS526').repeat(__.out()).emit().path().count(local).max()
```

TODO: find the difference between __.out() and out()
