private static String submitDatabricksJob() throws IOException {
    // Create the API endpoint URL for submitting a job
    URL url = new URL(WORKSPACE_URL + "/jobs/runs/submit");

    // Create a connection to the Databricks API
    HttpURLConnection connection = (HttpURLConnection) url.openConnection();
    connection.setRequestMethod("POST");
    connection.setRequestProperty("Authorization", "Bearer " + API_TOKEN);
    connection.setRequestProperty("Content-Type", "application/json");
    connection.setDoOutput(true);

    // JSON payload for job submission with parameters
    String payload = "{\"job_id\": \"" + JOB_ID + "\", \"cluster_id\": \"" + CLUSTER_ID + "\", \"notebook_params\": {\"param_name\": \"param_value\"}}";

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
        JSONObject jsonResponse = new JSONObject(response.toString());
        String runId = jsonResponse.getString("run_id");
        return runId;
    } finally {
        connection.disconnect();
    }
}
