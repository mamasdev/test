import org.springframework.web.multipart.MultipartFile;
import com.fasterxml.jackson.databind.ObjectMapper;

public String importConfig(String destinationAit, MultipartFile jsonConfig) throws Exception {
    try {
        // Get the current logged-in user
        LoggedInUser user = (LoggedInUser) request.getAttribute("loggedInUser");

        // Initialize the ObjectMapper to parse the JSON
        ObjectMapper objectMapper = new ObjectMapper();

        // Read the content of the uploaded JSON file into a string
        String jsonString = new String(jsonConfig.getBytes());

        // Deserialize the string into a GeneratedJson object
        GeneratedJson generatedJson = objectMapper.readValue(jsonString, GeneratedJson.class);

        // Check if the destination AIT matches the AIT in the uploaded configuration
        if (!destinationAit.equals(generatedJson.getPlatformAIT())) {
            throw new Exception("Imported configuration AIT doesn't match the destination AIT.");
        }

        // Find the platform by the platform code in the uploaded JSON
        Optional<PlatformDetailInfo> platform = platformDetailInfoRepository.findByPlatformCode(generatedJson.getPlatformCode());
        if (platform.isPresent()) {
            PlatformDetailInfo newPlatform = new PlatformDetailInfo();
            newPlatform.setPlatformName(generatedJson.getPlatformName());
            newPlatform.setPlatformCode(generatedJson.getPlatformCode());
            newPlatform.setPlatformAIT(generatedJson.getPlatformAIT());
            newPlatform.setCreatedAt(Utils.convertDateToUTCTimezone(new Date(System.currentTimeMillis())));
            newPlatform.setLastModifiedAt(Utils.convertDateToUTCTimezone(new Date(System.currentTimeMillis())));
            newPlatform.setLastModifiedBy(user.getNbkid());
            newPlatform.setCreatedBy(user.getNbkid());
            newPlatform.setIsActive(true);

            // Save the new platform if not already present
            platformDetailInfoRepository.save(newPlatform);
        }

        //
