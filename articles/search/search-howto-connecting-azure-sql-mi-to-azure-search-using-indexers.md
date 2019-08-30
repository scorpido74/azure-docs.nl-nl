---
title: Azure SQL Managed instance-verbinding voor zoek indexen-Azure Search
description: Schakel openbaar eind punt in om verbindingen met SQL Managed instances van een Indexeer functie op Azure Search toe te staan.
author: vl8163264128
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: victliu
ms.openlocfilehash: 07bc1a55c0222fda87b28acbaa8bfe552fb8e6ed
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70186673"
---
# <a name="configure-a-connection-from-an-azure-search-indexer-to-sql-managed-instance"></a>Een verbinding van een Azure Search Indexeer functie configureren voor een beheerd exemplaar van SQL
Zoals beschreven in het [verbinden van Azure SQL database naar Azure Search met Indexeer functies](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq), wordt het maken van Indexeer functies voor **SQL Managed instances** ondersteund door Azure Search via het open bare eind punt.

## <a name="create-azure-sql-managed-instance-with-public-endpoint"></a>Een door Azure SQL beheerd exemplaar maken met een openbaar eind punt
Maak een SQL-beheerd exemplaar met de optie **openbaar eind punt inschakelen** geselecteerd.

   ![Openbaar eind punt inschakelen](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/enable-public-endpoint.png "Openbaar eind punt inschakelen")

## <a name="enable-azure-sql-managed-instance-public-endpoint"></a>Open bare eind punt van Azure SQL Managed instance inschakelen
U kunt ook open bare eind punten inschakelen op een bestaand exemplaarvan SQL Managed in het**open bare endpoint** > voor **beveiliging** > van het**virtuele netwerk** > .

   ![Openbaar eind punt inschakelen](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-vnet.png "Openbaar eind punt inschakelen")

## <a name="verify-nsg-rules"></a>NSG-regels controleren
Controleer of de netwerk beveiligings groep de juiste **regels voor binnenkomende beveiliging** heeft waarmee verbindingen van Azure-Services zijn toegestaan.

   ![Binnenkomende beveiligings regel NSG](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/nsg-rule.png "Binnenkomende beveiligings regel NSG")

## <a name="get-public-endpoint-connection-string"></a>connection string van open bare eind punten ophalen
Zorg ervoor dat u de connection string gebruikt voor het **open bare eind punt** (poort 3342, niet poort 1433).

   ![Connection String van open bare eind punten](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-connection-string.png "Connection String van open bare eind punten")

## <a name="next-steps"></a>Volgende stappen
Met configuratie uit de weg kunt u nu een door SQL beheerd exemplaar opgeven als de gegevens bron voor een Azure Search Indexeer functie met behulp van de portal of REST API. Zie [Azure SQL database verbinding maken met Azure Search met Indexeer functies](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) voor meer informatie.
