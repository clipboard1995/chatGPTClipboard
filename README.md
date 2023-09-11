import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.OutputStream;
import java.net.HttpURLConnection;
import java.net.URL;

public class DatabricksJobManager {

    private final String workspaceUrl;
    private final String clusterId;
    private final String apiToken;

    public DatabricksJobManager(String workspaceUrl, String clusterId, String apiToken) {
        this.workspaceUrl = workspaceUrl;
        this.clusterId = clusterId;
        this.apiToken = apiToken;
    }

    public String submitJobWithParameters(String jobId, String jobParameters) throws IOException {
        // Create the API endpoint URL for submitting a job
        URL url = new URL(workspaceUrl + "/jobs/runs/submit");

        // Create a connection to the Databricks API
        HttpURLConnection connection = (HttpURLConnection) url.openConnection();
        connection.setRequestMethod("POST");
        connection.setRequestProperty("Authorization", "Bearer " + apiToken);
        connection.setRequestProperty("Content-Type", "application/json");
        connection.setDoOutput(true);

        // JSON payload for job submission with parameters
        String payload = "{\"job_id\": \"" + jobId + "\", \"cluster_id\": \"" + clusterId + "\", \"notebook_params\": " + jobParameters + "}";

        // Write the payload to the request
        try (OutputStream os = connection.getOutputStream()) {
            byte[] input = payload.getBytes("utf-8");
            os.write(input, 0, input.length);
        }

        // Get the response from the API
        try (BufferedReader br = new BufferedReader(new InputStreamReader(connection.getInputStream(), "utf-8"))) {
            StringBuilder response = new StringBuilder();
            String responseLine;
            while ((responseLine = br.readLine()) != null) {
                response.append(responseLine.trim());
            }

            // Extract the job ID (run ID) from the response
            return response.toString();
        } finally {
            connection.disconnect();
        }
    }

    public String getJobStatus(String runId) throws IOException {
        // Create the API endpoint URL for getting job status
        URL url = new URL(workspaceUrl + "/jobs/runs/get?run_id=" + runId);

        // Create a connection to the Databricks API
        HttpURLConnection connection = (HttpURLConnection) url.openConnection();
        connection.setRequestMethod("GET");
        connection.setRequestProperty("Authorization", "Bearer " + apiToken);

        // Get the response from the API
        try (BufferedReader br = new BufferedReader(new InputStreamReader(connection.getInputStream(), "utf-8"))) {
            StringBuilder response = new StringBuilder();
            String responseLine;
            while ((responseLine = br.readLine()) != null) {
                response.append(responseLine.trim());
            }
            return response.toString();
        } finally {
            connection.disconnect();
        }
    }
}
