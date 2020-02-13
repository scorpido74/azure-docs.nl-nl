---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 53494bc9642a3df91492d2353a6aa3c6875c1fff
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172770"
---
|Naam |Beschrijving |Effect (s) |Version |
|---|---|---|---|
|[Diagnostische instellingen voor Key Vault implementeren in Event hub](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Key%20Vault/KeyVault_DiagnosticLog_Deploy.json) |Hiermee worden de diagnostische instellingen voor de Key Vault geïmplementeerd om te streamen naar een regionale Event hub wanneer een Key Vault waarvoor deze diagnostische instellingen ontbreken, wordt gemaakt of bijgewerkt. |deployIfNotExists |1.0.0 |
|[Diagnostische logboeken in Key Vault moeten worden ingeschakeld](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |Het inschakelen van Diagnostische logboeken controleren. Zo kunt u een activiteit spoor opnieuw maken om te gebruiken voor onderzoek doeleinden; Wanneer er een beveiligings incident optreedt of wanneer uw netwerk is aangetast |AuditIfNotExists, uitgeschakeld |1.0.0 |
|[Key Vault objecten moeten worden hersteld](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |Dit beleid controleert of de sleutel kluis objecten niet kunnen worden hersteld. De functie voor voorlopig verwijderen helpt bij het effectief vasthouden van de resources voor een bepaalde Bewaar periode (90 dagen), zelfs na het verwijderen van een bewerking, terwijl het uiterlijk van het object wordt verwijderd. Als ' beveiliging opschonen ' is ingeschakeld, kan een kluis of een object in de verwijderde status pas worden verwijderd als de retentie periode van 90 dagen is verstreken. Deze kluizen en objecten kunnen nog steeds worden hersteld, waardoor klanten er zeker van zijn dat het Bewaar beleid wordt gevolgd. |Controle, uitgeschakeld |1.0.0 |
