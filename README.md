# test
test


```javascript
const fs = require('fs');
const path = require('path');

function deleteFolderRecursive(folderPath) {
    if (fs.existsSync(folderPath)) {
        fs.readdirSync(folderPath).forEach((file) => {
            const currentPath = path.join(folderPath, file);

            if (fs.lstatSync(currentPath).isDirectory()) {
                deleteFolderRecursive(currentPath);
            } else {
                fs.unlinkSync(currentPath); // Delete file
            }
        });
        fs.rmdirSync(folderPath); // Remove the empty folder
        console.log(`Deleted: ${folderPath}`);
    }
}

function findAndDeleteFolders(rootDir, foldersToDelete) {
    fs.readdirSync(rootDir, { withFileTypes: true }).forEach((dirent) => {
