---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/12/2020
ms.author: dacoulte
ms.openlocfilehash: 7b2179c0a924f7fc29aa70ff748d435e664980ae
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77193129"
---
|Naam |Beschrijving |Effect (s) |Version |
|---|---|---|---|
|[Diagnostische instellingen voor Key Vault implementeren in Event hub](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_DiagnosticLog_Deploy.json) |Hiermee worden de diagnostische instellingen voor de Key Vault geïmplementeerd om te streamen naar een regionale Event hub wanneer een Key Vault waarvoor deze diagnostische instellingen ontbreken, wordt gemaakt of bijgewerkt. |deployIfNotExists |1.0.0 |
|[Diagnostische logboeken in Key Vault moeten worden ingeschakeld](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |Het inschakelen van Diagnostische logboeken controleren. Zo kunt u een activiteit spoor opnieuw maken om te gebruiken voor onderzoek doeleinden; Wanneer er een beveiligings incident optreedt of wanneer uw netwerk is aangetast |AuditIfNotExists, uitgeschakeld |1.0.0 |
|[Key Vault objecten moeten worden hersteld](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |Dit beleid controleert of de sleutel kluis objecten niet kunnen worden hersteld. De functie voor voorlopig verwijderen helpt bij het effectief vasthouden van de resources voor een bepaalde Bewaar periode (90 dagen), zelfs na het verwijderen van een bewerking, terwijl het uiterlijk van het object wordt verwijderd. Als ' beveiliging opschonen ' is ingeschakeld, kan een kluis of een object in de verwijderde status pas worden verwijderd als de retentie periode van 90 dagen is verstreken. Deze kluizen en objecten kunnen nog steeds worden hersteld, waardoor klanten er zeker van zijn dat het Bewaar beleid wordt gevolgd. |Controle, uitgeschakeld |1.0.0 |
