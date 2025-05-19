Please note :

 - in all queries we remove UserInitiated event
 - To correlate row from same event we use CorrelationId
 - The duration of events is calculated with the difference of the first event and the last event

# Duration of Resource health event

AzureActivity
| where Category == "ResourceHealth"
| where parse_json(Properties).cause != "UserInitiated"
| extend cause = tostring(parse_json(Properties).cause)
| extend currentHealthStatus = tostring(parse_json(Properties).currentHealthStatus)
| extend details = tostring(parse_json(Properties).details)
| extend type = tostring(parse_json(Properties).type)
| summarize arg_max(TimeGenerated,*), arg_min(TimeGenerated,*) by CorrelationId
| extend diff= datetime_diff('second',TimeGenerated,TimeGenerated2 )
| project TimeGenerated, diff, ResourceId, Level,CorrelationId


# Count Resource Health event by Resource

AzureActivity
| where Category == "ResourceHealth"
| where parse_json(Properties).cause != "UserInitiated"
| extend cause = tostring(parse_json(Properties).cause)
| extend currentHealthStatus = tostring(parse_json(Properties).currentHealthStatus)
| extend details = tostring(parse_json(Properties).details)
| extend type = tostring(parse_json(Properties).type)
| summarize count() by ResourceId

## Create a timechart of Resource Health event by ResourceId

AzureActivity
| where Category == "ResourceHealth"
| where parse_json(Properties).cause != "UserInitiated"
| extend cause = tostring(parse_json(Properties).cause)
| extend currentHealthStatus = tostring(parse_json(Properties).currentHealthStatus)
| extend details = tostring(parse_json(Properties).details)
| extend type = tostring(parse_json(Properties).type)
| summarize count() by CorrelationId, TimeGenerated,ResourceId
| summarize count() by bin(TimeGenerated,1d),ResourceId
| render timechart