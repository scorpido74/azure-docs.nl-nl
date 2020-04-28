---
title: Releaseopmerkingen
description: Release opmerkingen voor Azure Synapse Analytics (werk ruimten)
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 059e77c063d00ef850a171507ca2e06422ade426
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82191767"
---
# <a name="azure-synapse-analytics-preview-release-notes"></a>Release opmerkingen bij Azure Synapse Analytics (preview)

In dit artikel worden de beperkingen en problemen met Azure Synapse Analytics (werk ruimten) beschreven. Zie [Wat is Azure Synapse Analytics (werk ruimten)](overview-what-is.md) voor meer informatie.

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="azure-synapse-workspaces"></a>Azure-Synapse (werk ruimten) 

### <a name="azure-synapse-cli"></a>Azure Synapse CLI

- Probleem en gevolgen voor de klant: werk ruimten die zijn gemaakt met SDK kunnen Synapse Studio niet starten

- Tijdelijke oplossing: Voer de volgende stappen uit: 
  1.    Werk ruimte maken door `az synapse workspace create`uit te voeren.
  2.    Haal de beheerde identiteits- `$identity=$(az synapse workspace show --name {workspace name}  --resource-group {resource group name} --query "identity.principalId")`id op door uit te voeren.
  3.    Voeg werk ruimte als rol toe aan het opslag ` az role assignment create --role "Storage Blob Data Contributor" --assignee-object-id {identity } --scope {storage account resource id}`account door uit te voeren.
  4.    Firewall regel toevoegen door uit ` az synapse firewall-rule create --name allowAll --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255 `te voeren.

## <a name="next-steps"></a>Volgende stappen

* [Een werkruimte maken](quickstart-create-workspace.md)
* [Synapse Studio gebruiken](quickstart-synapse-studio.md)
* [Een SQL-groep maken](quickstart-create-sql-pool.md)
* [SQL on-demand gebruiken](quickstart-sql-on-demand.md)
* [Een Apache Spark groep maken](quickstart-create-apache-spark-pool.md)