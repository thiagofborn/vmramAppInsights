# Enabling Alert via Memory RAM consumption Azure VM

1. Enable Monitoring via Application Insights
2. Create a query

## Enable Monitoring via Application Insights

Pre-requisite:
The VM must be running to enable the Application Insights monitor agent.

Go to the Azure Portal, and find the VM you want to enable the monitoring process via App Insights. 

Feel free to follow the picture.

![To help find things. Please note that the Azure Portal may change in the future](media/enable-appInsights-001.jpg 'Common configuration')

After you click on the **Enable** buttom, you need to pick the subscription and the **Log Analytics Work Space**. 

![To help find things. Please note that the Azure Portal may change in the future](media/enable-appInsights-002.jpg 'Common configuration')

>Note: You may need to wait for the data to be populated at this Work Space.

## Let's check the **Logs** Workspace

At this same page, on the left **Azure Portal blade**, we can see the **Logs** link. Clink on it.

![To help find things. Please note that the Azure Portal may change in the future](media/enable-appInsights-003.jpg 'Common configuration')

Then select **InsightsMetrics**:

![To help find things. Please note that the Azure Portal may change in the future](media/enable-appInsights-004.jpg 'Common configuration')


## Create a query

Available Memory in percentage:

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Memory" and Name == "AvailableMB"
| extend TotalMemory = toreal(todynamic(Tags)["vm.azm.ms/memorySizeMB"])
| extend AvailableMemoryPercentage = (toreal(Val) / TotalMemory) * 100.0
| summarize AggregatedValue = avg(AvailableMemoryPercentage) by bin(TimeGenerated, 15m), Computer, _ResourceId
```
Paste the query for memory consumption:

![To help find things. Please note that the Azure Portal may change in the future](media/enable-appInsights-005.jpg 'Common configuration')

>Note: the **+New alert rule** buttom now is available.

## Create the new alert Rule

Click on **+New alert rule**, and follow the instructions.

## References

1. Create a log Alert - <https://docs.microsoft.com/en-us/azure/azure-monitor/platform/alerts-log>
