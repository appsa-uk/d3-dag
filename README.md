# d3-dag
Directed Acyclic Graph rendering with D3

Examples:
1. [Graph from graphViz DOT syntax description](https://appsa-uk.github.io/d3-dag/dag-graphViz-d3.html). Graphlib-dot library is used to convert GraphViz DOT string to graph object.
2. [Graph from JSON](https://appsa-uk.github.io/d3-dag/dag-json-d3.html). ```parseCustmoJson``` function is used to convert json object to graph object using graphlib library.

```javascript
         function parseCustomJson(jsonString) {
            const data = JSON.parse(jsonString);
            const g = new graphlib.Graph({ directed: true });

            // Set graph attributes (if any)
            if (data.graphAttributes) {
            g.setGraph(data.graphAttributes);
            }

            // Add nodes
            data.nodes.forEach(node => {
            g.setNode(node.id, { label: node.label, ...node.attributes });
            });

            // Add edges
            data.edges.forEach(edge => {
            g.setEdge(edge.source, edge.target, { label: edge.label, ...edge.attributes }); // Edge attributes can be custom
            });

            return g;
         }

```


3. [Graph from array of node objects](https://appsa-uk.github.io/d3-dag/dag-adjacency-lists-d3.html) (adjacency list). ```parseAdjacencyList``` function is used to convert adjacency lists array object to graph object using graphlib library.

```javascript
      function parseAdjacencyList(jsonAdjacencyList) {
         const rawData = JSON.parse(jsonAdjacencyList);
         const g = new graphlib.Graph({ directed: true });
      
         g.setGraph({})
      
         // Add Nodes
         rawData.forEach(nodeObj => {
            if (!nodeObj.id) {
               console.warn("Node object missing 'id' property:", nodeObj);
               return;
            }
      
            const nodeId = nodeObj.id.toString();
      
            // Prepare node properties for graphlib
            // Exclude 'linksTo' and 'id' from direct node properties
            const nodeProps = { ...nodeObj };
            delete nodeProps.id;
            delete nodeProps.linksTo;
      
            // Map specific properties for Dagre-D3 rendering
            // You can add more mappings here as needed
            if (nodeProps.label === undefined) {
               nodeProps.label = nodeId;
            }
            if (nodeProps.color) {
               nodeProps.style = `fill: ${nodeProps.color};`;
               delete nodeProps.color;
            }
            // For example you can add custom settings
            if (nodeProps.shape) {
                nodeProps.shape = nodeProps.shape;
            }
      
            g.setNode(nodeId, nodeProps);
         });
         
         // --- Second Pass: Add Edges ---
         // Now iterate again to add edges, assuming all nodes are already in the graph.
         rawData.forEach(nodeObj => {
         const sourceId = nodeObj.id.toString();
         
            if (Array.isArray(nodeObj.linksTo)) {
               nodeObj.linksTo.forEach(link => {
                if (!link.target) {
                    console.warn(`Link from ${sourceId} missing 'target' property:`, link);
                    return;
                }
         
                const targetId = link.target.toString();
         
                if (!g.hasNode(targetId)) {
                    console.warn(`Target node '${targetId}' for edge from '${sourceId}' does not exist. Skipping edge.`, link);
                    return;
                }
         
                // Prepare edge properties for graphlib
                const edgeProps = { ...link }; 
                delete edgeProps.target; // 'target' is used to define the edge, not its property
         
                // Map specific properties for Dagre-D3 rendering
                if (edgeProps.label === undefined) {
                    edgeProps.label = '';
                }
                // If you want a custom style based on a 'color' in the link:
                // if (edgeProps.color) {
                //     edgeProps.style = `stroke: ${edgeProps.color};`;
                //     delete edgeProps.color;
                // }
         
                g.setEdge(sourceId, targetId, edgeProps);
            });
          }
        });
        return g;
      }

```



Bugs:
- arrowheads are not renedered on Safari (most likely because of ```<foreignObject>```)
