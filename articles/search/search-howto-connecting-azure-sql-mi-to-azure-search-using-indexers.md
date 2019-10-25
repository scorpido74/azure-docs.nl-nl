---
title: Azure SQL Managed instance-verbinding voor zoek indexering
titleSuffix: Azure Cognitive Search
description: Schakel openbaar eind punt in om verbindingen met SQL Managed instances toe te staan vanuit een Indexeer functie op Azure Cognitive Search.
manager: nitinme
author: vl8163264128
ms.author: victliu
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 16daf4a79252134703715ccd88f0b10dda7f4fa6
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792156"
---
# <a name="configure-a-connection-from-an-azure-cognitive-search-indexer-to-sql-managed-instance"></a>Een verbinding van een Azure Cognitive Search Indexeer functie configureren in een door SQL beheerd exemplaar

Zoals beschreven in het [verbinden van Azure SQL database met Azure Cognitive Search met behulp van Indexeer functies](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq), het maken van Indexeer functies voor **SQL Managed instances** wordt door Azure Cognitive Search via het open bare eind punt ondersteund.

## <a name="create-azure-sql-managed-instance-with-public-endpoint"></a>Een door Azure SQL beheerd exemplaar maken met een openbaar eind punt
Maak een SQL-beheerd exemplaar met de optie **openbaar eind punt inschakelen** geselecteerd.

   ![Openbaar eind punt inschakelen](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/enable-public-endpoint.png "Openbaar eind punt inschakelen")

## <a name="enable-azure-sql-managed-instance-public-endpoint"></a>Open bare eind punt van Azure SQL Managed instance inschakelen
U kunt ook open bare eind punten inschakelen op een bestaand SQL beheerd exemplaar onder **beveiliging** > **virtueel netwerk** > **open bare eind punt** > **in te scha kelen**.

   ![Openbaar eind punt inschakelen](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-vnet.png "Openbaar eind punt inschakelen")

## <a name="verify-nsg-rules"></a>NSG-regels controleren
Controleer of de netwerk beveiligings groep de juiste **regels voor binnenkomende beveiliging** heeft waarmee verbindingen van Azure-Services zijn toegestaan.

   ![Binnenkomende beveiligings regel NSG](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/nsg-rule.png "Binnenkomende beveiligings regel NSG")

## <a name="get-public-endpoint-connection-string"></a>connection string van open bare eind punten ophalen
Zorg ervoor dat u de connection string gebruikt voor het **open bare eind punt** (poort 3342, niet poort 1433).

   ![connection string van open bare eind punten](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-connection-string.png "connection string van open bare eind punten")

## <a name="next-steps"></a>Volgende stappen
Met configuratie uit de weg kunt u nu een door SQL beheerd exemplaar opgeven als de gegevens bron voor een Azure Cognitive Search Indexeer functie met behulp van de portal of REST API. Zie [verbinding maken tussen Azure SQL database en Azure Cognitive Search met behulp van Indexeer functies](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) voor meer informatie.
