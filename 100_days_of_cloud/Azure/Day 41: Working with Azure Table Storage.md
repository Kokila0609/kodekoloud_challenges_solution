# Azure Table Storage Configuration Guide

The Nautilus DevOps team is developing a simple 'To-Do' application using Azure Table Storage to store and manage tasks efficiently. This guide outlines the steps required to provision the storage table and insert the initial application tasks.

## Objectives

* Create an Azure Storage Account named `nautilustablest25829` containing a Table Storage table named `tasks`.
* Insert two predefined tasks with unique `RowKey` values, descriptions, and statuses.
* Verify task completion states.

---

## Pre-requisites & References

For comprehensive details on Azure CLI commands used in this guide, see the official Microsoft documentation:
* [az storage table create Reference](https://learn.microsoft.com/en-us/cli/azure/storage/table?view=azure-cli-latest#az-storage-table-create)
* [az storage entity insert Reference](https://learn.microsoft.com/en-us/cli/azure/storage/entity?view=azure-cli-latest#az-storage-entity-insert)
* [Azure Table Storage Quickstart](https://learn.microsoft.com/en-us/azure/storage/tables/table-storage-quickstart-portal)

---

## Step-by-Step Execution

### 1. Create the Storage Table
Run the following command to provision the `tasks` table within your storage account.

```bash
az storage table create \
  --name tasks \
  --account-name nautilustablest25829
```

### 2. Insert Tasks Into the Table

#### Insert Task 1
Add the first task entity with a status of `completed`.

```bash
az storage entity insert \
  --table-name "tasks" \
  --account-name "nautilustablest25829" \
  --entity PartitionKey="tasks" RowKey="1" description="Learn Table Storage" status="completed"
```

#### Insert Task 2
Add the second task entity with a status of `in-progress`.

```bash
az storage entity insert \
  --table-name "tasks" \
  --account-name "nautilustablest25829" \
  --entity PartitionKey="tasks" RowKey="2" description="Build To-Do App" status="in-progress"
```

---

### 3. Verification
To verify that the tasks were inserted correctly and match their respective statuses, query the table data using the following command:

```bash
az storage entity query \
  --table-name "tasks" \
  --account-name "nautilustablest25829" \
  --output table
```
