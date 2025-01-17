import org.springframework.web.multipart.MultipartFile;

public class ConfigAitDTO {

    private String ait;  // AIT field
    private MultipartFile configFile;  // File upload field

    // Getters and Setters
    public String getAit() {
        return ait;
    }

    public void setAit(String ait) {
        this.ait = ait;
    }

    public MultipartFile getConfigFile() {
        return configFile;
    }

    public void setConfigFile(MultipartFile configFile) {
        this.configFile = configFile;
    }
}
