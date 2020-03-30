---
title: Azure Security Center-waarschuwingen en -aanbevelingen exporteren naar SVM's | Microsoft Documenten
description: In dit artikel wordt uitgelegd hoe u continue export van beveiligingswaarschuwingen en aanbevelingen naar SPM's instellen
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: memildin
ms.openlocfilehash: 19fdcc7b590c6ad6873c7808ae26d218bbda7f5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158963"
---
# <a name="export-security-alerts-and-recommendations"></a>Beveiligingswaarschuwingen en aanbevelingen exporteren

Azure Security Center genereert gedetailleerde beveiligingswaarschuwingen en aanbevelingen. U ze bekijken in de portal of via programmatische tools. Het kan ook nodig zijn om deze informatie te exporteren of te sturen naar andere monitoring tools in uw omgeving. 

In dit artikel wordt de set tools beschreven waarmee u waarschuwingen en aanbevelingen handmatig of doorlopend exporteren.

Met behulp van deze tools u:

* Continu exporteren naar Log Analytics-werkruimten
* Continu exporteren naar Azure Event Hubs (voor integraties met externe SPM's)
* Exporteren naar CSV (eenmalig)


## <a name="setting-up-a-continuous-export"></a>Het opzetten van een continue export

De onderstaande stappen zijn nodig, of u nu een continue export instelt naar log Analytics-werkruimte of Azure Event Hubs.

1. Selecteer in de zijbalk **van**het Beveiligingscentrum de optie Prijzen & instellingen .

1. Selecteer het specifieke abonnement waarvoor u de gegevensexport wilt configureren.
    
1. Selecteer **Continu exporteren**op de zijbalk van de instellingenpagina voor dat abonnement .

    [Exportopties in Azure Security Center ![](media/continuous-export/continuous-export-options-page.png)](media/continuous-export/continuous-export-options-page.png#lightbox) Hier ziet u de exportopties. Er is een tabblad voor elk beschikbaar exportdoel. 

1. Selecteer het gegevenstype dat u wilt exporteren en kies uit de filters voor elk type (exporteer bijvoorbeeld alleen waarschuwingen met hoge ernst).

1. Kies in het gebied 'Doel exporteren' waar u de gegevens wilt opslaan. Gegevens kunnen worden opgeslagen in een doel op een ander abonnement (bijvoorbeeld op een Central Event Hub-instantie of een centrale Log Analytics-werkruimte).

1. Klik op **Opslaan**.



## <a name="configuring-siem-integration-via-azure-event-hubs"></a>SIEM-integratie configureren via Azure Event Hubs

Azure Event Hubs is een geweldige oplossing voor het programmatisch consumeren van streaming gegevens. Voor waarschuwingen en aanbevelingen van het Azure Security Center is dit de voorkeursmanier om te integreren met een SIEM van derden.

> [!NOTE]
> De meest effectieve methode om bewakingsgegevens te streamen naar externe hulpprogramma's is in de meeste gevallen het gebruik van Azure Event Hubs. [In dit artikel](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs) vindt u een korte beschrijving van hoe u bewakingsgegevens van verschillende bronnen streamen naar een gebeurtenishub en koppelingen naar gedetailleerde richtlijnen.

> [!NOTE]
> Als u eerder waarschuwingen voor beveiligingscentrum hebt geëxporteerd naar een SIEM met azure-activiteitslogboek, vervangt de onderstaande procedure die methode.

Als u de gebeurtenisschema's van de geëxporteerde gegevenstypen wilt weergeven, gaat u naar de [gebeurtenisschema's van de gebeurtenisgebeurtenis gebeurtenisgebeurtenis gebeurtenis gebeurtenis](https://aka.ms/ASCAutomationSchemas).


### <a name="to-integrate-with-a-siem"></a>Integreren met een SIEM 

Nadat u de door u gekozen Security Center-gegevens hebt geconfigureerd voor het continue exporteren naar Azure Event Hubs, u de juiste connector instellen voor uw SIEM:

* **Azure Sentinel** - Gebruik de native Azure Security Center-waarschuwingsgegevensconnector die daar wordt aangeboden. [data connector](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)
* **Splunk** - Gebruik de [Azure Monitor-invoegtoepassing voor Splunk](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md)
* **IBM QRadar** - Gebruik [een handmatig geconfigureerde logboekbron](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/t_dsm_guide_microsoft_azure_enable_event_hubs.html)
* **ArcSight** – [SmartConnector gebruiken](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292)

Als u de continu geëxporteerde gegevens automatisch van de geconfigureerde gebeurtenishub naar Azure Data Explorer wilt verplaatsen, gebruikt u de instructies in [Degegevens innemen van Gebeurtenishub naar Azure Data Explorer.](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub)



## <a name="continuous-export-to-a-log-analytics-workspace"></a>Continue export naar een Log Analytics-werkruimte

Als u Azure Security Center-gegevens in een Log Analytics-werkruimte wilt analyseren of Azure-waarschuwingen samen met Security Center wilt gebruiken, stelt u continue export in naar uw Log Analytics-werkruimte.

Als u wilt exporteren naar een Log Analytics-werkruimte, moet de Log Analytics-oplossingen van het Beveiligingscentrum zijn ingeschakeld op uw werkruimte. Als u de Azure-portal gebruikt, wordt de gratis laagoplossing van Security Center automatisch ingeschakeld wanneer u continue export inschakelt. Als u uw continue exportinstellingen echter programmatisch configureert, moet u handmatig de gratis of standaardprijslaag voor de vereiste werkruimte selecteren vanuit **de instellingen &.**  

### <a name="log-analytics-tables-and-schemas"></a>Log Analytics-tabellen en -schema's

Beveiligingswaarschuwingen en aanbevelingen worden respectievelijk opgeslagen in de tabellen *SecurityAlert* en *SecurityRecommendations.* De naam van de Log Analytics-oplossing die deze tabellen bevat, is afhankelijk van of u zich op de gratis of standaardlaag bevindt (zie [prijzen](security-center-pricing.md)): Beveiliging('Beveiliging en audit') of SecurityCenterFree.

![De tabel *SecurityAlert* in Log Analytics](./media/continuous-export/log-analytics-securityalert-solution.png)

Als u de gebeurtenisschema's van de geëxporteerde gegevenstypen wilt weergeven, gaat u naar de [tabelschema's van Log Analytics](https://aka.ms/ASCAutomationSchemas).

###  <a name="view-exported-security-alerts-and-recommendations-in-azure-monitor"></a>Geëxporteerde beveiligingswaarschuwingen en aanbevelingen weergeven in Azure Monitor

In sommige gevallen u ervoor kiezen om de geëxporteerde beveiligingswaarschuwingen en/of aanbevelingen in Azure Monitor weer te [geven.](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview) 

Azure Monitor biedt een uniforme waarschuwingservaring voor een verscheidenheid aan Azure-waarschuwingen, waaronder diagnostisch logboek, metrische waarschuwingen en aangepaste waarschuwingen op basis van query's van logboekanalyseswerkruimte.

Als u waarschuwingen en aanbevelingen van Beveiligingscentrum in Azure Monitor wilt weergeven, configureert u een waarschuwingsregel op basis van logboekanalysequery's (logboekwaarschuwing):

1. Klik **op de pagina Waarschuwingen van** Azure Monitor op Nieuwe **waarschuwingsregel**.

    ![De waarschuwingspagina van Azure Monitor](./media/continuous-export/azure-monitor-alerts.png)

1. Configureer op de pagina Regel maken uw nieuwe regel (op dezelfde manier als u een [logboekwaarschuwingsregel in Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)configureert):

    * Selecteer **voor Resource**de werkruimte Log Analytics waarnaar u beveiligingswaarschuwingen en aanbevelingen hebt geëxporteerd.

    * Selecteer Aangepaste **logboekzoekopdracht** **voor Voorwaarde**. Configureer op de pagina die wordt weergegeven de query, de terugkijkperiode en de frequentieperiode. In de zoekopdracht u *SecurityAlert* of *SecurityRecommendation* typen om de gegevenstypen op te vragen waarnaar Beveiligingscentrum voortdurend exporteert terwijl u de functie Continue export naar Logboekanalyse inschakelt. 
    
    * Configureer de [optie](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) de actiegroep die u wilt activeren. Actiegroepen kunnen e-mailverzenden, ITSM-tickets, WebHooks en meer activeren.
    ![Azure Monitor-waarschuwingsregel](./media/continuous-export/azure-monitor-alert-rule.png)

U ziet nu nieuwe waarschuwingen of aanbevelingen van het Azure Security Center (afhankelijk van uw configuratie) in Azure Monitor-waarschuwingen, waarbij automatisch een actiegroep wordt geactiveerd (indien aanwezig).

## <a name="manual-one-time-export-of-security-alerts"></a>Handmatige eenmalige export van beveiligingswaarschuwingen

Als u een CSV-rapport wilt downloaden voor waarschuwingen of aanbevelingen, opent u de pagina **Beveiligingswaarschuwingen** of **Aanbevelingen** en klikt u op **csv-rapport downloaden.**

[![Waarschuwingsgegevens downloaden als CSV-bestand](media/continuous-export/download-alerts-csv.png)](media/continuous-export/download-alerts-csv.png#lightbox)

> [!NOTE]
> Deze rapporten bevatten waarschuwingen en aanbevelingen voor bronnen van de momenteel geselecteerde abonnementen.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u continue export van uw aanbevelingen en waarschuwingen configureren. U hebt ook geleerd hoe u uw waarschuwingsgegevens downloaden als een CSV-bestand. 

Zie voor gerelateerd materiaal de volgende documentatie: 

- [Azure Event Hubs-documentatie](https://docs.microsoft.com/azure/event-hubs/)
- [Azure Sentinel-documentatie](https://docs.microsoft.com/azure/sentinel/)
- [Documentatie voor Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/)
- [Workflowautomatisering en schema's voor continue exportgegevens](https://aka.ms/ASCAutomationSchemas)
