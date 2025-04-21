```javascript
const fs = require('fs');
const path = require('path');

// CONFIGURE THESE
const sourceDir = 'C:/Path/To/Your/LocalFolder'; // Local folder to copy from
const destDir = 'Z:/TargetFolder';               // NAS share (use mapped drive or UNC path like '\\NAS\Share')

function copyFolderRecursiveSync(source, target) {
  if (!fs.existsSync(target)) {
    fs.mkdirSync(target, { recursive: true });
  }

  const items = fs.readdirSync(source, { withFileTypes: true });

  for (const item of items) {
    const srcPath = path.join(source, item.name);
    const destPath = path.join(target, item.name);

    if (item.isDirectory()) {
      copyFolderRecursiveSync(srcPath, destPath);
    } else {
      fs.copyFileSync(srcPath, destPath);
    }
  }
}

// MAIN
try {
  console.time('Copy Time');
  copyFolderRecursiveSync(sourceDir, destDir);
  console.timeEnd('Copy Time');
  console.log('Copy complete.');
} catch (err) {
  console.error('Error copying folder:', err.message);
}
