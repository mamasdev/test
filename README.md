To adjust the current importConfig method to accept a JSON file instead of a string, you'll need to modify your endpoint to handle file uploads. The frontend would send the JSON file, and the backend would handle its processing. Below are the changes you should make:

1. Modify the Controller to Accept a File

Change the importConfig endpoint to accept a MultipartFile instead of a String. You can use Spring's @RequestParam to bind the uploaded file to a MultipartFile parameter.

Here's an example of how to change the endpoint:

import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.multipart.MultipartFile;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestMapping;

@RestController
@RequestMapping("/config")
public class GeneratedConfigController {

    @Autowired
    private GenerateConfigServiceImpl generateConfigService;

    // Modify the importConfig method to accept a file
    @PostMapping("/import")
    public String importConfig(@RequestParam("file") MultipartFile jsonFile) {
        try {
            return generateConfigService.importConfig(jsonFile);
        } catch (Exception e) {
            return "Error importing configuration: " + e.getMessage();
        }
    }
}

2. Modify the importConfig Method to Handle File Parsing

In the service method, change the parameter from String jsonConfig to MultipartFile jsonFile. Then, read the contents of the file into a String, and deserialize it using ObjectMapper.

Here’s how you can modify the importConfig method:

import org.springframework.web.multipart.MultipartFile;
import com.fasterxml.jackson.databind.ObjectMapper;

public String importConfig(MultipartFile jsonFile) {
    try {
        // Read the file content into a String
        String jsonConfig = new String(jsonFile.getBytes(), StandardCharsets.UTF_8);

        // Convert the String content to GeneratedJson object using ObjectMapper
        ObjectMapper objectMapper = new ObjectMapper();
        GeneratedJson generatedJson = objectMapper.readValue(jsonConfig, GeneratedJson.class);

        // Now proceed with the rest of your logic (existing code)
        // Example:
        Optional<PlatformDetailInfo> platform = platformDetailInfoRepository.findByPlatformCode(generatedJson.getPlatformCode());
        if (platform.isPresent()) {
            PlatformDetailInfo newPlatform = platform.get();
            // Additional logic...
        }

        // Save the imported configuration and return success message
        return "Configuration imported and saved successfully.";
    } catch (JsonProcessingException e) {
        throw new RuntimeException("Error parsing the imported configuration JSON", e);
    } catch (Exception e) {
        throw new RuntimeException("Error importing configuration", e);
    }
}

Key Changes:

1. Controller: The importConfig method now takes a MultipartFile jsonFile as input.


2. Service: The service method now reads the file content using jsonFile.getBytes() and converts it into a String to process it with ObjectMapper.



Frontend Side:

When sending the file from the frontend, you should make sure the form method is POST with enctype="multipart/form-data". Here is an example of how to upload the file:

<form method="POST" action="/config/import" enctype="multipart/form-data">
    <input type="file" name="file" />
    <button type="submit">Upload</button>
</form>

With these changes, the backend will be able to receive a JSON file, parse it, and then proceed with the import process.

Let me know if you need further adjustments or help!

