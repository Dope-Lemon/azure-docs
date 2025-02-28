---
title: How to bind an Azure Database for PostgreSQL to your application in Azure Spring Apps
description: Learn how to bind an Azure Database for PostgreSQL instance to your application in Azure Spring Apps.
author: KarlErickson
ms.service: spring-apps
ms.topic: how-to
ms.date: 09/26/2022
ms.author: karler
ms.custom: passwordless-java, devx-track-java
---

# Bind an Azure Database for PostgreSQL to your application in Azure Spring Apps

> [!NOTE]
> Azure Spring Apps is the new name for the Azure Spring Cloud service. Although the service has a new name, you'll see the old name in some places for a while as we work to update assets such as screenshots, videos, and diagrams.

**This article applies to:** ✔️ Java ❌ C#

**This article applies to:** ✔️ Basic/Standard tier ✔️ Enterprise tier

With Azure Spring Apps, you can bind select Azure services to your applications automatically, instead of having to configure your Spring Boot application manually. This article shows you how to bind your application to your Azure Database for PostgreSQL instance.

## Prerequisites

* An application deployed to Azure Spring Apps. For more information, see [Quickstart: Deploy your first application to Azure Spring Apps](./quickstart.md).
* An Azure Database for PostgreSQL Flexible Server instance.
* [Azure CLI](/cli/azure/install-azure-cli) version 2.45.0 or higher.

## Prepare your Java project

Use the following steps to prepare your project.

1. In your project's *pom.xml* file, add the following dependency:

   ```xml
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-data-jpa</artifactId>
   </dependency>
   <dependency>
       <groupId>com.azure.spring</groupId>
       <artifactId>spring-cloud-azure-starter-jdbc-postgresql</artifactId>
   </dependency>
   ```

1. In the *application.properties* file, remove any `spring.datasource.*` properties.

1. Update the current app by running `az spring app deploy`, or create a new deployment for this change by running `az spring app deployment create`.

## Bind your app to the Azure Database for PostgreSQL instance

### [Service Connector for flexible server](#tab/Passwordlessflex)

1. Install the [Service Connector](../service-connector/overview.md) passwordless extension for the Azure CLI:

   ```azurecli
   az extension add --name serviceconnector-passwordless --upgrade
   ```

1. Configure Azure Spring Apps to connect to the PostgreSQL Database with a system-assigned managed identity using the `az spring connection create` command.

   ```azurecli
   az spring connection create postgres-flexible \
       --resource-group $AZ_SPRING_APPS_RESOURCE_GROUP \
       --service $AZ_SPRING_APPS_SERVICE_INSTANCE_NAME \
       --app $APP_NAME \
       --deployment $DEPLOYMENT_NAME \
       --target-resource-group $POSTGRES_RESOURCE_GROUP \
       --server $POSTGRES_SERVER_NAME \
       --database $DATABASE_NAME \
       --system-identity
   ```

### [Service Connector for single server](#tab/Passwordlesssingle)

1. Install the [Service Connector](../service-connector/overview.md) passwordless extension for the Azure CLI:

   ```azurecli
   az extension add --name serviceconnector-passwordless --upgrade
   ```

1. Configure Azure Spring Apps to connect to the PostgreSQL Database with a system-assigned managed identity using the `az spring connection create` command.

   ```azurecli
   az spring connection create postgres \
       --resource-group $AZ_SPRING_APPS_RESOURCE_GROUP \
       --service $AZ_SPRING_APPS_SERVICE_INSTANCE_NAME \
       --app $APP_NAME \
       --deployment $DEPLOYMENT_NAME \
       --target-resource-group $POSTGRES_RESOURCE_GROUP \
       --server $POSTGRES_SERVER_NAME \
       --database $DATABASE_NAME \
       --system-identity
   ```

### [Service Connector with password](#tab/Secrets)

Use the following steps to bind your app using a secret.

1. Note the admin username and password of your Azure Database for PostgreSQL account.

1. Connect to the server, create a database named **testdb** from a PostgreSQL client, and then create a new non-admin account.

1. Run the following command to connect to the database with admin username and password.

   ```azurecli
   az spring connection create postgres \
       --resource-group $AZ_SPRING_APPS_RESOURCE_GROUP \
       --service $AZ_SPRING_APPS_SERVICE_INSTANCE_NAME \
       --app $APP_NAME \
       --deployment $DEPLOYMENT_NAME \
       --target-resource-group $POSTGRES_RESOURCE_GROUP \
       --server $POSTGRES_SERVER_NAME \
       --database $DATABASE_NAME \
       --secret name=$USERNAME secret=$PASSWORD
   ```

---

## Next steps

In this article, you learned how to bind an application in Azure Spring Apps to an Azure Database for PostgreSQL instance. To learn more about binding services to an application, see [Bind an Azure Cosmos DB database to an application in Azure Spring Apps](./how-to-bind-cosmos.md).
