---
title: 'Releaseopmerkingen: Azure Synapse Analytics (werkruimten)'
description: Releaseopmerkingen voor Azure Synapse Analytics (werkruimten)
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: add5c89e83f33980803bf571239023859653c4f1
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87059611"
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