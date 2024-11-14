# test
test

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
        const fullPath = path.join(rootDir, dirent.name);

        if (dirent.isDirectory()) {
            // Check if the directory is one of the target folders
            if (foldersToDelete.includes(dirent.name)) {
                deleteFolderRecursive(fullPath);
            } else {
                // Recurse into subdirectories
                findAndDeleteFolders(fullPath, foldersToDelete);
            }
        }
    });
}

// Specify the root directory to search
const rootDirectory = path.resolve(__dirname, 'path_to_your_directory'); // Change this to your directory
const foldersToDelete = ['node_modules', 'jspm_packages'];

// Start the deletion process
findAndDeleteFolders(rootDirectory, foldersToDelete);
console.log('Deletion process completed.');
