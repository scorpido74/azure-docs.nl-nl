---
title: Voorbeeldsjablonen van Azure Resource Manager - App Service | Microsoft Docs
description: Voorbeeldsjablonen van Azure Resource Manager voor App Service
services: app-service
documentationcenter: app-service
author: tfitzmac
tags: azure-service-management
ms.service: app-service
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 01/04/2019
ms.author: tomfitz
ms.custom: mvc
ms.openlocfilehash: 83bb357544d9069da1c86f583eaca5a470953ce8
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70066475"
---
# <a name="azure-resource-manager-templates-for-app-service"></a>Azure Resource Manager-sjablonen voor App Service

De volgende tabel bevat koppelingen naar Azure Resource Manager-sjablonen voor Azure App Service. Zie [Hulp bij het implementeren van apps met Azure Resource Manager-sjablonen](deploy-resource-manager-template.md) voor aanbevelingen om veelvoorkomende fouten bij het maken van app-sjablonen te voorkomen.

Zie [Microsoft.Web resource types](/azure/templates/microsoft.web/allversions) (Microsoft.Web-resourcetypen) voor informatie over de JSON-syntaxis en eigenschappen.

| | |
|-|-|
|**Een app implementeren**||
| [Een App Service-plan en een eenvoudige Linux-app](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-linux) | Hiermee wordt een App Service-app geïmplementeerd die is geconfigureerd voor Linux. |
| [Een App Service-plan en een eenvoudige Windows-app](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-windows) | Hiermee wordt een App Service-app geïmplementeerd die is geconfigureerd voor Windows. |
| [Een app die is gekoppeld aan een GitHub-opslagplaats](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-github-deploy)| Hiermee wordt een App Service-app geïmplementeerd die code uit GitHub haalt. |
| [App met aangepaste implementatiesites](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-custom-deployment-slots)| Hiermee wordt een App Service-app met aangepaste implementatiesites/-omgevingen geïmplementeerd. |
|**App configureren**||
| [App-certificaat uit Key Vault](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault)| Hiermee wordt een App Service-app-certificaat uit het Azure Key Vault-geheim geïmplementeerd en gebruikt voor SSL-binding. |
| [App met aangepast domein](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain)| Hiermee wordt een App Service-app met een aangepaste hostnaam geïmplementeerd. |
| [App met aangepast domein en SSL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain-and-ssl)| Hiermee wordt een App Service-app met een aangepaste hostnaam geïmplementeerd en wordt een app-certificaat uit Key Vault opgehaald voor SSL-binding. |
| [App met een GoLang-extensie](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-with-golang)| Hiermee wordt een App Service-app met de Golang-site-extensie geïmplementeerd. Daarna kunt u webtoepassingen uitvoeren die zijn ontwikkeld in Golang in Azure. |
| [App met Java 8 en Tomcat 8](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-java-tomcat)| Hiermee wordt een App Service-app geïmplementeerd waarvoor Java 8 en Tomcat 8 zijn ingeschakeld. Daarna kunt u Java-toepassingen uitvoeren in Azure. |
|**App met verbonden resources**||
| [App in Linux met MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-mysql) | Hiermee wordt een App Service-app geïmplementeerd in Linux met Azure Database for MySQL. |
| [App in Linux met PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-postgresql) | Hiermee wordt een App Service-app geïmplementeerd in Linux met Azure Database for PostgreSQL. |
|**App met verbonden resources**||
| [App met MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-mysql)| Hiermee wordt een App Service-app geïmplementeerd in Windows met Azure Database for MySQL. |
| [App met PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-postgresql)| Hiermee wordt een App Service-app geïmplementeerd in Windows met Azure Database for PostgreSQL. |
| [App met een SQL-database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)| Hiermee worden een App Service-app en een SQL-database geïmplementeerd op serviceniveau Basic. |
| [App met een Blobopslag-verbinding](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-blob-connection)| Hiermee wordt een App Service-app geïmplementeerd met een verbindingsreeks voor Azure Blobopslag. Daarna kunt u Blobopslag gebruiken vanuit de app. |
| [App met Azure Cache voor Redis](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-redis-cache)| Hiermee wordt een App Service-app met een Azure Cache voor Redis geïmplementeerd. |
|**App Service Environment voor PowerApps**||
| [Create an App Service environment v2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-create)(Een App Service-omgeving v2 maken) | Hiermee wordt een App Service-omgeving v2 in uw virtuele netwerk gemaakt. |
| [Create an App Service environment v2 with an ILB Address](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-ilb-create/) (Een App Service-omgeving v2 met een ILB-adres (interne load balancer) maken) | Hiermee wordt een App Service-omgeving v2 in uw virtuele netwerk gemaakt met een privéadres voor de interne load balancer. |
| [Configure the default SSL certificate for an ILB App Service environment or an ILB App Service environment v2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-ase-ilb-configure-default-ssl) (Het standaard-SSL-certificaat configureren voor een ILB App Service-omgeving of een ILB App Service-omgeving v2) | Hiermee wordt het standaard-SSL-certificaat geconfigureerd voor een ILB App Service-omgeving of een ILB App Service-omgeving v2. |
| | |
