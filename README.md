```javascript
# Define paths
$source = "C:\Path\To\Your\LocalFolder"          # Replace with your folder
$destination = "\\YourNAS\Share\TargetFolder"    # Or "Z:\TargetFolder" if using mapped drive

# Ensure destination folder exists
if (-not (Test-Path -Path $destination)) {
    New-Item -ItemType Directory -Path $destination -Force | Out-Null
}

# Copy files recursively
Copy-Item -Path $source\* -Destination $destination -Recurse -Force

Write-Host "Copy completed successfully!"
