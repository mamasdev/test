```javascript
Here’s the code for creating two buttons in Angular to handle the import and export functionality for JSON data. We will use Angular's HTTP client to interact with your backend API.

1. Create a Component with Import and Export Buttons

In this example, we will create two buttons: one for importing a JSON file and the other for exporting the configuration as a JSON file.

1.1 HTML Template for the Component

<!-- import-export.component.html -->
<div class="import-export-container">
  <!-- Export Button -->
  <button (click)="exportConfig()" class="btn btn-primary">Export Configuration</button>

  <!-- Import Button -->
  <input type="file" (change)="onFileChange($event)" />
  <button (click)="importConfig()" class="btn btn-success" [disabled]="!selectedFile">Import Configuration</button>
</div>

1.2 TypeScript Component Logic

// import-export.component.ts
import { Component } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';

@Component({
  selector: 'app-import-export',
  templateUrl: './import-export.component.html',
  styleUrls: ['./import-export.component.css']
})
export class ImportExportComponent {
  selectedFile: File | null = null;

  constructor(private http: HttpClient) {}

  // Export Configuration as JSON
  exportConfig(): void {
    // Send GET request to your backend to fetch the configuration and trigger file download
    this.http.get<any>('/api/export-config', { responseType: 'blob' as 'json' })
      .subscribe((response: any) => {
        // Create a Blob from the response data
        const blob = new Blob([response], { type: 'application/json' });
        const url = window.URL.createObjectURL(blob);
        const a = document.createElement('a');
        a.href = url;
        a.download = 'exported_config.json'; // Set the filename for the download
        a.click();
        window.URL.revokeObjectURL(url);
      }, error => {
        console.error('Error exporting configuration:', error);
      });
  }

  // Handle the selected file for import
  onFileChange(event: any): void {
    this.selectedFile = event.target.files[0];
  }

  // Import Configuration from selected JSON file
  importConfig(): void {
    if (!this.selectedFile) {
      return;
    }

    // Create a form data object to send the file and additional data to the backend
    const formData = new FormData();
    formData.append('jsonConfig', this.selectedFile);
    formData.append('destinationAit', 'yourDestinationAit');  // Set the destination AIT value

    // Send POST request to upload the configuration file
    this.http.post('/api/import-config', formData).subscribe(
      (response: any) => {
        console.log('Configuration imported successfully:', response);
      },
      (error: any) => {
        console.error('Error importing configuration:', error);
      }
    );
  }
}

1.3 Add HTTP Client Module in Your Angular Module

In your app.module.ts file, import the HttpClientModule to make HTTP requests:

import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { HttpClientModule } from '@angular/common/http'; // Import the HttpClientModule

import { AppComponent } from './app.component';
import { ImportExportComponent } from './import-export/import-export.component';

@NgModule({
  declarations: [
    AppComponent,
    ImportExportComponent
  ],
  imports: [
    BrowserModule,
    HttpClientModule  // Add HttpClientModule to imports
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }

1.4 Example CSS for the Buttons (Optional)

/* import-export.component.css */
.import-export-container {
  margin: 20px;
  padding: 10px;
}

button {
  margin: 10px;
}

input[type="file"] {
  margin-right: 10px;
}

Backend API Endpoint Assumptions:

Export Configuration: This sends a GET request to /api/export-config that will return the configuration in JSON format, and the frontend will trigger a file download.

Import Configuration: This sends a POST request to /api/import-config with the uploaded JSON file as part of a multipart/form-data request.


Explanation:

Export Button:

The exportConfig() method sends a GET request to your backend to fetch the configuration data.

The response is expected to be a blob (binary data, in this case, JSON), which is then used to trigger a file download on the browser.


Import Button:

The onFileChange() method allows the user to select a file.

The importConfig() method sends the selected file to the backend as part of a FormData object, which is sent via POST.



Final Notes:

Replace yourDestinationAit with the actual AIT value or logic you need for your scenario.

Ensure the backend endpoints /api/export-config and /api/import-config are properly implemented to handle these requests.


