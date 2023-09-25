`# Create a Python file (e.g., custom_listener.py) with the following code:
from pyspark import SparkContext
from pyspark.sql import SparkSession
from pyspark.scheduler import SparkListener, SparkListenerApplicationStart, SparkListenerApplicationEnd
import os

class CustomSparkListener(SparkListener):
    def __init__(self, dbfs_path):
        self.dbfs_path = dbfs_path
        self.spark_ui_tabs = []

    def onApplicationStart(self, applicationStart):
        # Add the custom tab to Spark UI when the cluster starts
        self.add_custom_tab()

    def onApplicationEnd(self, applicationEnd):
        # Remove the custom tab when the cluster terminates
        self.remove_custom_tab()

    def add_custom_tab(self):
        # Add a custom tab to Spark UI with HTML files from the specified DBFS path
        tab_name = "Custom HTML Viewer"
        tab_content = f"""
        <iframe src="/tab/{tab_name}" width="100%" height="800px" frameborder="0"></iframe>
        <script>
            fetch('/files/{self.dbfs_path}').then(response => response.json()).then(data => {{
                const container = document.querySelector('iframe').contentDocument.body;
                data.files.forEach(file => {{
                    const link = document.createElement('a');
                    link.href = `/files/{self.dbfs_path}/${{file.name}}`;
                    link.textContent = file.name;
                    link.style.display = 'block';
                    link.style.margin = '5px';
                    link.addEventListener('click', (e) => {{
                        e.preventDefault();
                        container.innerHTML = '';
                        const iframe = document.createElement('iframe');
                        iframe.src = e.target.href;
                        iframe.width = '100%';
                        iframe.height = '800px';
                        container.appendChild(iframe);
                    }});
                    container.appendChild(link);
                }});
            }});
        </script>
        """
        self.spark_ui_tabs.append((tab_name, tab_content))

    def remove_custom_tab(self):
        # Remove the custom tab from Spark UI when the cluster terminates
        for tab_name, _ in self.spark_ui_tabs:
            self.spark_ui_tabs.remove((tab_name, _))


def attach_custom_listener(dbfs_path):
    spark = SparkSession.builder.getOrCreate()
    sc = SparkContext.getOrCreate()
    custom_listener = CustomSparkListener(dbfs_path)
    sc.addSparkListener(custom_listener)

# Usage:
dbfs_path = "/your/dbfs/path"
attach_custom_listener(dbfs_path)
` 
