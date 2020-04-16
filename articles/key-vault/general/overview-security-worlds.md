---
title: Azure Key Vault-beveiligingswerelden | Microsoft Documenten
description: Azure Key Vault is een multi-tenant service. Het maakt gebruik van een pool van HSM's in elke Azure-locatie. Alle locaties in een geografische regio delen een cryptografische grens.
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 07/03/2017
ms.openlocfilehash: 3584f83c5e1a5e83d069373395227b70c084eae9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428951"
---
# <a name="azure-key-vault-security-worlds-and-geographic-boundaries"></a>Beveiligingswerelden en geografische grenzen van Azure Key Vault

Azure Key Vault is een multi-tenantservice en maakt gebruik van een pool van Hardware Security Modules (HSM's) op elke Azure-locatie. 

Alle HSM's op Azure-locaties in dezelfde geografische regio hebben dezelfde cryptografische grens (Thales Security World). Oost-VS en West-VS delen bijvoorbeeld dezelfde beveiligingswereld omdat ze behoren tot de geolocatie van de VS. Op dezelfde manier delen alle Azure-locaties in Japan dezelfde beveiligingswereld en alle Azure-locaties in Australië, India, enzovoort. 

## <a name="backup-and-restore-behavior"></a>Back-up- en herstelgedrag

Een back-up die is genomen van een sleutelkluis op een Azure-locatie, kan worden hersteld naar een sleutelkluis op een andere Azure-locatie, zolang beide voorwaarden waar zijn:

- Beide Azure-locaties behoren tot dezelfde geografische locatie
- Beide sleutelkluizen behoren tot hetzelfde Azure-abonnement

Een back-up die bijvoorbeeld wordt genomen door een bepaald abonnement van een sleutel in een sleutelkluis in West-India, kan alleen worden hersteld naar een andere sleutelkluis in hetzelfde abonnement en geolocatie; West-India, Centraal-India of Zuid-India.

## <a name="regions-and-products"></a>Regio's en producten

- [Azure-regio's](https://azure.microsoft.com/regions/)
- [Microsoft-producten per regio](https://azure.microsoft.com/regions/services/)

Regio's worden toegewezen aan beveiligingswerelden, weergegeven als belangrijke koppen in de tabellen:

In de producten per regio artikel, bijvoorbeeld, het **tabblad Amerika** bevat OOST-VS, CENTRAL US, WEST US alle mapping naar de Regio Amerika. 

>[!NOTE]
>Een uitzondering is dat US DOD EAST en US DOD CENTRAL hun eigen veiligheidswerelden hebben. 

Op **dezelfde** manier gaan NOORD-EUROPA en WEST-EUROPA beide in kaart met de regio Europa. Hetzelfde geldt ook op het tabblad **Azië-Pacific.**



