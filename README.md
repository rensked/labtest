# Introduction to Azure Stream Analytics#

---

<a name="Overview"></a>
## Overview ##

In this lab, you'll send data to Microsoft Azure, using the data from the device of the previous labs, and to implement IoT solutions taking advantage of Microsoft Azure advanced analytic services such as Azure Stream Analytics.


<a name="Objectives"></a>
### Objectives ###
In this module, you'll see how to:

- Use Azure Stream Analytics to process the data from your device
- Create a dashboard in Power BI to analyze the data

<a name="Prerequisites"></a>
### Prerequisites ###

The following is required to complete this module:

- Windows 10 with [developer mode enabled][1]
- [Visual Studio Community 2015][2] with [Update 1][3] or greater
- [Azure Device Explorer][7].

[1]: https://msdn.microsoft.com/library/windows/apps/xaml/dn706236.aspx
[2]: https://www.visualstudio.com/products/visual-studio-community-vs
[3]: http://go.microsoft.com/fwlink/?LinkID=691134
[7]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md


---

<a name="Exercises"></a>
## Exercises ##
This module includes the following exercises:

1. [Setting up the app to send data to Azure](#Exercise1)
1. [Using Stream Analytics to process the data](#Exercise2)
1. [Analyzing telemetry data using Power BI](#Exercise3)

Estimated time to complete this module: **20 minutes**

<a name="Exercise1"></a>
### Exercise 1: Setting up the app to send data to Azure ###

In this exercise, you'll again send telemetry data (light level) to Azure IoT Hub, as was done in previous modules.

<a name="Ex1Task1"></a>
#### Task 1 - Reviewing the IoT Hub and registrering your device ####

In this task, you'll return to the created IoT Hub.

Considering you completed the previous modules, you are now able to send data to the Azure IoT hub. In case this is not done, review the lab of Module 2, where you set up the Azure IoT hub and register your device. Take a note of the connection string of the iothubowner in the shared access policy settings of the IoT hub.  

<a name="Ex1Task2"></a>
#### Task 2 - Sending telemetry data to the Azure IoT hub ####

Now you'll again send telemetry data to the Azure IoT Hub.

1. Open in Visual Studio the **xxxxxx.sln** solution located at **/xxxxx** folder.

1. In case you have not done this, enter the connection string of the registred device in **MainPage.xaml.cs**. This information can be retrieved in **Device explorer**

1. Press **F5** to run and debug the application.

	The information being sent can be monitored using the Device Explorer application. Run the application and go to the **Data** tab and select the name of the device you want to monitor (_DhasPI_ in your case), then click on **Monitor**

1. Make sure to tick **Enable** and insert **powerbi** as the Consumer Group. This will explained in the next task. 

	![Monitoring messages sent](Images/monitoring-messages-sent.png?raw=true "Monitoring messages sent")

	_Monitoring messages sent_

<a name="Exercise2"></a>
### Exercise 2: Using Stream Analytics to process the data ###

You have seen how to use the **Device Explorer** to peek at the data being sent to the Azure IoT Hub. However, the Azure IoT suite offers many different ways to generate meaningful information from the data gathered by the devices.

In this exercise you will use Azure Stream Analytics to process the data and generate meaningful information.

<a name="Ex2Task1" />
#### Task 1 - Create a Service Bus Consumer Group ####
In order to allow several consumer applications to read data from the IoT Hub independently, a **Consumer Group** must be configured for each one. If all of the consumer applications (the Device Explorer, Stream Analytics / Power BI, the Web site you'll configure in the next section) read the data from the default consumer group, only one application will retain the lease and the others will be disconnected.

In this task, you'll create a new Consumer Group that will be used by a Stream Analytics job.

1. Open the Azure Portal (https://portal.azure.com/), and select the IoT Hub you created.

1. From the settings blade, click **Messaging**.

1. At the bottom of the **Messaging** blade, type the name of the new **Consumer Group**: "PowerBI"

    ![Create Consumer Group](Images/create-consumer-group.png?raw=true "Create Consumer Group")

	_Create Consumer Group_

1. From the top menu, click **Save**.

<a name="Ex2Task2"></a>
#### Task 2 - Creating a Stream Analytics Job ####
Before the information can be consumed (for instance using **Power BI**), it must be processed by a **Stream Analytics Job**. To do so, an input for that job must be provided. As the simulated devices are sending information to an IoT Hub, we'll use these as the input for the job.

> **Note:** You will create the Stream Analytics job using the [classic Azure Portal](https://manage.windowsazure.com) since the _Power BI_ sink for Stream Analytics output is not available in the new Azure Portal yet.

1. Go to the classic Azure Portal (https://manage.windowsazure.com/), and click **NEW** on the bottom bar.

1. Then select **DATA SERVICES > STREAM ANALYTICS > QUICK CREATE** and enter the required fields:
	- **Job Name**: Enter a job name.
	- **Region**: West Europe
	- **Subscription**: If you have more than one subscription associated to your account, select the one where you created the IoT hub.
	- **Regional Monitoring Storage Account**: Choose the Azure storage account that you would like to use to store monitoring data for all Stream Analytics jobs running within this region. You have the option to choose an existing storage account or to create a new one.

	![Creating Stream Analytics job](Images/new-stream-analytics.png?raw=true "Creating Stream Analytics job")

	_Creating Stream Analytics job_

1. The new job will be shown with a status of **Created**. Notice that the Start button is disabled. You must configure the job **Input**, **Output**, and **Query** before you can start the job.

<a name="Ex2Task3"></a>
#### Task 3 - Defining the Stream Analytics Job Input ####

In this task, you'll specify a job Input using the IoT Hub you previously created.

1. Go to your Stream Analytics job, click on the **INPUTS** tab and then in the **ADD AN INPUT** button.

1. In the **Add an input to your job** popup, select the **Data Stream** option and click **Next**. In the following step, select the option **IoT Hub** and click **Next**. Lastly, in the **IoT Hub Settings** screen, provide the following information:
	- **Input Alias**: TelemetryHUB
	- **Subscription:** Use IoT Hub from Current Subscription
	- **Choose an IoT Hub**: _Use the one you used for the IoT Hub you created_
	- **IoT Hub Shared Access Policy Name**: iothubowner
	- **Shared access policy key**: _The primary key of the iothubowner access policy_
	- **IoT Hub Consumer Group**: powerbi

	![Creating Stream Analytics Input](Images/new-stream-analytics-input.png?raw=true "Creating Stream Analytics Input")

	_Creating Stream Analytics Input_


4. Click **Next**, and then **Complete** (leave the Serialization settings as they are).

<a name="Ex2Task4" />
#### Task 4 - Defining the Stream Analytics Job Output ####
The output of the Stream Analytics job will be **Power BI**.

1. In your Stream Analytics job click **OUTPUTS** tab, and click the **ADD AN OUTPUT** link. 

1. In the **Add an output to your job** popup, select the **POWER BI** option and the click the **Next button**.

1. In the following screen you will setup the credentials of your Power BI account in order to allow the job to connect and send data to it. Click the **Authorize Now** link. You will be redirected to the Microsoft login page.

1. Enter your Power BI account email and password and click **Continue**. If the authorization is successful, you will be redirected back to the **Microsoft Power BI Settings** screen.

1. In this screen you will enter the following information:

	- **Output alias**: PowerBI
	- **Dataset Name**: Simulated
	- **Table Name**: Telemetry
	- **Workspace**: My Workspace

1. Click **Complete** to create the output.

    <!-- TODO: update this screenshot when Power BI sink available in new portal -->

	![Creating Stream Analytics Output](Images/new-stream-analytics-output.png?raw=true "Creating Stream Analytics Output")

	_Creating Stream Analytics Output_

<a name="Ex2Task5"></a>
#### Task 5 - Defining the Stream Analytics Job Query ####
Now that the job's inputs and outputs are configured, the Stream Analytics Job needs to know how to transform the input data into the output data source. To do so, you'll create a new Query.

1. Go to the Stream Analytics Job **QUERY** tab and replace the query with the following statement:

	````SQL
	            SELECT
                               DeviceName,
                               guid,
                               measurename,
                               Max(measurementTime) measurementTime,
                               Avg(value) AvgValue
                INTO
                               [PowerBI]
                FROM
                               [TelemetryHUB] TIMESTAMP by measurementTime
                GROUP BY
                               DeviceName, guid, measurename,
                               TumblingWindow(Second, 10)
	````

	The query takes the data from the input (using the alias defined when the input was created **TelemetryHUB**) and inserts into the output (**PowerBI**, the alias of the output) after grouping it by using a 10 second window.

	![Creating Stream Analytics Query](Images/stream-analytics-query.png?raw=true "Creating Stream Analytics Query")

	_Creating Stream Analytics Query_

1. Click on the **SAVE** button and **YES** in the confirmation dialog.

<a name="Ex2Task6"></a>
#### Task 6 - Starting the Stream Analytics Job ####
In this task, you'll run the Stream Analytics job and view the output in Visual Studio.

1. Click the **START** button at the bottom bar.

1. In the **Start Output** popup, select the **JOB START TIME** option. After clicking **OK** the job will be started. The job status will change to Starting; it can take several minutes to start.

    Once the job starts it creates the Power BI datasource associated with the given subscription.

<a name="Exercise3"></a>
### Exercise 3: Analyzing the telemetry data using Power BI ###
Power BI is a cloud based data analysis, which can be used for reporting and data analysis from wide range of data source. Power BI is simple and user friendly enough that business analysts and power users can work with it and get benefits of it.

In this exercise, you'll create reports in Power BI to visualize the telemetry data processed by Stream Analytics and sent by the device through the Azure IoT hub.

<a name="Ex3Task1"></a>
#### Task 1 - Creating a Light By Time report ####
After some minutes of the job running, you'll see that the dataset that you configured as an output for the Job, is now displayed in the Power BI workspace Datasets section.

1. Go to [PowerBI.com](https://powerbi.microsoft.com/) and login with your work or school account. If the Stream Analytics job query outputs results, you'll see your dataset is already created:

	![Power BI: New Datasource](Images/power-bi-new-datasource.png?raw=true "Power BI: New Datasource")

	_Power BI: New Datasource_

	> **Note:** The Power BI dataset will only be created if the job is running and if it is receiving data from the IoT Hub input, so check that the Universal App is running and sending data to Azure to ensure that the dataset be created.

1. Once the datasource becomes available you can start creating reports. To create a new Report click on the **Simulated** datasource:

	![Power BI: Report Designer](Images/power-bi-report-designer.png?raw=true "Power BI: Report Designer")

	_Power BI: Report Designer_

	The Report designer will open showing the list of available fields for the selected datasource and the different visualizations supported by the tool.

1. To create the _Average Light by time_ report, select the following fields:

	- avgvalue
	- measurementTime

	As you can see the **avgvalue** field is automatically set to the **Value** field and the **timecreated** is inserted as an axis. Now change the chart type to a **Line Chart**:

	![Selecting the Line Chart](Images/select-line-chart.png?raw=true "Selecting the Line Chart")

	_Selecting the Line Chart_

1. To create a filter to show only the Light sensor data, drag the **devicename** field to the **Filters** section, and then select the **Photocell** value:

	![Select Report Filter](Images/select-report-filter.png?raw=true "Select Report Filter")
	![Select Light sensor values](Images/select-light-sensor-values.png?raw=true "Select Light sensor values")

	_Selecting the Report Filters_

1. Now the report is almost ready. Click **Save** and set _Light by Time_ as the name for the report.

	![Light by Time Report](Images/light-by-time-report.png?raw=true "Light by Time Report")

	_Light by Time Report_

<a name="Ex3Task2"></a>
#### Task 2 - Creating a Power BI dashboard ####
In this task, you'll create a new Dashboard, and pin the _Light by Time_ report to it.

1. Click the plus sign (+) next to the **Dashboards** section to create a new dashboard.

1. Set _Simulated Telemetry_ as the **Title** and press Enter.

1. Go back to your report, and click the pin icon to add the report to the recently created dashboard.

	![Pin a Report to the Dashboard](Images/pin-a-report-to-the-dashboard.png?raw=true "Pin a Report to the Dashboard")

	_Pinning a Report to the Dashboard_

<a name="Summary"></a>
## Summary ##

By completing this module, you should have:

- Used **Stream Analytics** to process data in near-realtime and spool data to **Power BI**.
- Created a **Power BI** reports and dashboards with graphs to visualize the telemetry data.

<a name="Optional"></a>
## Optional ##

Try using other data of the other sensors and visualize this in PowerBI. 