# csci585-hw4-graph-traversal

### Q1 - Creating a graph

#### Creating directly from the command line
```
graph = TinkerGraph.open()
g = graph.traversal()

g.addV("course").property(id, 101).property("name", "cs101").as("cs101").
  addV("course").property(id, 201).property("name", "cs201").as("cs201").
  addV("course").property(id, 220).property("name", "cs220").as("cs220").
  addV("course").property(id, 420).property("name", "cs420").as("cs420").
  addV("course").property(id, 334).property("name", "cs334").as("cs334").
  addV("course").property(id, 681).property("name", "cs681").as("cs681").
  addV("course").property(id, 400).property("name", "cs400").as("cs400").
  addV("course").property(id, 526).property("name", "cs526").as("cs526").
  addE("requires pre-req").from("cs201").to("cs101").
  addE("requires pre-req").from("cs220").to("cs201").
  addE("requires pre-req").from("cs420").to("cs220").
  addE("is a co-req of").from("cs420").to("cs220").
  addE("requires pre-req").from("cs334").to("cs201").
  addE("requires pre-req").from("cs681").to("cs334").
  addE("requires pre-req").from("cs400").to("cs334").
  addE("requires pre-req").from("cs526").to("cs400").
  addE("is a co-req of").from("cs526").to("cs400")

```


### Q2 - Find doubly-connected nodes

```
g.V().filter(outE().count(0.is(2)).as('a').out().as('b').select('a', 'b')
```
