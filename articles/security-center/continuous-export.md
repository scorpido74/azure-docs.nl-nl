---
title: Azure Security Center waarschuwingen en aanbevelingen exporteren naar Siem's | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe u continue export van beveiligings waarschuwingen en aanbevelingen instelt voor Siem's
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: memildin
ms.openlocfilehash: d101acd3e72e68efd9198cb273fd352967a0cd54
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/13/2020
ms.locfileid: "88192366"
---
# <a name="export-security-alerts-and-recommendations"></a>Beveiligingswaarschuwingen en aanbevelingen exporteren

Azure Security Center genereert gedetailleerde beveiligings waarschuwingen en aanbevelingen. U kunt deze weer geven in de portal of via programmatische hulpprogram ma's. Mogelijk moet u deze informatie ook exporteren of verzenden naar andere controle hulpprogramma's in uw omgeving. 

In dit artikel wordt de set hulpprogram ma's beschreven waarmee u waarschuwingen en aanbevelingen hand matig of op doorlopende wijze kunt exporteren.

Met deze hulpprogram ma's kunt u het volgende doen:

* Doorlopend exporteren naar Log Analytics-werk ruimten
* Doorlopend exporteren naar Azure Event Hubs (voor integratie met Siem's van derden)
* Exporteren naar CSV (één keer)



## <a name="availability"></a>Beschikbaarheid

|Aspect|Details|
|----|:----|
|Release status:|Algemeen beschikbaar|
|Koers|Gratis laag|
|Vereiste rollen en machtigingen:|**Rol van beveiligings beheerder** voor de resource groep (of **eigenaar**)<br>Moet ook schrijf machtigingen hebben voor de doel resource|
|Clouds|![Ja](./media/icons/yes-icon.png) Commerciële Clouds<br>![Ja](./media/icons/yes-icon.png) US Gov<br>![Nee](./media/icons/no-icon.png) China gov, andere gov|
|||



## <a name="setting-up-a-continuous-export"></a>Een continue export instellen

De onderstaande stappen zijn nodig om een doorlopende export naar Log Analytics werk ruimte of Azure Event Hubs in te stellen.

1. Selecteer op de zijbalk van Security Center de **& instellingen voor prijzen**.

1. Selecteer het specifieke abonnement waarvoor u de gegevens export wilt configureren.
    
1. Selecteer in de zijbalk van de pagina instellingen voor dat abonnement **doorlopend exporteren**.

    [ ![ Export opties in azure Security Center](media/continuous-export/continuous-export-options-page.png)](media/continuous-export/continuous-export-options-page.png#lightbox) hier ziet u de export opties. Er is een tabblad voor elk beschik bare export doel. 

1. Selecteer het gegevens type dat u wilt exporteren en kies uit de filters voor elk type (bijvoorbeeld alleen waarschuwingen met hoge Ernst exporteren).

1. Kies in het gebied export doel de locatie waar u de gegevens wilt opslaan. Gegevens kunnen worden opgeslagen in een doel voor een ander abonnement (bijvoorbeeld op een centraal Event hub-exemplaar of een centrale Log Analytics-werk ruimte).

1. Klik op **Opslaan**.



## <a name="configuring-siem-integration-via-azure-event-hubs"></a>SIEM-integratie configureren via Azure Event Hubs

Azure Event Hubs is een geweldige oplossing voor het programmatisch gebruiken van streaming-gegevens. Voor Azure Security Center waarschuwingen en aanbevelingen is het de aanbevolen manier om te integreren met een SIEM van derden.

> [!NOTE]
> De meest efficiënte methode voor het streamen van bewakings gegevens naar externe hulpprogram ma's is in de meeste gevallen het gebruik van Azure Event Hubs. In [dit artikel](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs) vindt u een korte beschrijving van de manier waarop u bewakings gegevens uit verschillende bronnen kunt streamen naar een event hub en koppelingen naar gedetailleerde richt lijnen.

> [!NOTE]
> Als u eerder Security Center waarschuwingen naar een SIEM hebt geëxporteerd met behulp van Azure-activiteiten logboek, vervangt de onderstaande procedure die methodologie.

Als u de gebeurtenis schema's van de geëxporteerde gegevens typen wilt weer geven, gaat u naar event [hub Event schemas](https://aka.ms/ASCAutomationSchemas).


### <a name="to-integrate-with-a-siem"></a>Integreren met een SIEM 

Nadat u continue export van uw gekozen Security Center gegevens naar Azure Event Hubs hebt geconfigureerd, kunt u de juiste connector instellen voor uw SIEM:

* **Azure-Sentinel** : gebruik de systeem eigen Azure Security Center Alerts [Data Connector](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center) is daar beschikbaar.
* **Splunk** : gebruik de [Azure monitor-invoeg toepassing voor Splunk](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md)
* **IBM QRadar** : [een hand matig geconfigureerde logboek bron](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/t_dsm_guide_microsoft_azure_enable_event_hubs.html) gebruiken
* **ArcSight** : gebruik [SmartConnector](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292)

Als u de voortdurend geëxporteerde gegevens automatisch van uw geconfigureerde Event hub naar Azure Data Explorer wilt verplaatsen, gebruikt u de instructies in [opname gegevens van Event hub naar azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub).



## <a name="continuous-export-to-a-log-analytics-workspace"></a>Doorlopend exporteren naar een Log Analytics-werk ruimte

Als u Azure Security Center gegevens in een Log Analytics werk ruimte wilt analyseren of als u Azure-waarschuwingen wilt gebruiken in combi natie met Security Center, doorlopende export naar uw Log Analytics-werk ruimte.

Als u wilt exporteren naar een Log Analytics-werk ruimte, moet u de Log Analytics oplossingen van Security Center op uw werk ruimte hebben ingeschakeld. Als u de Azure Portal gebruikt, wordt de oplossing gratis laag van Security Center automatisch ingeschakeld wanneer u doorlopend exporteren inschakelt. Als u echter de instellingen voor continue export programmatisch configureert, moet u hand matig de prijs categorie gratis of standaard selecteren voor de vereiste werk ruimte in de **instellingen voor de prijs &**.  

### <a name="log-analytics-tables-and-schemas"></a>Log Analytics tabellen en schema's

Beveiligings waarschuwingen en aanbevelingen worden respectievelijk opgeslagen in de *SecurityAlert* -en *SecurityRecommendations* -tabellen. De naam van de Log Analytics oplossing die deze tabellen bevat, is afhankelijk van of u zich in de laag gratis of Standard bevindt (Zie [prijzen](security-center-pricing.md)): Security (beveiliging en audit) of SecurityCenterFree.

![De * SecurityAlert *-tabel in Log Analytics](./media/continuous-export/log-analytics-securityalert-solution.png)

Als u de gebeurtenis schema's van de geëxporteerde gegevens typen wilt weer geven, gaat u naar de [log Analytics-tabel schema's](https://aka.ms/ASCAutomationSchemas).

###  <a name="view-exported-security-alerts-and-recommendations-in-azure-monitor"></a>Geëxporteerde beveiligings waarschuwingen en aanbevelingen in Azure Monitor weer geven

In sommige gevallen kunt u ervoor kiezen om de geëxporteerde beveiligings waarschuwingen en/of aanbevelingen in [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)weer te geven. 

Azure Monitor biedt een uniforme waarschuwings ervaring voor verschillende Azure-waarschuwingen, waaronder diagnostisch logboeken, metrische waarschuwingen en aangepaste waarschuwingen op basis van Log Analytics werkruimte query's.

Als u waarschuwingen en aanbevelingen van Security Center in Azure Monitor wilt weer geven, configureert u een waarschuwings regel op basis van Log Analytics query's (logboek waarschuwing):

1. Klik op de pagina **waarschuwingen** van Azure monitor op **nieuwe waarschuwings regel**.

    ![Pagina waarschuwingen van Azure Monitor](./media/continuous-export/azure-monitor-alerts.png)

1. Configureer op de pagina regel maken uw nieuwe regel (op dezelfde manier als u een [waarschuwings regel voor het logboek configureert in azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)):

    * Selecteer voor **resource**de log Analytics werk ruimte waarnaar u beveiligings waarschuwingen en aanbevelingen hebt geëxporteerd.

    * Selecteer **Condition**voor voor waarde **aangepaste logboek zoekopdracht**. Configureer op de pagina die wordt weer gegeven, de query, de lookback periode en de frequentie periode. In de zoek query kunt u *SecurityAlert* of *SecurityRecommendation* typen om een query uit te voeren op de gegevens typen die Security Center doorlopend exporteren naar wanneer u de functie continue export naar log Analytics inschakelt. 
    
    * U kunt desgewenst de [actie groep](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) configureren die u wilt activeren. Actie groepen kunnen e-mail verzenden, ITSM tickets, webhooks en meer activeren.
    ![Waarschuwings regel Azure Monitor](./media/continuous-export/azure-monitor-alert-rule.png)

U ziet nu nieuwe Azure Security Center waarschuwingen of aanbevelingen (afhankelijk van uw configuratie) in Azure Monitor-waarschuwingen, waarbij automatisch een actie groep wordt geactiveerd (indien gegeven).

## <a name="manual-one-time-export-of-security-alerts"></a>Hand matige eenmalige export van beveiligings waarschuwingen

Als u een CSV-rapport voor waarschuwingen of aanbevelingen wilt downloaden, opent u de pagina **beveiligings waarschuwingen** of **aanbevelingen** en klikt u op de knop **CSV-rapport downloaden** .

[![Waarschuwings gegevens downloaden als een CSV-bestand](media/continuous-export/download-alerts-csv.png)](media/continuous-export/download-alerts-csv.png#lightbox)

> [!NOTE]
> Deze rapporten bevatten waarschuwingen en aanbevelingen voor resources van de momenteel geselecteerde abonnementen.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u continue export van uw aanbevelingen en waarschuwingen kunt configureren. U hebt ook geleerd hoe u uw waarschuwings gegevens kunt downloaden als een CSV-bestand. 

Raadpleeg de volgende documentatie voor gerelateerde materialen: 

- [Documentatie over Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/)
- [Informatie over Azure-Sentinel](https://docs.microsoft.com/azure/sentinel/)
- [Azure Monitor-documentatie](https://docs.microsoft.com/azure/azure-monitor/)
- [Schema's voor werk stroom automatisering en doorlopend exporteren van gegevens typen](https://aka.ms/ASCAutomationSchemas)
