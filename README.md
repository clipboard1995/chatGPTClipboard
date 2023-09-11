import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.OutputStream;
import java.net.HttpURLConnection;
import java.net.URL;

public class DatabricksJobManager {

    private static final String API_TOKEN = "YOUR_DATABRICKS_API_TOKEN";
    private static final String WORKSPACE_URL = "https://<YOUR_WORKSPACE_URL>/api/2.0";
    private static final String CLUSTER_ID = "YOUR_CLUSTER_ID";
    private static final String JOB_ID = "YOUR_JOB_ID";

    public static void main(String[] args) throws IOException {
        // Step 1: Submit a Databricks job with parameters
        String runId = submitDatabricksJob();
        System.out.println("Job submitted. Run ID: " + runId);

        // Step 2: Get the status of the submitted job using the run ID
        String status = getJobStatus(runId);
        System.out.println("Job Status: " + status);
    }

    private static String submitDatabricksJob() throws IOException {
        // ... (Same as previous code)
    }

    private static String getJobStatus(String runId) throws IOException {
        // Create the API endpoint URL for getting job status
        URL url = new URL(WORKSPACE_URL + "/jobs/runs/get?run_id=" + runId);

        // Create a connection to the Databricks API
        HttpURLConnection connection = (HttpURLConnection) url.openConnection();
        connection.setRequestMethod("GET");
        connection.setRequestProperty("Authorization", "Bearer " + API_TOKEN);

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

