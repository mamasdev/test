```javascript
function resolveGraph(graph) {
  // Create a map of @id → object
  const map = {};
  for (const node of graph) {
    map[node['@id']] = { ...node };
  }

  // Recursive function to resolve @id references
  function deref(obj) {
    if (Array.isArray(obj)) {
      return obj.map(deref);
    } else if (obj && typeof obj === 'object') {
      // If object is a reference like { "@id": "#12" }
      if (obj['@id'] && Object.keys(obj).length === 1 && map[obj['@id']]) {
        return deref(map[obj['@id']]);
      }

      // Recursively dereference all nested properties
      const result = {};
      for (const [key, value] of Object.entries(obj)) {
        if (key !== '@id' && key !== '@type') {
          result[key] = deref(value);
        }
      }
      return result;
    }
    return obj;
  }

  // Find the root (API or main model)
  const root = graph.find(n => n['@type']?.includes('#Api')) || graph[0];
  return deref(root);
}

// Example usage
const response = require('./anypointResponse.json');
const flattened = resolveGraph(response['@graph']);
console.log(JSON.stringify(flattened, null, 2));
