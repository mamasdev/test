# test

```javascript
const fs = require('fs');
const path = require('path');

function deleteFolder(folderPath) {
    if (fs.existsSync(folderPath)) {
        fs.rmSync(folderPath, { recursive: true, force: true });
        console.log(`Deleted: ${folderPath}`);
    }
}

// Specify the root directory to search and delete folders
const rootDirectory = path.resolve(__dirname, 'path_to_your_directory'); // Change this to your directory
const foldersToDelete = ['node_modules', 'jspm_packages'];

function findAndDeleteFolders(rootDir, foldersToDelete) {
    fs.readdirSync(rootDir, { withFileTypes: true }).forEach((dirent) => {
        const fullPath = path.join(rootDir, dirent.name);

        if (dirent.isDirectory()) {
            if (foldersToDelete.includes(dirent.name)) {
                deleteFolder(fullPath);
            } else {
                findAndDeleteFolders(fullPath, foldersToDelete);
            }
        }
    });
}

findAndDeleteFolders(rootDirectory, foldersToDelete);
console.log('Deletion process completed.');
