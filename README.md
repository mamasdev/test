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
import jsonld from 'jsonld';
import fs from 'fs';

// Load your Anypoint-style JSON (with @context + @graph)
const doc = JSON.parse(fs.readFileSync('./anypoint-model.json', 'utf8'));

(async () => {
  // 1️⃣ Flatten merges all @id references into one graph map
  const flattened = await jsonld.flatten(doc);

  // 2️⃣ Optionally "frame" the document into a nested tree
  // Replace the @type below with your root type, usually "#Api" or a full URI
  const frame = {
    "@context": doc["@context"],
    "@type": "http://www.mulesoft.org/schema/model#Api"
  };

  const framed = await jsonld.frame(flattened, frame);

  console.log(JSON.stringify(framed, null, 2));
})();
