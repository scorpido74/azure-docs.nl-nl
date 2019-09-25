---
title: Overzicht van het Azure-activiteiten logboek
description: Meer informatie over het Azure-activiteiten logboek en hoe u dit kunt gebruiken voor het begrijpen van gebeurtenissen die zich in uw Azure-abonnement voordoen.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: ee3a1fef379e2950172dddc389b30e0a363127ae
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262133"
---
# <a name="overview-of-azure-activity-log"></a>Overzicht van Azure-activiteiten logboek

Het **Azure-activiteiten logboek** biedt inzicht in gebeurtenissen op abonnements niveau die in azure hebben plaatsgevonden. Dit omvat een reeks gegevens, van Azure Resource Manager operationele gegevens naar updates op Service Health-gebeurtenissen. Het activiteiten logboek was voorheen bekend als _audit logboeken_ of _operationele logboeken_omdat de beheer categorie rapporten beheert voor uw abonnementen. 

Gebruik het activiteiten logboek om te bepalen _wat_, _wie_en _Wanneer_ voor schrijf bewerkingen (put, post, Delete) die zijn uitgevoerd op de resources in uw abonnement. U kunt ook de status van de bewerking en andere relevante eigenschappen begrijpen. 

Het activiteiten logboek bevat geen lees bewerkingen (GET) of bewerkingen voor resources die gebruikmaken van het klassieke/RDFE-model.

## <a name="comparison-to-resource-logs"></a>Vergelijking met resource logboeken
Er is één activiteiten logboek voor elk Azure-abonnement. Het bevat gegevens over de bewerkingen op een resource van de buiten kant (het "besturings vlak"). [Bron logboeken](resource-logs-overview.md) worden verzonden door een resource en bevatten informatie over de werking van die resource (het ' gegevens vlak '). U moet een diagnostische instelling voor elke resource maken om bron logboeken te verzamelen.

![Activiteiten logboeken vergeleken met resource logboeken](media/activity-logs-overview/Activity_Log_vs_other_logs_v5.png)


> [!NOTE]
> Het Azure-activiteiten logboek is voornamelijk bedoeld voor activiteiten die zich in Azure Resource Manager voordoen. Er worden geen resources bijgehouden met het klassieke/RDFE-model. Sommige klassieke resource typen hebben een proxy resource provider in Azure Resource Manager (bijvoorbeeld micro soft. ClassicCompute). Als u met een klassiek resource type communiceert via Azure Resource Manager met behulp van deze proxy resource providers, worden de bewerkingen weer gegeven in het activiteiten logboek. Als u communiceert met een klassiek resource type buiten de Azure Resource Manager-proxy's, worden uw acties alleen vastgelegd in het bewerkings logboek. Het bewerkings logboek kan worden doorzocht in een afzonderlijk gedeelte van de portal.

## <a name="activity-log-retention"></a>Bewaar periode van het activiteiten logboek
Zodra de vermeldingen van het activiteiten logboek zijn gemaakt, worden ze niet gewijzigd of verwijderd door het systeem. U kunt ze ook niet wijzigen in de interface of via een programma. Gebeurtenissen in het activiteiten logboek worden gedurende 90 dagen bewaard. Als u deze gegevens langere tijd wilt opslaan, moet u deze [in azure monitor verzamelen](activity-log-collect.md) of [exporteren naar opslag of event hubs](activity-log-export.md).

## <a name="view-the-activity-log"></a>Het activiteiten logboek weer geven
Bekijk het activiteiten logboek voor alle resources in het menu **monitor** in de Azure Portal. Bekijk het activiteiten logboek voor een bepaalde resource vanuit de optie **activiteiten logboek** in het menu van de resource. U kunt ook activiteiten logboek records ophalen met Power shell, CLI of REST API.  Zie [activiteiten logboek gebeurtenissen van Azure weer geven en ophalen](activity-log-view.md).

![Activiteiten logboek weer geven](./media/activity-logs-overview/view-activity-log.png)

## <a name="collect-activity-log-in-azure-monitor"></a>Activiteiten logboek verzamelen Azure Monitor
Verzamel het activiteiten logboek in een Log Analytics-werk ruimte in Azure Monitor om het met andere bewakings gegevens te analyseren en de gegevens langer dan 90 dagen te bewaren. Zie [Azure-activiteiten logboeken verzamelen en analyseren in log Analytics werk ruimte in azure monitor](activity-log-collect.md).

![Query activiteiten logboek](./media/activity-logs-overview/query-activity-log.png)

## <a name="export-activity-log"></a>Activiteitenlogboek exporteren
Exporteer het activiteiten logboek naar Azure Storage om het te archiveren of streamen naar een event hub voor opname door een service van derden of een aangepaste analyse oplossing. Zie [het Azure-activiteiten logboek exporteren](activity-log-export.md). U kunt activiteiten logboek gebeurtenissen ook analyseren in Power BI met behulp van het [**Power bi-inhouds pakket**](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).

## <a name="alert-on-activity-log"></a>Waarschuwing in het activiteiten logboek
U kunt een waarschuwing maken wanneer bepaalde gebeurtenissen in het activiteiten logboek worden gemaakt met een [waarschuwing voor een activiteiten logboek](activity-log-alerts.md). U kunt ook een waarschuwing maken met behulp van een [logboek query](alerts-log-query.md) wanneer uw activiteiten logboek is verbonden met een log Analytics-werk ruimte, maar er zijn kosten voor het registreren van query waarschuwingen. Er zijn geen kosten voor waarschuwingen voor activiteiten Logboeken.

## <a name="categories-in-the-activity-log"></a>Categorieën in het activiteiten logboek
Elke gebeurtenis in het activiteiten logboek heeft een bepaalde categorie die wordt beschreven in de volgende tabel. Zie voor volledige informatie over de schema's van deze categorieën vallen [gebeurtenisschema in het Azure-activiteitenlogboek](activity-log-schema.md). 

| Category | Description |
|:---|:---|
| Beheer | Bevat de record van alle bewerkingen voor maken, bijwerken, verwijderen en acties die zijn uitgevoerd via Resource Manager. Voor beelden van beheer gebeurtenissen zijn het maken van een _virtuele machine_ en het verwijderen van de _netwerk beveiligings groep_.<br><br>Elke actie die door een gebruiker of toepassing wordt uitgevoerd met behulp van Resource Manager, is gemodelleerd als een bewerking voor een bepaald bron type. Als het bewerkings type _schrijven_, _verwijderen_of _actie_is, worden de records van zowel het begin als het slagen of mislukken van die bewerking vastgelegd in de beheer categorie. Beheer gebeurtenissen omvatten ook eventuele wijzigingen in op rollen gebaseerd toegangs beheer in een abonnement. |
| Service Health | Bevat de record van alle service status incidenten die zich in azure hebben voorgedaan. Een voor beeld van een Service Health gebeurtenis _SQL Azure in VS-Oost_ondervindt downtime. <br><br>Service Health gebeurtenissen zijn beschikbaar in zes rassen: _Actie vereist_, _assistentie herstel_, _incident_, _onderhoud_, _informatie_of _beveiliging_. Deze gebeurtenissen worden alleen gemaakt als u een resource in het abonnement hebt die van invloed is op de gebeurtenis.
| Resourcestatus | Bevat de record van de resource status gebeurtenissen die zijn opgetreden in uw Azure-resources. Een voor beeld van een Resource Health gebeurtenis is de status van de _virtuele machine is gewijzigd in niet beschikbaar_.<br><br>Resource Health gebeurtenissen kunnen een van de volgende vier statussen vertegenwoordigen: _Beschikbaar_, niet _beschikbaar_, _gedegradeerd_en _onbekend_. Daarnaast kunnen Resource Health gebeurtenissen worden gecategoriseerd als _platform gestart_ of door de _gebruiker gestart_. |
| Waarschuwing | Bevat de registratie van activeringen voor Azure-waarschuwingen. Een voor beeld van een waarschuwings gebeurtenis is _CPU% op myVM heeft de afgelopen vijf minuten meer dan 80_.|
| Automatisch schalen | Bevat de record van gebeurtenissen die betrekking hebben op de werking van de engine voor automatisch schalen op basis van de instellingen voor automatisch schalen die u hebt gedefinieerd in uw abonnement. Een voor beeld van een automatisch schalen-gebeurtenis is het _Omhoog schalen van de actie voor schalen is mislukt_. |
| Aanbeveling | Bevat aanbevelings gebeurtenissen van Azure Advisor. |
| Beveiliging | Bevat de record van waarschuwingen die zijn gegenereerd door Azure Security Center. Een voor beeld van een beveiligings gebeurtenis is een _verdacht dubbel extensie bestand_dat wordt uitgevoerd. |
| Beleid | Bevat records van alle bewerkingen voor effect acties die zijn uitgevoerd door Azure Policy. Voor beelden van beleids gebeurtenissen zijn onder andere _controle_ en _weigeren_. Elke actie die wordt uitgevoerd door beleid, wordt gemodelleerd als een bewerking voor een resource. |


## <a name="next-steps"></a>Volgende stappen

* [Een logboek profiel maken voor het exporteren van het Azure-activiteiten logboek](activity-log-export.md)
* [Het Azure-activiteiten logboek streamen naar Event Hubs](activity-logs-stream-event-hubs.md)
* [Het Azure-activiteiten logboek archiveren in opslag](archive-activity-log.md)

