# csci585-hw4-graph-traversal

### Q1 - Creating a graph

#### Creating directly from the command line
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


### Q2 - Find doubly-connected nodes

```
g.V().filter(outE().count().is(2)).as('a').out().as('b').select('a', 'b').unique()
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
