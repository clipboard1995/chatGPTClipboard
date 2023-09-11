public String submitJobWithParameters(String jobId, String notebookPath, String parameters) throws IOException {
    // Create the API endpoint URL for submitting a job
    URL url = new URL(workspaceUrl + "/jobs/runs/submit");

    // Create a connection to the Databricks API
    HttpURLConnection connection = (HttpURLConnection) url.openConnection();
    connection.setRequestMethod("POST");
    connection.setRequestProperty("Authorization", "Bearer " + apiToken);
    connection.setRequestProperty("Content-Type", "application/json");
    connection.setDoOutput(true);

    // JSON payload for job submission with parameters and notebook path
    String payload = "{\"job_id\": \"" + jobId + "\", \"cluster_id\": \"" + clusterId + "\", \"notebook_task\": {\"notebook_path\": \"" + notebookPath + "\", \"base_parameters\": " + parameters + "}}";

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

        // Extract the job ID from the response
        return response.toString();
    } finally {
        connection.disconnect();
    }
}
