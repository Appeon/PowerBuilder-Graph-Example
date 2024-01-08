# Echarts Demo Readme

This demo is a PowerBuilder application, developed with Appeon [PowerBuilder 2022 R3](https://www.appeon.com/products/powerbuilder). It shows:

1. How to use the PowerBuilder WebBrowser control to render the third-party visual charts which are exposed as JavaScript classes. 
2. How to execute JavaScript in the PowerBuilder application to dynamically adjust data display in different chart styles.
3. How to connect the chart event with the WebBrowser event so that JavaScript and PowerScript can interact with each other.

### Setting Up the Project

Download this PowerBuilder demo application, and then:

1. Open the PowerBuilder project in PowerBuilder 2022 R3.
2. If you already had the corresponding PostgreSQL database driver installed on the machine when you installed PowerBuilder 2022 R3 and selected PostgreSQL for Database Provider, you can just skip step#3 and step#4 below to run the PowerBuilder project directly. Otherwise, please follow the steps below to create the ODBC data source. 
3. Download the database file <b>pbdemo2021_for_postgresql.zip</b> from [PowerBuilder-Project-Example-Database](https://github.com/Appeon/PowerBuilder-Project-Example-Database). 
4. Use the downloaded database file to create a new ODBC data source referring to the below must-configure connection settings:

   ```
   Data source name: 	PB Demo DB V2022R3	//Must be the same as the Data source name specified in the code.
   User ID: 	         dba
   Password: 	         sql
   ```
5. Run the PowerBuilder project.

### General steps to apply a chart (exposed as JavaScript classes)

Step 1: Create an HTML page which contains the following:

-   References to the online JS and CSS files for the chart.

-   A div or canvas container where the chart will be rendered.

-   An event listener for the Resize event, so that the div or canvas container can be automatically resized.

Step 2: Create a PowerBuilder window and add a WebBrowser control to the window. Set the WebBrowser control to open the HTML page created in step 1.

Step 3: Define a ue_clicked user event for the WebBrowser control and register it before it's triggered.

Step 4: Use the EvaluateJavaScriptSync and EvaluateJavaScriptAsync functions of the WebBrowser control to generate the chart.

Step 5: If the JavaScript classes support user event bindings, then bind window.webBrowser.ue_clicked with the WebBrowser ue_clicked event so that arguments can be passed between them.

### To apply ECharts

Step 1: Create an HTML page which contains the following:

-   References to the offline JS files for ECharts: echarts.js and echarts-gl.js.

-   A div container where the chart will be rendered.

-   An event listener for the Resize event, so that the div container can be automatically resized.

For a sample HTML page, see echarts.html.

Step 2: Create a PowerBuilder window and add a WebBrowser control to the window. Set the defaultUrl property of the WebBrowser control to open the HTML page created in step 1.

For example, defaultUrl="file:///echarts.html"

For a sample window and WebBrowser control, see the n_webbrowser user object in this demo.

Step 3: Define a user event for the WebBrowser control and then register it before it's triggered.

For example,

event ue_clicked(string as_arg);

end event
```
RegisterEvent("ue_clicked");
```

For a sample code, see the n_webbrowser user object in this demo.

Step 4: Use the EvaluateJavaScriptSync and EvaluateJavaScriptAsync functions of the WebBrowser control to execute JavaScript to define two variables for the HTML page:

-   options - the value comes from the DataWindow, and generated as JSON strings through JSONGenerator

-   myChart - myChart.setOption(option) generates the chart

For a sample code, see of_apply and of_apply_async functions in n_webbrowser.

Step 5: Bind the select event of the point in the chart with the selectHandler function, so that the WebBrowser ue_clicked event can be triggered when window.webBrowser.ue_clicked is triggered, and values can be passed by as_arg between PowerScript and JavaScript.

### To import ECharts from demo

The demo app has shown cases for many chart styles including Line, Candlestick(K-Line), Area, Bar, Pie, Scatter, Radar, Funnel and Gauge.

If you want to directly integrate the ECharts from the demo app into your app, take the following steps:

1.  Add echarts_base.pbl to your application.

2.  Call the interface in the n_webbrowser user object of echarts_base.pbl.

n_webbrowser has encapsulated the interfaces required for interfering with the chart.

The of_CreateOption function etc. generate the JSON string from the graph DataObject, DataWindow, and SQL statements and then generate the chart from the JSON string. For example,
```
String ls_Title, ls_Option, ls_data
Boolean lb_Toolbox
//Title
ls_Title = dw_1.Describe("gr_1.title")
wb_1.of_SetTitle(ls_Title)
//Style
wb_1.of_SetStyle("area")
//Width
wb_1.of_SetWidth(750)
//Height
wb_1.of_SetHeight(400)
//ToolBox
wb_1.of_SetToolBox(lb_Toolbox)
//CreateOption
ls_Option = wb_1.of_CreateOption_Graph(dw_1)
//SetOption
wb_1.of_SetOption(ls_Option)
//Apply
wb_1.of_Apply()
```

The other options for the chart are configured through the options variable. If not configured, the chart will use the default system settings.

For example, to configure the color, you can add an array in the options argument:

{title:{text:'default title',left:'center'},color:['red','#004411','rgb(128,128,128)']}

Note: The current version of echarts.js is 5.0.2 and echarts-gl.js is 2.0.2. You can download and use a newer version of these files from https://github.com/apache/echarts/releases.
