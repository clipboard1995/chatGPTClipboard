import org.apache.spark.SparkConf
import org.apache.spark.scheduler.SparkListener
import org.apache.spark.scheduler.SparkListenerEvent
import org.apache.spark.scheduler.SparkListenerTabAttached
import org.apache.spark.sql.SparkSession

// Create a Spark configuration
val conf = new SparkConf()
  .setAppName("CustomTabListenerExample")

// Create a Spark session
val spark = SparkSession.builder.config(conf).getOrCreate()

// Define a custom Spark listener by extending the SparkListener class
class CustomTabSparkListener extends SparkListener {
  override def onOtherEvent(event: SparkListenerEvent): Unit = {
    event match {
      case tabAttached: SparkListenerTabAttached =>
        // Define your custom tab's name and content here
        val customTabName = "Custom Tab"
        val customTabContent = s"""
          <iframe src="/custom-tab" width="100%" height="800" frameborder="0"></iframe>
        """
        
        // Add the custom tab to the Spark UI
        tabAttached.attachTab(customTabName, customTabContent)
      case _ =>
    }
  }
}

// Create an instance of your custom listener and register it with the Spark session
val customListener = new CustomTabSparkListener()
spark.sparkContext.addSparkListener(customListener)
