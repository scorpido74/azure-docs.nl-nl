---
title: Azure-activiteitslogboek verzamelen met diagnostische instellingen - Azure-monitor | Microsoft Documenten
description: Gebruik diagnostische instellingen om Azure Activity logs door te sturen naar Azure Monitor Logs, Azure storage of Azure Event Hubs.
author: bwren
ms.subservice: logs
ms.topic: conceptual
ms.author: bwren
ms.date: 02/04/2020
ms.openlocfilehash: 6d4c724c7cfb4c1779f0fc6592a7e61e060755b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79096904"
---
# <a name="update-to-azure-activity-log-collection-and-export"></a>Bijwerken naar logboekverzameling en export van Azure Activity-logboeken
Het [Azure Activity-logboek](platform-logs-overview.md) is een [platformlogboek](platform-logs-overview.md) dat inzicht biedt in gebeurtenissen op abonnementsniveau die zich in Azure hebben voorgedaan. De methode om activiteitslogboekvermeldingen naar [een gebeurtenishub of opslagaccount of](activity-log-export.md) naar een [loganalytics-werkruimte](activity-log-collect.md) te verzenden, is gewijzigd om [diagnostische instellingen](diagnostic-settings.md)te gebruiken. In dit artikel wordt het verschil beschreven tussen de methoden en hoe u oudere instellingen wissen ter voorbereiding om te worden gewijzigd in diagnostische instellingen.


## <a name="differences-between-methods"></a>Verschillen tussen methoden

### <a name="advantages"></a>Voordelen
Het gebruik van diagnostische instellingen heeft de volgende voordelen ten opzichte van de huidige methoden:

- Consistente methode voor het verzamelen van alle platformlogs.
- Het logboek activiteit verzamelen over meerdere abonnementen en tenants.
- Filterverzameling om alleen logboeken voor bepaalde categorieën te verzamelen.
- Alle categorieën van activiteitenlogboeken verzamelen. Sommige categorieën worden niet verzameld met behulp van een verouderde methode.
- Snellere latentie voor het innemen van logboeken. De vorige methode heeft ongeveer 15 minuten latentie, terwijl diagnostische instellingen voegt slechts ongeveer 1 minuut.

### <a name="considerations"></a>Overwegingen
Houd rekening met de volgende details van het verzamelen van activiteitenlogboeken met diagnostische instellingen voordat u deze functie inschakelt.

- De bewaarinstelling voor het verzamelen van het activiteitslogboek voor Azure-opslag is verwijderd, wat betekent dat gegevens voor onbepaalde tijd worden opgeslagen totdat u deze verwijdert.
- Momenteel u alleen een diagnostische instelling op abonnementsniveau maken met behulp van de Azure-portal. Als u andere methoden wilt gebruiken, zoals PowerShell of CLI, u een resourcemanagersjabloon maken.


### <a name="differences-in-data"></a>Verschillen in gegevens
Diagnostische instellingen verzamelen dezelfde gegevens als de vorige methoden die zijn gebruikt om het activiteitslogboek te verzamelen met de volgende huidige verschillen:

De volgende kolommen zijn verwijderd. De vervanging van deze kolommen is in een andere indeling, dus het kan nodig zijn om logboekquery's die ze gebruiken te wijzigen. U ziet mogelijk nog steeds verwijderde kolommen in het schema, maar deze worden niet gevuld met gegevens.

| Verwijderde kolom | Vervangende kolom |
|:---|:---|
| ActiviteitStatus    | ActivityStatusValue    |
| ActiviteitSubstatus | ActivitySubstatusWaarde |
| OperationName     | OperationNameValue     |
| ResourceProvider  | ResourceProviderWaarde  |

De volgende kolom is toegevoegd:

- Authorization_d
- Claims_d
- Properties_d

> [!IMPORTANT]
> In sommige gevallen kunnen de waarden in deze kolommen in alle hoofdletters zijn. Als u een query hebt die deze kolommen bevat, moet u de [operator =~](https://docs.microsoft.com/azure/kusto/query/datatypes-string-operators) gebruiken om een ongevoelige vergelijking uit te voeren.

## <a name="work-with-legacy-settings"></a>Werken met oudere instellingen
Oudere instellingen voor het verzamelen van het logboek Activiteit blijven werken als u niet kiest voor vervanging door een diagnostische instelling. Gebruik de volgende methode om het logboekprofiel voor een abonnement te beheren.

1. Selecteer **Activiteitslogboek**in het menu **Azure Monitor** in de Azure-portal .
3. Klik op **Diagnostische instellingen**.

   ![Diagnostische instellingen](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. Klik op de paarse banner voor de legacy-ervaring.

    ![Ervaring met legacy](media/diagnostic-settings-subscription/legacy-experience.png)


Zie de volgende artikelen voor meer informatie over het gebruik van de methoden voor het verzamelen van oude verzamelingen.

- [Azure-activiteitslogboeken verzamelen en analyseren in de werkruimte Log Analytics in Azure Monitor](activity-log-collect.md)
- [Azure Activity-logboeken verzamelen in Azure Monitor voor Azure Active Directory-tenants](activity-log-collect-tenants.md)
- [Azure-activiteitslogboek exporteren naar opslag of Azure-gebeurtenishubs](activity-log-export.md)

## <a name="disable-existing-settings"></a>Bestaande instellingen uitschakelen
U moet de bestaande verzameling van de activiteit uitschakelen voordat u deze inschakelt met diagnostische instellingen. Als beide zijn ingeschakeld, kunnen dubbele gegevens worden ingevoerd.

### <a name="disable-collection-into-log-analytics-workspace"></a>Verzameling uitschakelen in de werkruimte Log Analytics

1. Open het menu **Logboekanalyse-werkruimten** in de Azure-portal en selecteer de werkruimte om het activiteitenlogboek te verzamelen.
2. Selecteer **azure-activiteitenlogboek**in de sectie **Werkruimtegegevensbronnen in** het menu van de werkruimte .
3. Klik op het abonnement dat u wilt loskoppelen.
4. Klik **op Verbreken** en vervolgens **Ja** wanneer u wordt gevraagd uw keuze te bevestigen.

### <a name="disable-log-profile"></a>Logboekprofiel uitschakelen

1. Gebruik de procedure die is beschreven in [Werken met oudere instellingen](#work-with-legacy-settings) om verouderde instellingen te openen.
2. Schakel alle huidige verzamelingen uit naar opslag- of gebeurtenishubs.



## <a name="activity-log-monitoring-solution"></a>Monitoringoplossing activiteitslogboek
De bewakingsoplossing azure log Analytics bevat meerdere logboekquery's en -weergaven voor het analyseren van de activiteitenlogboekrecords in uw logboekanalysewerkruimte. Deze oplossing maakt gebruik van logboekgegevens die zijn verzameld in een loganalytics-werkruimte en blijft werken zonder wijzigingen als u het activiteitslogboek verzamelt met diagnostische instellingen. Zie [activity logs Analytics monitoring oplossing](activity-log-collect.md#activity-logs-analytics-monitoring-solution) voor meer informatie over deze oplossing.

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het activiteitenlogboek](../../azure-resource-manager/management/view-activity-logs.md)
* [Meer informatie over diagnostische instellingen](diagnostic-settings.md)
