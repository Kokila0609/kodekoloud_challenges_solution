# Event Hubs Logging Guide

# Requirement


The Nautilus DevOps team wants to integrate an Azure Virtual Machine with Azure Event Hubs for centralized log collection. Follow these steps to complete the task:

Create Azure Event Hubs Namespace:

Create an Event Hubs namespace named datacenter-namespace in the East US region.
Select the Standard pricing tier. Make sure to enable Enable Auto-inflate.
Create an Event Hub:

Within the namespace, create an Event Hub named datacenter-hub.
Verify the Virtual Machine Configuration:

A VM named datacenter-vm already exists.
A Python script named send_logs.py already exists on the VM under /home/azureuser. This script is used to send logs to the Event Hub. Make sure to execute this script mutiple times.
Verify Logs:

Ensure the logs are successfully sent to the Event Hub by checking the Event Hubs metrics in the Azure portal.

Follow these steps to configure your Azure Event Hub, update the Python script, and verify log ingestion.

## Step 1: Create Infrastructure
1. Create an **Event Hubs namespace** in the Azure Portal.
2. Inside that namespace, create an **Event Hub** instance.

## Step 2: Retrieve the Connection String
1. **Find Event Hubs**: Search for and select **Event Hubs** from the main menu.
2. **Select Your Namespace**: Click on the name of your Event Hubs namespace.
3. **Go to Policies**: In the left menu, click **Shared access policies** under the **Settings** section.
4. **Choose a Policy**: Select an existing policy, like the default `RootManageSharedAccessKey`.
5. **Copy the Key**: Find the **Connection string–primary key** box and click the copy button next to it.

## Step 3: Configure the Script
Paste your copied connection string into the `connection_str` variable inside your `send_logs.py` script:

```python
# azureuser@datacenter-vm:~\$ cat send_logs.py 
from azure.eventhub import EventHubProducerClient, EventData

# Event Hub Configuration
connection_str = "YOUR_CONNECTION_STRING_HERE"
event_hub_name = "YOUR_EVENT_HUB_NAME"

# Initialize the producer client
producer = EventHubProducerClient.from_connection_string(
    conn_str=connection_str,
    eventhub_name=event_hub_name
)

# Send logs to the Event Hub
with producer:
    for i in range(10):
        event_data_batch = producer.create_batch()
        event_data_batch.add(EventData(f"Log entry {i + 1}: Sample log message"))
        producer.send_batch(event_data_batch)
        print(f"Log entry {i + 1} sent.")
```

## Step 4: Execute the Script
Run the script from your terminal to stream the logs:

```bash
azureuser@datacenter-vm:~\$ python3 send_logs.py
Log entry 1 sent.
Log entry 2 sent.
Log entry 3 sent.
Log entry 4 sent.
Log entry 5 sent.
Log entry 6 sent.
Log entry 7 sent.
Log entry 8 sent.
Log entry 9 sent.
Log entry 10 sent.
```

## Step 5: Verify Logs in Event Hub
1. Navigate to your specific **Event Hub** instance inside the Azure Portal namespace.
2. Check the **Overview** dashboard to see the real-time **Messages** chart. 
3. Verify that the **Incoming Messages** metric spike matches your 10 sent entries.

### Links:

https://learn.microsoft.com/en-us/azure/event-hubs/event-hubs-create

https://learn.microsoft.com/en-us/azure/event-hubs/event-hubs-get-connection-string
