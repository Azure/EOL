# End of Life Overview

Currently, the following methods can be used to be notified of service retirements:

- Direct Email: Notifications are sent to subscription owners and administrators via email.
- Azure Portal Notifications: Available in the Service Health blade within the Azure portal.
- Azure Updates: Public announcements are made on the Azure Updates page.
- Service Retirement Workbook in Azure Advisor provides a centralized view of service retirements, helping customers assess impacts and plan migrations.

## Service Health
Service health can be used to obtain a list of service retirements. The retirement notifications will remain in Service Health for 60 days so it's important to capture this information for your records. You can use the following query to see any retirements:

```kql
ServiceHealthResources
| where type =~ 'Microsoft.ResourceHealth/events'
| extend eventType = properties.EventType, status = properties.Status, description = properties.Title, trackingId = properties.TrackingId, summary = properties.Summary, priority = properties.Priority, impactStartTime = properties.ImpactStartTime, impactMitigationTime = todatetime(tolong(properties.ImpactMitigationTime))
| where eventType == 'HealthAdvisory’
```

## JSON File Overview
The JSON file found at https://github.com/Azure/EOL/blob/main/service_list.json contains an array of objects, each representing a service or feature in Azure that is scheduled for retirement. Each object includes the following properties:

- Id: A unique identifier for the retiring feature.
- ServiceName: The name of the Azure service.
- RetiringFeature: The specific feature or service that is being retired.
- RetirementDate: The date when the feature or service will be retired.
- Link: A URL to more information about the retirement announcement.

```json
  {
    "Id": 583,
    "ServiceName": "Azure Synapse",
    "RetiringFeature": "Runtime for Apache Spark 3.2",
    "RetirementDate": "2024-07-08",
    "Link": "https://azure.microsoft.com/updates/azure-synapse-runtime-for-apache-spark-32-end-of-support/"
  }
```

## Azure Resource Graph Query Overview
This Azure Resource Graph query found at https://github.com/Azure/EOL/blob/main/resource_list.kql is designed to identify Azure resources that are associated with specific retiring features or services. 
The JSON data contains information about retiring Azure services and features, including their ServiceName, RetiringFeature, RetirementDate, and Link. The ServiceID in the JSON data corresponds to the ServiceID used in the Azure Resource Graph query.

- For example, the JSON entry for "Azure Synapse" with ServiceID 583 and retiring feature "Runtime for Apache Spark 3.2" is directly referenced in the query with ServiceID 583.
- The query identifies resources that match these retiring features and assigns the corresponding ServiceID to them.

```kql
| where ('*' in (dynamic(['*'])) or resourceGroup in (dynamic(['*']))) 
| where ('*' in (dynamic(['*'])) or location in (dynamic(['*'])))
| where array_length(dynamic([583])) == 0 or ServiceID in (dynamic([583]))
```
