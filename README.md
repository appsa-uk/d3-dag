# d3-dag
Directed Acyclic Graph rendering with D3

Examples:
1. [Graph from graphViz DOT syntax description](https://appsa-uk.github.io/d3-dag/dag-graphViz-d3.html). Graphlib-dot library is used to convert GraphViz DOT string to graph object.
2. [Graph from JSON](https://appsa-uk.github.io/d3-dag/dag-json-d3.html). ```parseCustmoJson``` function is used to convert json object to graph object using graphlib library.

```javascript


         function parseCustomJson(jsonString) {
             try {
                 const data = JSON.parse(jsonString);
                 const g = new graphlib.Graph({ directed: true }); // Create a new graphlib graph

                 // Set graph attributes (if any)
                 if (data.graphAttributes) {
                     g.setGraph(data.graphAttributes);
                 }

                 // Add nodes
                 data.nodes.forEach(node => {
                     g.setNode(node.id, { label: node.label, ...node.attributes }); // Node attributes can be custom
                 });

                 // Add edges
                 data.edges.forEach(edge => {
                     g.setEdge(edge.source, edge.target, { label: edge.label, ...edge.attributes }); // Edge attributes can be custom
                 });

                 return g;
             } catch (error) {
                 console.error("Error parsing custom JSON:", error);
                 throw new Error("Invalid JSON input.");
             }
         }

```


3. Graph from CSV

Bugs:
- arrowheads are not renedered on Safari (most likely because of <foreignObject>)
