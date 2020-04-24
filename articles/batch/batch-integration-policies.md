---
title: Integratie met Azure-beleid
description: Azure Policy is een service in azure die u gebruikt om beleids regels te maken, toe te wijzen en te beheren waarmee u regels voor uw resources afdwingt.
ms.topic: article
ms.date: 02/24/2020
ms.custom: seodec18
ms.openlocfilehash: 74c087031a024b0c50d6c40057a4e2eda554be6f
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82116074"
---
# <a name="integration-with-azure-policy"></a>Integratie met Azure Policy

Azure Policy is een service in azure die u gebruikt om beleid te maken, toe te wijzen en te beheren waarmee regels worden afgedwongen voor uw resources om ervoor te zorgen dat deze resources compatibel blijven met uw bedrijfs standaarden en service overeenkomsten. Azure Policy evalueert uw resources voor niet-naleving van de beleids regels die u toewijst. 

Azure Batch heeft twee ingebouwde uitbrei dingen die u helpen bij het beheren van beleids naleving. 

|**Naam**...|   **Beschrijving**|**Effect (s)**|  **Versie**|    **Bron**
|----------------|----------|----------|----------------|---------------|
|Diagnostische logboeken in batch-accounts moeten worden ingeschakeld|   Het inschakelen van Diagnostische logboeken controleren. Zo kunt u een activiteit spoor opnieuw maken om te gebruiken voor onderzoek doeleinden; Wanneer er een beveiligings incident optreedt of wanneer uw netwerk is aangetast|AuditIfNotExists, uitgeschakeld|  2.0.0|  GitHub|
|Metrische waarschuwings regels moeten worden geconfigureerd voor batch-accounts| De configuratie van regels voor metrische waarschuwingen voor een batch-account controleren om de vereiste metriek in te scha kelen|   AuditIfNotExists, uitgeschakeld| 1.0.0|  GitHub|

Met beleids definities worden de voor waarden beschreven waaraan moet worden voldaan. Met een voor waarde wordt de resource-eigenschap vergeleken met een vereiste waarde. Resource-eigenschaps velden worden geopend met vooraf gedefinieerde aliassen. U kunt eigenschaps aliassen gebruiken om toegang te krijgen tot specifieke eigenschappen voor een bron type. Met aliassen kunt u bepalen welke waarden of voor waarden zijn toegestaan voor een eigenschap van een resource. Elke alias wordt toegewezen aan paden in verschillende API-versies voor een bepaald bron type. Tijdens de beleids evaluatie haalt de beleids engine het pad naar de eigenschap voor die API-versie op.

De resources die worden vereist door batch, zijn onder andere: account, reken knooppunt, pool, taak en taak. U kunt daarom eigenschaps aliassen gebruiken om toegang te krijgen tot specifieke eigenschappen voor deze resources. Meer informatie over [aliassen](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

Gebruik de Azure-beleids extensie voor Visual Studio code om ervoor te zorgen dat u de huidige aliassen kent en uw resources en beleid controleert. Het kan worden geïnstalleerd op alle platforms die worden ondersteund door Visual Studio code. Deze ondersteuning omvat Windows, Linux en macOS. Zie [installatie richtlijnen](https://docs.microsoft.com/azure/governance/policy/how-to/extension-for-vscode).



