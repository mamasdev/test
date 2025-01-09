# test

```javascript
public String exportConfig(Integer configId) {
    try {
        // Fetch the configuration by ID
        Optional<GenerateConfig> generateConfig = generateConfigRepository.findById(configId);
        
        if (generateConfig.isPresent()) {
            // Use ObjectMapper to convert the GenerateConfig object to JSON
            ObjectMapper objectMapper = new ObjectMapper();
            String configJson = objectMapper.writeValueAsString(generateConfig.get());
            
            // Save the JSON to a file or return it as a string (depending on how you want to handle the export)
            // For example, save it to a file:
            Path path = Paths.get("config_export_" + configId + ".json");
            Files.write(path, configJson.getBytes());

            return "Configuration exported successfully to " + path.toString();
        } else {
            throw new ElementNotFoundException("Configuration not found for the provided ID");
        }
    } catch (Exception e) {
        throw new ServiceException("Error exporting configuration", e);
    }
}


public String importConfig(String jsonConfig) {
    try {
        // Parse the JSON string to the GenerateConfig object
        ObjectMapper objectMapper = new ObjectMapper();
        GenerateConfig generateConfig = objectMapper.readValue(jsonConfig, GenerateConfig.class);

        // Check if the associated Platform exists
        Optional<Platform> platform = platformDetailInfoRepository.findById(generateConfig.getPlatformEnvironment().getPlatformId());

        if (!platform.isPresent()) {
            // If the Platform doesn't exist, create it
            Platform newPlatform = new Platform();
            newPlatform.setId(generateConfig.getPlatformEnvironment().getPlatformId()); // Set the ID from the imported data
            newPlatform.setName(generateConfig.getPlatformEnvironment().getPlatformName());
            newPlatform.setPlatformCode(generateConfig.getPlatformEnvironment().getPlatformCode());

            // Optionally, set other properties for the new Platform here
            platformDetailInfoRepository.save(newPlatform);

            // Now associate the generated Platform to the PlatformEnvironment
            generateConfig.getPlatformEnvironment().setPlatform(newPlatform);
        } else {
            // If the Platform exists, associate it with the PlatformEnvironment
            generateConfig.getPlatformEnvironment().setPlatform(platform.get());
        }

        // Check if the associated PlatformEnvironment exists
        Optional<PlatformEnvironment> platformEnvironment = platformEnvironmentRepository.findById(generateConfig.getPlatformEnvironment().getId());

        if (!platformEnvironment.isPresent()) {
            // If the PlatformEnvironment doesn't exist, create it
            PlatformEnvironment newPlatformEnvironment = new PlatformEnvironment();
            newPlatformEnvironment.setId(generateConfig.getPlatformEnvironment().getId()); // Set the ID from the imported data
            newPlatformEnvironment.setName(generateConfig.getPlatformEnvironment().getName());
            newPlatformEnvironment.setEnvCode(generateConfig.getPlatformEnvironment().getEnvCode());
            newPlatformEnvironment.setEnvType(generateConfig.getPlatformEnvironment().getEnvType());

            // Associate with the existing or newly created Platform
            newPlatformEnvironment.setPlatform(generateConfig.getPlatformEnvironment().getPlatform());

            // Optionally, set other properties for the new PlatformEnvironment here
            platformEnvironmentRepository.save(newPlatformEnvironment);

            // Now associate the generated PlatformEnvironment to the imported config
            generateConfig.setPlatformEnvironment(newPlatformEnvironment);
        }

        // If the PlatformEnvironment exists or has been created, set the lastModifiedAt field
        generateConfig.setLastModifiedAt(new Date());

        // Save the configuration to the repository
        generateConfigRepository.save(generateConfig);

        return "Configuration imported and saved successfully.";
    } catch (JsonProcessingException e) {
        throw new ServiceException("Error parsing the imported configuration JSON", e);
    } catch (Exception e) {
        throw new ServiceException("Error importing configuration", e);
    }
}

