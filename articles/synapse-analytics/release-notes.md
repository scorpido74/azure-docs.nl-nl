---
title: Releaseopmerkingen
description: Releaseopmerkingen voor Azure Synapse Analytics (werkruimten)
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 514694dc2e3f06db2fb80f6b3ba0106343be11d8
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83658499"
---
# <a name="azure-synapse-analytics-preview-release-notes"></a>Azure Synapse Analytics-releaseopmerkingen (preview)

In dit artikel worden beperkingen en problemen in Azure Synapse Analytics (werkruimten) beschreven. Raadpleeg [Wat is Azure Synapse Analytics (werkruimten)?](overview-what-is.md) voor gerelateerde informatie

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="azure-synapse-workspaces"></a>Azure Synapse (werkruimten) 

### <a name="azure-synapse-cli"></a>Azure Synapse CLI

- Probleem en gevolgen voor de klant: Synapse Studio kan niet worden gestart in werkruimten die zijn gemaakt met SDK

- Tijdelijke oplossing: Voltooi de volgende stappen: 
  1.    Maak een werkruimte door `az synapse workspace create` uit te voeren.
  2.    Haal de id van de beheerde identiteit op door `$identity=$(az synapse workspace show --name {workspace name}  --resource-group {resource group name} --query "identity.principalId")` uit te voeren.
  3.    Voeg de werkruimte als rol toe aan het opslagaccount door ` az role assignment create --role "Storage Blob Data Contributor" --assignee-object-id {identity } --scope {storage account resource id}` uit te voeren.
  4.    Voeg een firewallregel toe door ` az synapse firewall-rule create --name allowAll --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255 ` uit te voeren.

## <a name="next-steps"></a>Volgende stappen

* [Een werkruimte maken](quickstart-create-workspace.md)
* [Synapse Studio gebruiken](quickstart-synapse-studio.md)
* [Een SQL-pool maken](quickstart-create-sql-pool-portal.md)
* [SQL on-demand gebruiken](quickstart-sql-on-demand.md)
* [Een Apache Spark-pool maken](quickstart-create-apache-spark-pool-portal.md)