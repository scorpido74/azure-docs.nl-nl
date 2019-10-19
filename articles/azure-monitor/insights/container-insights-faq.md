---
title: Veelgestelde vragen over het Azure Monitor voor containers | Microsoft Docs
description: Azure Monitor voor containers is een oplossing waarmee de status van uw AKS-clusters en-Container Instances in azure wordt gecontroleerd. In dit artikel vindt u antwoorden op veelgestelde vragen.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 08/14/2019
ms.openlocfilehash: bda64dd555f1970b70878d827f6be1dab3f1e2d5
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555426"
---
# <a name="azure-monitor-for-containers-frequently-asked-questions"></a>Veelgestelde vragen over containers Azure Monitor

Deze veelgestelde vragen over micro soft is een lijst met veelgestelde vragen over Azure Monitor voor containers. Als u aanvullende vragen over de oplossing hebt, gaat u naar het [discussie forum](https://feedback.azure.com/forums/34192--general-feedback) en plaatst u uw vragen. Wanneer een vraag regel matig wordt gesteld, voegen we deze toe aan dit artikel zodat het snel en eenvoudig kan worden gevonden.

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

Mogelijk wordt de volgende fout weer **gegeven: de antwoord-URL die in de aanvraag is opgegeven, komt niet overeen met de antwoord-url's die zijn geconfigureerd voor de toepassing: ' <-toepassings-ID \> '** . De oplossing om deze op te lossen vindt u in het artikel een [realtime weer gave van container logboeken met Azure monitor voor containers](container-insights-live-logs.md#configure-aks-with-azure-active-directory). 

## <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>Waarom kan ik het cluster niet upgraden na het onboarden?

Als u Azure Monitor voor containers voor een AKS-cluster hebt ingeschakeld, verwijdert u de Log Analytics werk ruimte waarnaar de gegevens naar het cluster zijn verzonden bij het upgraden van het cluster. U kunt dit probleem omzeilen door de bewaking uit te scha kelen en vervolgens weer in te scha kelen naar een andere geldige werk ruimte in uw abonnement. Wanneer u de cluster upgrade opnieuw probeert uit te voeren, moet het proces worden uitgevoerd en voltooid.  

## <a name="which-ports-and-domains-do-i-need-to-openwhitelist-for-the-agent"></a>Welke poorten en domeinen heb ik nodig om de agent te openen/white list?
- *. ods.opinsights.azure.com 443
- *. oms.opinsights.azure.com 443
- *. blob.core.windows.net 443
- dc.services.visualstudio.com 443
- *. microsoftonline.com 443
- *. monitoring.azure.com 443
- login.microsoftonline.com 443

## <a name="next-steps"></a>Volgende stappen

Als u wilt beginnen met het bewaken van uw AKS-cluster, raadpleegt u [hoe u de Azure monitor voor containers](container-insights-onboard.md) kunt gebruiken om inzicht te krijgen in de vereisten en beschik bare methoden voor bewaking 
