---
title: Azure-activiteiten logboek verzamelen met Diagnostische instellingen (preview)-Azure Monitor | Microsoft Docs
description: Gebruik Diagnostische instellingen om Azure-activiteiten logboeken door te sturen naar Azure Monitor-logboeken, Azure Storage of Azure Event Hubs.
author: bwren
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
ms.author: bwren
ms.date: 02/04/2020
ms.openlocfilehash: fcdcef5d63163b24fe5de0f547dc2dde00cd674f
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77016252"
---
# <a name="update-to-azure-activity-log-collection-and-export"></a>Bijwerken naar Azure-activiteiten logboek verzamelen en exporteren
Het [Azure-activiteiten logboek](platform-logs-overview.md) is een [platform logboek](platform-logs-overview.md) dat inzicht biedt in gebeurtenissen op abonnements niveau die zich in azure hebben voorgedaan. De methode voor het verzenden van activiteiten logboek vermeldingen naar [een event hub-of opslag account](activity-log-export.md) of naar een [log Analytics werk ruimte](activity-log-collect.md) is gewijzigd voor het gebruik van [Diagnostische instellingen](diagnostic-settings.md). In dit artikel wordt het verschil tussen de methoden beschreven en wordt uitgelegd hoe u verouderde instellingen in de voor bereiding kunt wissen om de diagnostische instellingen te wijzigen.


## <a name="differences-between-methods"></a>Verschillen tussen methoden

### <a name="advantages"></a>Voordelen
Het gebruik van diagnostische instellingen heeft de volgende voor delen ten opzichte van de huidige methoden:

- Consistente methode voor het verzamelen van alle platform Logboeken.
- Verzamel activiteiten logboek over meerdere abonnementen en tenants.
- Filter verzameling om alleen logboeken voor bepaalde categorieën te verzamelen.
- Verzamel alle activiteiten logboek categorieën. Sommige categorieën worden niet verzameld met een verouderde methode.
- Snellere latentie voor opname van Logboeken. De vorige methode heeft ongeveer 15 minuten latentie terwijl Diagnostische instellingen meer dan 1 minuut toevoegen.

### <a name="considerations"></a>Overwegingen
Houd rekening met de volgende details van de verzameling activiteiten logboeken met Diagnostische instellingen voordat u deze functie inschakelt.

- De Bewaar instelling voor het verzamelen van het activiteiten logboek naar Azure Storage is verwijderd, wat betekent dat de gegevens voor onbepaalde tijd worden opgeslagen totdat u deze verwijdert.
- Op dit moment kunt u alleen een diagnostische instelling op abonnements niveau maken met behulp van de Azure Portal. Als u andere methoden wilt gebruiken, zoals Power shell of CLI, kunt u een resource manager-sjabloon maken.


### <a name="differences-in-data"></a>Verschillen in gegevens
Diagnostische instellingen verzamelen dezelfde gegevens als de vorige methoden die worden gebruikt voor het verzamelen van het activiteiten logboek met de volgende huidige verschillen:

De volgende kolommen zijn verwijderd. De vervanging voor deze kolommen heeft een andere indeling, dus u moet mogelijk logboek query's wijzigen die deze gebruiken. U ziet mogelijk nog steeds Verwijderde kolommen in het schema, maar ze worden niet gevuld met gegevens.

| Verwijderde kolom | Vervangende kolom |
|:---|:---|
| ActivityStatus    | Waarde voor ActivityStatusValue    |
| ActivitySubstatus | ActivitySubstatusValue |
| OperationName     | OperationNameValue     |
| ResourceProvider  | ResourceProviderValue  |

De volgende kolom zijn toegevoegd:

- Authorization_d
- Claims_d
- Properties_d

> [!IMPORTANT]
> In sommige gevallen kunnen de waarden in deze kolommen in hoofd letters worden getypt. Als u een query hebt die deze kolommen bevat, moet u de [operator = ~](https://docs.microsoft.com/azure/kusto/query/datatypes-string-operators) gebruiken om een niet-hoofdletter gevoelige vergelijking te maken.

## <a name="work-with-legacy-settings"></a>Werken met oude instellingen
De oude instellingen voor het verzamelen van het activiteiten logboek blijven werken als u er niet voor kiest om te vervangen door een diagnostische instelling. Gebruik de volgende methode voor het beheren van het logboek profiel voor een abonnement.

1. Selecteer in het menu **Azure monitor** van de Azure Portal het **activiteiten logboek**.
3. Klik op **Diagnostische instellingen**.

   ![Diagnostische instellingen](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. Klik op de banner paars voor de oude ervaring.

    ![Verouderde ervaring](media/diagnostic-settings-subscription/legacy-experience.png)


Raadpleeg de volgende artikelen voor meer informatie over het gebruik van de verouderde verzamelings methoden.

- [Azure-activiteiten Logboeken in Log Analytics werk ruimte in Azure Monitor verzamelen en analyseren](activity-log-collect.md)
- [Azure-activiteiten logboeken verzamelen in Azure Monitor over Azure Active Directory tenants](activity-log-collect-tenants.md)
- [Azure-activiteiten logboek exporteren naar opslag of Azure Event Hubs](activity-log-export.md)

## <a name="disable-existing-settings"></a>Bestaande instellingen uitschakelen
U moet de bestaande verzameling van de activiteit uitschakelen voordat u deze inschakelt met behulp van diagnostische instellingen. Als beide zijn ingeschakeld, kan dit leiden tot dubbele gegevens.

### <a name="disable-collection-into-log-analytics-workspace"></a>Verzameling uitschakelen in Log Analytics werk ruimte

1. Open het menu **log Analytics werk ruimten** in de Azure Portal en selecteer de werk ruimte om het activiteiten logboek te verzamelen.
2. Selecteer in de sectie **werkruimte gegevens bronnen** van het menu van de werk ruimte **Azure-activiteiten logboek**.
3. Klik op het abonnement dat u wilt loskoppelen.
4. Klik op **verbinding verbreken** en vervolgens op **Ja** wanneer u wordt gevraagd uw keuze te bevestigen.

### <a name="disable-log-profile"></a>Logboek profiel uitschakelen

1. Gebruik de procedure die wordt beschreven in [werken met oude instellingen](#work-with-legacy-settings) om oude instellingen te openen.
2. Alle huidige verzamelingen uitschakelen voor opslag of event hubs.



## <a name="activity-log-monitoring-solution"></a>Bewakings oplossing voor activiteiten logboek
De Azure Log Analytics-bewakings oplossing bevat meerdere logboek query's en-weer gaven voor het analyseren van de records in het activiteiten logboek in uw Log Analytics-werk ruimte. Deze oplossing maakt gebruik van logboek gegevens die zijn verzameld in een Log Analytics-werk ruimte en blijven werken zonder wijzigingen als u het activiteiten logboek verzamelt met Diagnostische instellingen. Raadpleeg de [bewakings oplossing voor activiteiten logboeken](activity-log-collect.md#activity-logs-analytics-monitoring-solution) voor meer informatie over deze oplossing.

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het activiteiten logboek](../../azure-resource-manager/management/view-activity-logs.md)
* [Meer informatie over diagnostische instellingen](diagnostic-settings.md)
