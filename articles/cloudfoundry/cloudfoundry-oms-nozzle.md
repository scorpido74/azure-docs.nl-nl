---
title: Azure Log Analytics Nozzle implementeren voor cloudfoundry-monitoring
description: Stapsgewijze richtlijnen voor de implementatie van de Cloud Foundry-loggregator nozzle voor Azure Log Analytics. Gebruik de nozzle om de status- en prestatiestatistieken van het Cloud Foundry-systeem te controleren.
services: virtual-machines-linux
author: ningk
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/22/2017
ms.author: ningk
ms.openlocfilehash: bf6691310ec964a1d6293f3a60c151e3d6f8e641
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277364"
---
# <a name="deploy-azure-log-analytics-nozzle-for-cloud-foundry-system-monitoring"></a>Azure Log Analytics Nozzle implementeren voor cloud foundry-systeembewaking

[Azure Monitor](https://azure.microsoft.com/services/log-analytics/) is een service in Azure. Het helpt u bij het verzamelen en analyseren van gegevens die worden gegenereerd vanuit uw cloud- en on-premises omgevingen.

De Log Analytics Nozzle (the Nozzle) is een CF-component (Cloud Foundry), waarmee statistieken van de [cloudfoundry-loggregator-firehose](https://docs.cloudfoundry.org/loggregator/architecture.html) worden doorgestuurd naar Azure Monitor-logboeken. Met de Nozzle u uw CF-systeemstatus- en prestatiestatistieken verzamelen, bekijken en analyseren, voor meerdere implementaties.

In dit document leert u hoe u het nozzle implementeert in uw CF-omgeving en vervolgens toegang krijgt tot de gegevens van de Azure Monitor-logboekenconsole.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Vereisten

De volgende stappen zijn vereisten voor het implementeren van de nozzle.

### <a name="1-deploy-a-cf-or-pivotal-cloud-foundry-environment-in-azure"></a>1. Een CF- of Pivotal Cloud Foundry-omgeving implementeren in Azure

U de Nozzle gebruiken met een open source CF-implementatie of een Pivotal Cloud Foundry -implementatie .You can use the Nozzle with either a open source CF deployment or a Pivotal Cloud Foundry (PCF) deployment.

* [Cloud Foundry op Azure implementeren](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md)

* [Pivotal Cloud Foundry implementeren op Azure](https://docs.pivotal.io/pivotalcf/1-11/customizing/azure.html)

### <a name="2-install-the-cf-command-line-tools-for-deploying-the-nozzle"></a>2. Installeer de CF-opdrachtregelgereedschappen voor het implementeren van de nozzle

De Nozzle wordt uitgevoerd als een toepassing in CF-omgeving. U hebt CF CLI nodig om de toepassing te implementeren.

De Nozzle heeft ook toegang nodig tot de loggregator brandslang en de Cloud Controller. Als u de gebruiker wilt maken en configureren, hebt u de UAA-service (User Account and Authentication) nodig.

* [Cloud Foundry CLI installeren](https://docs.cloudfoundry.org/cf-cli/install-go-cli.html)

* [Cloud Foundry UAA-opdrachtregelclient installeren](https://github.com/cloudfoundry/cf-uaac/blob/master/README.md)

Voordat u de UAA-command-Line-client instelt, moet u ervoor zorgen dat RubyGems is geïnstalleerd.

### <a name="3-create-a-log-analytics-workspace-in-azure"></a>3. Een Werkruimte logboekanalyse maken in Azure

U de werkruimte Log Analytics handmatig of met behulp van een sjabloon maken. De sjabloon implementeert een instelling van vooraf geconfigureerde KPI-weergaven en waarschuwingen voor de Azure Monitor-logboekenconsole. 

#### <a name="to-create-the-workspace-manually"></a>Ga als u de werkruimte handmatig wilt maken:

1. Zoek in de Azure-portal in de lijst met services in de Azure Marketplace en selecteer vervolgens Logboekanalysewerkruimten.
2. Selecteer **Maken**en selecteer vervolgens keuzes voor de volgende items:

   * **Werkruimte Log Analytics:** Typ een naam voor uw werkruimte.
   * **Abonnement:** Als u meerdere abonnementen hebt, kiest u het abonnement dat hetzelfde is als uw CF-implementatie.
   * **Resourcegroep:** u een nieuwe resourcegroep maken of dezelfde groep gebruiken met uw CF-implementatie.
   * **Locatie**: Voer de locatie in.
   * **Prijscategorie:** Selecteer **OK** om te voltooien.

Zie [Aan de slag met Azure Monitor-logboeken](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started)voor meer informatie.

#### <a name="to-create-the-log-analytics-workspace-through-the-monitoring-template-from-azure-market-place"></a>Ga als volgt te werk om de werkruimte Log Analytics te maken via de bewakingssjabloon van Azure Market Place:

1. Open Azure Portal.
1. Klik op het bord '+' of 'Een resource maken' in de linkerbovenhoek.
1. Typ "Cloud Foundry" in het zoekvenster en selecteer "Cloud Foundry Monitoring Solution".
1. De voorpagina van de cloudfoundry-bewakingsoplossingssjabloon is geladen, klik op 'Maken' om het sjabloonblad te starten.
1. Voer de vereiste parameters in:
    * **Abonnement:** Selecteer een Azure-abonnement voor de werkruimte Log Analytics, meestal hetzelfde met de implementatie van Cloud Foundry.
    * **Resourcegroep:** selecteer een bestaande resourcegroep of maak een nieuwe groep voor de werkruimte Log Analytics.
    * **Locatie resourcegroep:** selecteer de locatie van de resourcegroep.
    * **OMS_Workspace_Name:** Voer een werkruimtenaam in als de werkruimte niet bestaat, maakt de sjabloon een nieuwe.
    * **OMS_Workspace_Region**: Selecteer de locatie voor de werkruimte.
    * **OMS_Workspace_Pricing_Tier:** Selecteer de SKU-werkruimte Log Analytics. Zie de [prijsrichtlijnen](https://azure.microsoft.com/pricing/details/log-analytics/) voor referentie.
    * **Juridische voorwaarden**: Klik op Juridische voorwaarden en klik vervolgens op 'Maken' om de juridische term te accepteren.
1. Nadat alle parameters zijn opgegeven, klikt u op 'Maken' om de sjabloon te implementeren. Wanneer de implementatie is voltooid, wordt de status weergegeven op het tabblad melding.


## <a name="deploy-the-nozzle"></a>Het mondstuk implementeren

Er zijn een paar verschillende manieren om de Nozzle te implementeren: als een PCF-tegel of als CF-toepassing.

### <a name="deploy-the-nozzle-as-a-pcf-ops-manager-tile"></a>De nozzle implementeren als een pcf ops-manager-tegel

Volg de stappen voor [het installeren en configureren van de Azure Log Analytics Nozzle voor PCF.](https://docs.pivotal.io/partners/azure-log-analytics-nozzle/installing.html) Dit is de vereenvoudigde aanpak, de PCF Ops manager tegel zal automatisch configureren en duwen het mondstuk. 

### <a name="deploy-the-nozzle-manually-as-a-cf-application"></a>De nozzle handmatig implementeren als CF-toepassing

Als u PCF Ops Manager niet gebruikt, implementeert u de nozzle als toepassing. In de volgende secties wordt dit proces beschreven.

#### <a name="sign-in-to-your-cf-deployment-as-an-admin-through-cf-cli"></a>Meld u aan bij uw CF-implementatie als beheerder via CF CLI

Voer de volgende opdracht uit:
```
cf login -a https://api.${SYSTEM_DOMAIN} -u ${CF_USER} --skip-ssl-validation
```

"SYSTEM_DOMAIN" is uw CF domeinnaam. U het ophalen door te zoeken in de 'SYSTEM_DOMAIN' in uw CF-implementatiemanifestbestand. 

"CF_User" is de CF-admin naam. U de naam en het wachtwoord ophalen door in de sectie 'scim' te zoeken en te zoeken naar de naam en de cf_admin_password in uw CF-implementatiemanifestbestand.

#### <a name="create-a-cf-user-and-grant-required-privileges"></a>Een CF-gebruiker maken en vereiste bevoegdheden verlenen

Voer de volgende opdrachten uit:
```
uaac target https://uaa.${SYSTEM_DOMAIN} --skip-ssl-validation
uaac token client get admin
cf create-user ${FIREHOSE_USER} ${FIREHOSE_USER_PASSWORD}
uaac member add cloud_controller.admin ${FIREHOSE_USER}
uaac member add doppler.firehose ${FIREHOSE_USER}
```

"SYSTEM_DOMAIN" is uw CF domeinnaam. U het ophalen door te zoeken in de 'SYSTEM_DOMAIN' in uw CF-implementatiemanifestbestand.

#### <a name="download-the-latest-log-analytics-nozzle-release"></a>Download de nieuwste Log Analytics Nozzle release

Voer de volgende opdracht uit:
```
git clone https://github.com/Azure/oms-log-analytics-firehose-nozzle.git
cd oms-log-analytics-firehose-nozzle
```

#### <a name="set-environment-variables"></a>Omgevingsvariabelen instellen

U nu omgevingsvariabelen instellen in het manifest.yml-bestand in uw huidige map. Het volgende toont het app-manifest voor de nozzle. Vervang waarden door uw specifieke log Analytics-werkruimtegegevens.

```
OMS_WORKSPACE             : Log Analytics workspace ID: Open your Log Analytics workspace in the Azure portal, select **Advanced settings**, select **Connected Sources**, and select **Windows Servers**.
OMS_KEY                   : OMS key: Open your Log Analytics workspace in the Azure portal, select **Advanced settings**, select **Connected Sources**, and select **Windows Servers**.
OMS_POST_TIMEOUT          : HTTP post timeout for sending events to Azure Monitor logs. The default is 10 seconds.
OMS_BATCH_TIME            : Interval for posting a batch to Azure Monitor logs. The default is 10 seconds.
OMS_MAX_MSG_NUM_PER_BATCH : The maximum number of messages in a batch to Azure Monitor logs. The default is 1000.
API_ADDR                  : The API URL of the CF environment. For more information, see the preceding section, "Sign in to your CF deployment as an admin through CF CLI."
DOPPLER_ADDR              : Loggregator's traffic controller URL. For more information, see the preceding section, "Sign in to your CF deployment as an admin through CF CLI."
FIREHOSE_USER             : CF user you created in the preceding section, "Create a CF user and grant required privileges." This user has firehose and Cloud Controller admin access.
FIREHOSE_USER_PASSWORD    : Password of the CF user above.
EVENT_FILTER              : Event types to be filtered out. The format is a comma-separated list. Valid event types are METRIC, LOG, and HTTP.
SKIP_SSL_VALIDATION       : If true, allows insecure connections to the UAA and the traffic controller.
CF_ENVIRONMENT            : Enter any string value for identifying logs and metrics from different CF environments.
IDLE_TIMEOUT              : The Keep Alive duration for the firehose consumer. The default is 60 seconds.
LOG_LEVEL                 : The logging level of the Nozzle. Valid levels are DEBUG, INFO, and ERROR.
LOG_EVENT_COUNT           : If true, the total count of events that the Nozzle has received and sent are logged to Azure Monitor logs as CounterEvents.
LOG_EVENT_COUNT_INTERVAL  : The time interval of the logging event count to Azure Monitor logs. The default is 60 seconds.
```

### <a name="push-the-application-from-your-development-computer"></a>De toepassing van uw ontwikkelingscomputer pushen

Zorg ervoor dat u zich onder de oms-log-analytics-firehose-nozzle map bevindt. Voer de volgende opdracht uit:
```
cf push
```

## <a name="validate-the-nozzle-installation"></a>De nozzle-installatie valideren

### <a name="from-apps-manager-for-pcf"></a>Van Apps Manager (voor PCF)

1. Meld u aan bij Ops Manager en controleer of de tegel wordt weergegeven op het installatiedashboard.
2. Meld u aan bij Apps Manager, controleer of de ruimte die u voor het mondstuk hebt gemaakt, wordt vermeld in het gebruiksrapport en controleer of de status normaal is.

### <a name="from-your-development-computer"></a>Vanaf uw ontwikkelingscomputer

Typ in het CF CLI-venster:
```
cf apps
```
Controleer of de OMS Nozzle-toepassing wordt uitgevoerd.

## <a name="view-the-data-in-the-azure-portal"></a>De gegevens weergeven in de Azure-portal

Als u de bewakingsoplossing via de sjabloon Market Place hebt geïmplementeerd, gaat u naar azureportal en zoekt u de oplossing. U de oplossing vinden in de resourcegroep die u in de sjabloon hebt opgegeven. Klik op de oplossing, blader naar de 'log analytics console', de vooraf geconfigureerde weergaven worden weergegeven, met de beste Kpi's van het Cloud Foundry-systeem, toepassingsgegevens, waarschuwingen en VM-statusstatistieken. 

Als u de werkruimte Log Analytics handmatig hebt gemaakt, voert u onderstaande stappen uit om de weergaven en waarschuwingen te maken:

### <a name="1-import-the-oms-view"></a>1. De OMS-weergave importeren

Blader in de OMS-portal naar **Designer** > **Import** > **Browse**bekijken en selecteer een van de omsview-bestanden. Selecteer bijvoorbeeld *Cloud Foundry.omsview*en sla de weergave op. Nu wordt een tegel weergegeven op de **pagina Overzicht.** Selecteer deze optie om gevisualiseerde statistieken te bekijken.

U deze weergaven aanpassen of nieuwe weergaven maken via **View Designer**.

De *"Cloud Foundry.omsview"* is een preview-versie van de Cloud Foundry OMS-weergavesjabloon. Dit is een volledig geconfigureerde, standaardsjabloon. Als u suggesties of feedback hebt over de sjabloon, stuurt u deze naar de [sectie probleem](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues).

### <a name="2-create-alert-rules"></a>2. Waarschuwingsregels maken

U [de waarschuwingen maken](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts)en de query's en drempelwaarden zo nodig aanpassen. De volgende waarschuwingen zijn aanbevolen waarschuwingen:

| Zoekquery                                                                  | Waarschuwing genereren op basis van | Beschrijving                                                                       |
| ----------------------------------------------------------------------------- | ----------------------- | --------------------------------------------------------------------------------- |
| Typ=CF_ValueMetric_CL Origin_s=bbs Name_s="Domain.cf-apps"                   | Aantal resultaten < 1   | **bbs. Domain.cf-apps** geeft aan of het cf-apps-domein up-to-date is. Dit betekent dat CF App-aanvragen van Cloud Controller worden gesynchroniseerd met bbs. LRPsDesired (Diego-gewenste AI's) voor uitvoering. Geen ontvangen gegevens betekent cf-apps Domein is niet up-to-date in het opgegeven tijdvenster. |
| Type=CF_ValueMetric_CL Origin_s=rep Name_s=OngezondValue_d>1            | Aantal resultaten > 0   | Voor Diego cellen, 0 betekent gezond, en 1 betekent ongezond. Stel de waarschuwing in als meerdere ongezonde Diego-cellen worden gedetecteerd in het opgegeven tijdvenster. |
| Type=CF_ValueMetric_CL Origin_s="bosh-hm-forwarder" Name_s="system.healthy" Value_d=0 | Aantal resultaten > 0 | 1 betekent dat het systeem gezond is, en 0 betekent dat het systeem niet gezond is. |
| Type=CF_ValueMetric_CL Origin_s=route_emitter Name_s=ConsulDownMode Value_d>0 | Aantal resultaten > 0   | Consul zendt zijn gezondheidstoestand periodiek uit. 0 betekent dat het systeem gezond is, en 1 betekent dat de routezender detecteert dat Consul is uitgeschakeld. |
| Type=CF_CounterEvent_CL Origin_s=DopplerServer (Name_s="TruncatingBuffer.DroppedMessages" of Name_s="doppler.shedEnvelopes") Delta_d>0 | Aantal resultaten > 0 | Het delta aantal berichten opzettelijk gedaald door Doppler als gevolg van rugdruk. |
| Type=CF_LogMessage_CL SourceType_s=LGR MessageType_s=ERR                      | Aantal resultaten > 0   | Loggregator zendt **LGR** uit om problemen met het registratieproces aan te geven. Een voorbeeld van een dergelijk probleem is wanneer de uitvoer van het logboekbericht te hoog is. |
| Type=CF_ValueMetric_CL Name_s=slowConsumerAlert                               | Aantal resultaten > 0   | Wanneer het nozzle een waarschuwing van de consument ontvangt van loggregator, wordt de **slowConsumerAlert** ValueMetric naar Azure Monitor-logboeken verzenden. |
| Type=CF_CounterEvent_CL Job_s=nozzle Name_s=eventsLost Delta_d>0              | Aantal resultaten > 0   | Als het deltaaantal verloren gebeurtenissen een drempelbereikt, betekent dit dat de nozzle een probleem kan hebben met het uitvoeren van problemen. |

## <a name="scale"></a>Schalen

U de nozzle en de loggregator schalen.

### <a name="scale-the-nozzle"></a>Schaal het mondstuk

U moet beginnen met ten minste twee exemplaren van de nozzle. De brandslang verdeelt de werklast over alle gevallen van de nozzle.
Als u ervoor wilt zorgen dat het mondstuk het gegevensverkeer van de brandslang kan bijhouden, stelt u de **waarschuwing voor slowConsumerAlert** in (in de vorige sectie 'Waarschuwingsregels maken'). Nadat u bent gewaarschuwd, volgt u de [richtlijnen voor slow Nozzle](https://docs.pivotal.io/pivotalcf/1-11/loggregator/log-ops-guide.html#slow-noz) om te bepalen of schalen nodig is.
Als u de nozzle wilt opschalen, gebruikt u Apps Manager of de CF CLI om de instantienummers of de geheugen- of schijfbronnen voor het mondstuk te verhogen.

### <a name="scale-the-loggregator"></a>De loggregator schalen

Loggregator stuurt een **LGR-logboekbericht** om problemen met het registratieproces aan te geven. U de waarschuwing controleren om te bepalen of de loggregator moet worden opgeschaald.
Als u de loggregator wilt opschalen, verhoogt u de dopplerbuffergrootte of voegt u extra Doppler-serverexemplaren toe aan het CF-manifest. Zie voor meer informatie [de richtlijnen voor het schalen van de loggregator](https://docs.cloudfoundry.org/running/managing-cf/logging-config.html#scaling).

## <a name="update"></a>Update

Als u de nozzle wilt bijwerken met een nieuwere versie, downloadt u de nieuwe nozzle-release, volgt u de stappen in de vorige sectie 'Het mondstuk implementeren' en drukt u de toepassing opnieuw.

### <a name="remove-the-nozzle-from-ops-manager"></a>Het mondstuk verwijderen uit Ops Manager

1. Meld u aan bij Ops Manager.
2. Zoek de **Microsoft Azure Log Analytics Nozzle voor PCF-tegel.**
3. Selecteer het pictogram voor afval en bevestig de verwijdering.

### <a name="remove-the-nozzle-from-your-development-computer"></a>Verwijder het mondstuk van uw ontwikkelingscomputer

Typ in het CF CLI-venster:
```
cf delete <App Name> -r
```

Als u de nozzle verwijdert, worden de gegevens in OMS-portal niet automatisch verwijderd. Deze vervalt op basis van de bewaarinstelling van uw Azure Monitor-logboeken.

## <a name="support-and-feedback"></a>Ondersteuning en feedback

Azure Log Analytics Nozzle is open source. Stuur uw vragen en feedback naar de [GitHub-sectie.](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues) Als u een Azure-ondersteuningsaanvraag wilt openen, kiest u 'Virtual Machine running Cloud Foundry' als servicecategorie. 

## <a name="next-step"></a>Volgende stap

Vanaf PCF2.0 worden VM-prestatiestatistieken door System Metrics Forwarder overgebracht naar Azure Log Analytics-nozzle en geïntegreerd in de werkruimte Log Analytics. U hebt de Log Analytics-agent niet meer nodig voor de VM-prestatiestatistieken. U de log-analyse-agent echter nog steeds gebruiken om Syslog-informatie te verzamelen. De Log Analytics-agent is geïnstalleerd als een Bosh-add-on voor uw CF VM's. 

Zie [Loganalytics-agent implementeren voor meer](https://github.com/Azure/oms-agent-for-linux-boshrelease)informatie naar uw implementatie van Cloud Foundry.
