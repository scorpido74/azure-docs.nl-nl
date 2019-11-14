---
title: Azure-activiteiten logboek verzamelen met Diagnostische instellingen (preview)-Azure Monitor | Microsoft Docs
description: Gebruik Diagnostische instellingen om Azure-activiteiten logboeken door te sturen naar Azure Monitor-logboeken, Azure Storage of Azure Event Hubs.
author: bwren
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
ms.author: bwren
ms.date: 10/31/2019
ms.openlocfilehash: 6104a8b01cc9fca5ff8de973e7fc2af77cda8515
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048210"
---
# <a name="collect-azure-activity-log-with-diagnostic-settings-preview"></a>Azure-activiteiten logboek met Diagnostische instellingen verzamelen (preview-versie)
Het [Azure-activiteiten logboek](activity-logs-overview.md) is een [platform logboek](platform-logs-overview.md) dat inzicht biedt in gebeurtenissen op abonnements niveau die zich in azure hebben voorgedaan. Tot nu toe hebt u een logboek profiel gemaakt voor het verzenden van activiteiten logboek vermeldingen naar [een event hub of een opslag account](activity-log-export.md) en een connector gebruikt om deze te verzamelen in een [log Analytics-werk ruimte](activity-log-collect.md).

U kunt nu een verzameling van het Azure-activiteiten logboek configureren met behulp van de [Diagnostische instellingen](diagnostic-settings.md) die worden gebruikt om [bron logboeken](resource-logs-overview.md)te verzamelen. Het gebruik van diagnostische instellingen heeft de volgende voor delen ten opzichte van de huidige methoden:

- Consistente methode voor het verzamelen van alle platform Logboeken.
- Verzamel activiteiten logboek over meerdere abonnementen en tenants.
- Filter verzameling om alleen logboeken voor bepaalde categorieën te verzamelen.
- Verzamel alle activiteiten logboek categorieën. Sommige categorieën worden niet verzameld met de vorige methode.
- Snellere latentie voor opname van Logboeken. De vorige methode heeft ongeveer 15 minuten latentie terwijl Diagnostische instellingen meer dan 1 minuut toevoegen.

## <a name="considerations"></a>Overwegingen
Houd rekening met de volgende details van de verzameling activiteiten logboeken met Diagnostische instellingen voordat u deze functie inschakelt.

- U moet de bestaande verzameling van de activiteit uitschakelen voordat u deze inschakelt met behulp van diagnostische instellingen. Als beide zijn ingeschakeld, kan dit leiden tot dubbele gegevens.
- De Bewaar instelling voor het verzamelen van het activiteiten logboek naar Azure Storage is verwijderd, wat betekent dat de gegevens voor onbepaalde tijd worden opgeslagen totdat u deze verwijdert.
- U kunt het activiteiten logboek verzenden van meerdere abonnementen naar een enkele Log Analytics-werk ruimte. U kunt het activiteiten logboek vanuit elk abonnement verzenden naar Maxi maal vijf Log Analytics-werk ruimten.

## <a name="configure-diagnostic-settings"></a>Diagnostische instellingen configureren
Gebruik de volgende procedure om een diagnostische instelling te maken in het Azure Portal om het Azure-activiteiten logboek te verzamelen. U kunt op dit moment geen diagnostische instelling voor het abonnements niveau maken met behulp van andere methoden.

1. Bestaande Log Analytics werkruimte verzameling uitschakelen voor het activiteiten logboek:
   1. Open het menu **log Analytics werk ruimten** in de Azure Portal en selecteer de werk ruimte om het activiteiten logboek te verzamelen.
   2. Selecteer in de sectie **werkruimte gegevens bronnen** van het menu van de werk ruimte **Azure-activiteiten logboek**.
   3. Klik op het abonnement dat u wilt loskoppelen.
   4. Klik op **verbinding verbreken** en vervolgens op **Ja** wanneer u wordt gevraagd uw keuze te bevestigen.
2. Selecteer in het menu **Azure monitor** van de Azure Portal het **activiteiten logboek**.
3. Klik op **Diagnostische instellingen**.
   
   ![Diagnostische instellingen](media/diagnostic-settings-subscription/diagnostic-settings.png)
   
4. Klik op de banner paars voor de oude ervaring en schakel alle huidige verzamelingen uit voor opslag of event hubs. 

    ![Verouderde ervaring](media/diagnostic-settings-subscription/legacy-experience.png)

5. Volg de procedures in [Diagnostische instellingen maken in azure Portal](diagnostic-settings.md#create-diagnostic-settings-in-azure-portal) om een diagnostische instelling te maken. Zie [Categorieën in het activiteiten logboek](activity-logs-overview.md#categories-in-the-activity-log) voor een uitleg van de categorieën die u kunt gebruiken om gebeurtenissen uit het activiteiten logboek te filteren. 


## <a name="differences-in-data"></a>Verschillen in gegevens
Diagnostische instellingen verzamelen dezelfde gegevens als de vorige methoden die worden gebruikt voor het verzamelen van het activiteiten logboek met de volgende huidige verschillen:

De volgende eigenschappen zijn verwijderd:

- ActivityStatus
- ActivitySubstatus
- OperationName
- ResourceProvider 

De volgende eigenschappen zijn toegevoegd:

- Authorization_d
- Claims_d
- Properties_d

## <a name="activity-log-monitoring-solution"></a>Bewakings oplossing voor activiteiten logboek
De Azure Log Analytics-bewakings oplossing bevat meerdere logboek query's en-weer gaven voor het analyseren van de records in het activiteiten logboek in uw Log Analytics-werk ruimte. Deze oplossing maakt gebruik van logboek gegevens die zijn verzameld in een Log Analytics-werk ruimte en blijven werken zonder wijzigingen als u het activiteiten logboek verzamelt met Diagnostische instellingen. Raadpleeg de [bewakings oplossing voor activiteiten logboeken](activity-log-collect.md#activity-logs-analytics-monitoring-solution) voor meer informatie over deze oplossing.

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het activiteiten logboek](../../azure-resource-manager/resource-group-audit.md)
* [Meer informatie over diagnostische instellingen](diagnostic-settings.md)
