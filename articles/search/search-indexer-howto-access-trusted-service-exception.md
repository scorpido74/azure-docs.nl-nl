---
title: Toegang tot opslag via een vertrouwde service uitzondering toestaan
titleSuffix: Azure Cognitive Search
description: Instructies voor het instellen van een vertrouwde service-uitzonde ring voor het veilig benaderen van gegevens van opslag accounts.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 1400f3c3d15698a5f1a145e8e0750ad7c4e9cec8
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90971418"
---
# <a name="accessing-data-in-storage-accounts-securely-via-trusted-service-exception"></a>Beveiligde toegang tot gegevens in opslag accounts via een vertrouwde service-uitzonde ring

Indexeer functies die toegang hebben tot gegevens in opslag accounts, kunnen gebruikmaken van de mogelijkheid van een [vertrouwde service-uitzonde ring](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) om veilig toegang te krijgen tot gegevens. Dit mechanisme biedt klanten die geen toegang tot de [Indexeer functie kunnen verlenen via IP-firewall regels](search-indexer-howto-access-ip-restricted.md) , een eenvoudig, veilig en gratis alternatief voor toegang tot gegevens in opslag accounts.

## <a name="step-1-configure-connection-to-the-storage-account-via-identity"></a>Stap 1: de verbinding met het opslag account configureren via de identiteit

Volg de details die worden beschreven in [de hand leiding Managed Identity Access](search-howto-managed-identities-storage.md) om Indexeer functies te configureren voor toegang tot opslag accounts via de beheerde identiteit van de zoek service.

## <a name="step-2-allow-trusted-microsoft-services-to-access-the-storage-account"></a>Stap 2: vertrouwde micro soft-Services toegang geven tot het opslag account

Ga in het Azure Portal naar het tabblad firewalls en virtuele netwerken van het opslag account. Zorg ervoor dat de optie ' vertrouwde micro soft-Services toegang geven tot dit opslag account ' is ingeschakeld. Met deze optie wordt alleen het specifieke exemplaar van de zoek service met de juiste op rollen gebaseerde toegang tot het opslag account (sterke verificatie) toegestaan om toegang te krijgen tot gegevens in het opslag account, zelfs als deze worden beveiligd door de IP-firewall regels.

![Uitzonde ring voor vertrouwde services](media\search-indexer-howto-secure-access\exception.png "Uitzonde ring voor vertrouwde services")

Indexeer functies kunnen nu toegang krijgen tot gegevens in het opslag account, zelfs als het account is beveiligd via IP-firewall regels.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over Azure Storage Indexeer functies:

- [Indexer van Azure Blob](search-howto-indexing-azure-blob-storage.md)
- [Indexeer functie Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md)
- [Indexeer functie van Azure-tabel](search-howto-indexing-azure-tables.md)
