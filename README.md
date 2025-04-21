```javascript
const fs = require('fs');
const path = require('path');

const sourceDir = 'C:/Path/To/Your/LocalFolder'; // Change this
const destDir = 'Z:/TargetFolder';               // Change this

fs.cp(sourceDir, destDir, { recursive: true }, (err) => {
  if (err) {
    console.error('Error copying folder:', err.message);
  } else {
    console.log('Folder copied successfully!');
  }
});
