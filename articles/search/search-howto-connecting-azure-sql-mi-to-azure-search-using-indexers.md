---
title: Azure SQL Managed Instance-verbinding voor zoeken indexering
titleSuffix: Azure Cognitive Search
description: Public Endpoint inschakelen om verbindingen met SQL Managed Instances toe te staan vanuit een indexer op Azure Cognitive Search.
manager: nitinme
author: vl8163264128
ms.author: victliu
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 65e483fd772e20daa73b465ea17dfa6ecde42233
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76964886"
---
# <a name="configure-a-connection-from-an-azure-cognitive-search-indexer-to-sql-managed-instance"></a>Een verbinding configureren van een Azure Cognitive Search-indexer naar SQL Managed Instance

Zoals opgemerkt in [Het verbinden van Azure SQL Database met Azure Cognitive Search met behulp van indexeerders,](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq)wordt het maken van indexers tegen **SQL Managed Instances** ondersteund door Azure Cognitive Search via het openbare eindpunt.

## <a name="create-azure-sql-managed-instance-with-public-endpoint"></a>Azure SQL Managed Instance maken met openbaar eindpunt
Maak een SQL Managed Instance met de optie **Openbaar eindpunt inschakelen** geselecteerd.

   ![Openbaar eindpunt inschakelen](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/enable-public-endpoint.png "Openbaar eindpunt inschakelen")

## <a name="enable-azure-sql-managed-instance-public-endpoint"></a>Openbaar eindpunt azure SQL Managed Instance inschakelen
U ook openbaar eindpunt inschakelen voor een bestaand SQL Managed Instance onder Public**endpoint** > Enable **security** > **Virtual Network.** > **Enable**

   ![Openbaar eindpunt inschakelen](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-vnet.png "Openbaar eindpunt inschakelen")

## <a name="verify-nsg-rules"></a>NSG-regels verifiëren
Controleer of de netwerkbeveiligingsgroep de juiste **binnenkomende beveiligingsregels** heeft waarmee verbindingen van Azure-services kunnen worden gemaakt.

   ![NSG Inbound security rule](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/nsg-rule.png "NSG Inbound security rule")

> [!NOTE]
> Indexers vereisen nog steeds dat SQL Managed Instance wordt geconfigureerd met een openbaar eindpunt om gegevens te kunnen lezen.
> U er echter voor kiezen om de binnenkomende toegang tot`public_endpoint_inbound`dat openbare eindpunt te beperken door de huidige regel ( ) te vervangen door de volgende 2 regels:
>
> * Binnenkomende toegang toestaan `AzureCognitiveSearch` vanaf de [servicetag](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) `AzureCognitiveSearch`("BRON" `cognitive_search_inbound`= , "NAAM" = )
>
> * Toegang tot binnenkomende toegang toestaan vanaf het IP-adres van de zoekservice, die kan `<your-search-service-name>.search.windows.net`worden verkregen door de volledig gekwalificeerde domeinnaam te pingen (bijv., ). ("BRON" `IP address`= , "NAAM" = `search_service_inbound`)
>
> Voor elk van deze 2 regels, `3342`stel "HAVEN" = , "PROTOCOL" = `TCP`, "BESTEMMING" = `Any`, "ACTIE" =`Allow`

## <a name="get-public-endpoint-connection-string"></a>Verbindingstekenreeks voor openbare eindpunten opteraken
Zorg ervoor dat u de verbindingstekenreeks gebruikt voor het **openbare eindpunt** (poort 3342, niet poort 1433).

   ![Tekenreeks openbare eindpuntverbinding](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-connection-string.png "Tekenreeks openbare eindpuntverbinding")

## <a name="next-steps"></a>Volgende stappen
Als configuratie uit de weg is, u nu een SQL Managed Instance opgeven als de gegevensbron voor een Azure Cognitive Search-indexer met behulp van de portal of REST API. Zie [Azure SQL Database verbinden met Azure Cognitive Search met behulp van indexeerders](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) voor meer informatie.
