import org.apache.spark.scheduler._

class CustomTabSparkListener extends SparkListener {

  override def onUIStarted(ui: SparkListenerUI): Unit = {
    // Define your custom tab's name and content here
    val customTabName = "Custom Tab"
    val customTabContent = s"""
      <iframe src="/custom-tab" width="100%" height="800" frameborder="0"></iframe>
    """

    // Add the custom tab to the Spark UI
    ui.attachTab(customTabName, customTabContent)
  }
}
