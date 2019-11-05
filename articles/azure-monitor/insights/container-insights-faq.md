---
title: Veelgestelde vragen over het Azure Monitor voor containers | Microsoft Docs
description: Azure Monitor voor containers is een oplossing waarmee de status van uw AKS-clusters en-Container Instances in azure wordt gecontroleerd. In dit artikel vindt u antwoorden op veelgestelde vragen.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/15/2019
ms.openlocfilehash: d3779a2d48db82bfccdc0f047119a36ef56c3bdf
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73477415"
---
# <a name="azure-monitor-for-containers-frequently-asked-questions"></a>Veelgestelde vragen over containers Azure Monitor

Deze veelgestelde vragen over micro soft is een lijst met veelgestelde vragen over Azure Monitor voor containers. Als u aanvullende vragen over de oplossing hebt, gaat u naar het [discussie forum](https://feedback.azure.com/forums/34192--general-feedback) en plaatst u uw vragen. Wanneer een vraag regel matig wordt gesteld, voegen we deze toe aan dit artikel zodat het snel en eenvoudig kan worden gevonden.

## <a name="can-i-view-metrics-collected-in-grafana"></a>Kan ik de metrische gegevens weer geven die zijn verzameld in Grafana?

Azure Monitor voor containers biedt ondersteuning voor het weer geven van gegevens die zijn opgeslagen in uw Log Analytics-werk ruimte in Grafana-Dash boards. We hebben een sjabloon die u kunt downloaden uit de Grafana van het [dash board](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker) van micro soft om u op weg te helpen en te verwijzen naar informatie over het uitvoeren van een query op extra gegevens van uw bewaakte clusters om te visualiseren in aangepaste Grafana Dash boards. 

## <a name="can-i-monitor-my-aks-engine-cluster-with-azure-monitor-for-containers"></a>Kan ik mijn AKS-engine-cluster bewaken met Azure Monitor voor containers?

Azure Monitor voor containers ondersteunt bewakings werkbelastingen die zijn geïmplementeerd op de AKS-Engine (voorheen bekend als ACS-Engine) cluster (s) die worden gehost op Azure. Zie voor meer informatie en een overzicht van de stappen die nodig zijn om bewaking in te scha kelen voor dit scenario Raadpleeg [Azure monitor gebruiken voor containers voor AKS-engine](https://github.com/microsoft/OMS-docker/tree/aks-engine).

## <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>Waarom zie ik geen gegevens in mijn Log Analytics-werk ruimte?

Als u op een bepaald moment dagelijks geen gegevens in de Log Analytics-werk ruimte kunt zien, hebt u mogelijk de standaard limiet van 500 MB bereikt of is de daglimiet opgegeven om de hoeveelheid gegevens die dagelijks moet worden verzameld, te bepalen. Wanneer de limiet voor de dag wordt bereikt, stopt het verzamelen van gegevens en wordt deze alleen op de volgende dag hervat. Zie [logboek gegevens gebruiken en kosten](../platform/manage-cost-storage.md)voor meer informatie over het controleren van uw gegevens gebruik en het bijwerken van een andere prijs categorie op basis van uw verwachte gebruiks patronen. 

## <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>Wat zijn de container statussen die zijn opgegeven in de tabel ContainerInventory?

De tabel ContainerInventory bevat informatie over zowel gestopte als actieve containers. De tabel wordt gevuld met een werk stroom in de agent die de docker doorzoekt voor alle containers (actief en gestopt), en stuurt die gegevens door naar de Log Analytics-werk ruimte.
 
## <a name="how-do-i-resolve-missing-subscription-registration-error"></a>Hoe kan ik oplossen van *ontbrekende registratie* fout van abonnement?

Als u de fout melding **abonnements registratie voor micro soft. OperationsManagement**ontvangt, kunt u deze oplossen door de resource provider **micro soft. OperationsManagement** te registreren in het abonnement waarin de werk ruimte is gedefinieerd. De documentatie voor hoe u dit doet, vindt u [hier](../../azure-resource-manager/resource-manager-register-provider-errors.md).

## <a name="is-there-support-for-rbac-enabled-aks-clusters"></a>Is er ondersteuning voor AKS-clusters met RBAC ingeschakeld?

De container bewakings oplossing biedt geen ondersteuning voor RBAC, maar wordt wel ondersteund met Azure Monitor voor containers. Op de pagina oplossings Details wordt mogelijk niet de juiste informatie weer gegeven op de Blades waarin de gegevens voor deze clusters worden weer gegeven.

## <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>Hoe kan ik logboek verzameling voor containers in de uitvoeren-naam ruimte inschakelen via helm?

De logboek verzameling van containers in de uitvoeren-naam ruimte is standaard uitgeschakeld. Logboek verzameling kan worden ingeschakeld door een omgevings variabele in te stellen op de omsagent. Zie de pagina [Azure monitor voor containers](https://github.com/helm/charts/tree/master/incubator/azuremonitor-containers) github voor meer informatie. 

## <a name="how-do-i-update-the-omsagent-to-the-latest-released-version"></a>Hoe kan ik de omsagent bij naar de meest recente versie?

Zie [agent beheer](container-insights-manage-agent.md)voor meer informatie over het bijwerken van de agent.

## <a name="how-do-i-enable-multi-line-logging"></a>Hoe kan ik logboek registratie met meerdere regels inschakelen?

Momenteel Azure Monitor voor containers geen ondersteuning voor logboek registratie in meerdere regels, maar er zijn tijdelijke oplossingen beschikbaar. U kunt alle services zo configureren dat ze worden geschreven in JSON-indeling en vervolgens docker/Moby als één regel schrijft.

U kunt uw logboek bijvoorbeeld als een JSON-object laten teruglopen, zoals wordt weer gegeven in het onderstaande voor beeld voor een node. js-toepassing voor een voor beeld:

```
console.log(json.stringify({ 
      "Hello": "This example has multiple lines:",
      "Docker/Moby": "will not break this into multiple lines",
      "and you will receive":"all of them in log analytics",
      "as one": "log entry"
      }));
```

Deze gegevens zien eruit als in het volgende voor beeld in Azure Monitor voor Logboeken wanneer u er een query op uitvoert:

```
LogEntry : ({“Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

Raadpleeg de volgende [github-koppeling](https://github.com/moby/moby/issues/22920)voor een gedetailleerde weer gave van het probleem.

## <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>Hoe kan ik Azure AD-fouten oplossen wanneer ik live-logboeken inschakel? 

Mogelijk wordt de volgende fout weer **gegeven: de antwoord-URL die in de aanvraag is opgegeven, komt niet overeen met de antwoord-url's die zijn geconfigureerd voor de toepassing: ' <-toepassings-ID\>'** . De oplossing voor het oplossen hiervan vindt u in het artikel [informatie over container gegevens in realtime weer geven met Azure monitor voor containers](container-insights-livedata-setup.md#configure-ad-integrated-authentication). 

## <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>Waarom kan ik het cluster niet upgraden na het onboarden?

Als u Azure Monitor voor containers voor een AKS-cluster hebt ingeschakeld, verwijdert u de Log Analytics werk ruimte waarnaar de gegevens naar het cluster zijn verzonden bij het upgraden van het cluster. U kunt dit probleem omzeilen door de bewaking uit te scha kelen en vervolgens weer in te scha kelen naar een andere geldige werk ruimte in uw abonnement. Wanneer u de cluster upgrade opnieuw probeert uit te voeren, moet het proces worden uitgevoerd en voltooid.  

## <a name="which-ports-and-domains-do-i-need-to-openwhitelist-for-the-agent"></a>Welke poorten en domeinen heb ik nodig om de agent te openen/white list?

Zie de [netwerk firewall vereisten](container-insights-onboard.md#network-firewall-requirements) voor de proxy-en firewall configuratie-informatie die is vereist voor de container agent met Azure, Azure US Government en Azure China Clouds.

## <a name="next-steps"></a>Volgende stappen

Als u wilt beginnen met het bewaken van uw AKS-cluster, raadpleegt u [hoe u de Azure monitor voor containers](container-insights-onboard.md) kunt gebruiken om inzicht te krijgen in de vereisten en beschik bare methoden voor bewaking 
