---
title: Containergroep handmatig stoppen of starten
description: Meer informatie over het handmatig stoppen of starten van een containergroep in Azure Container Instances.
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: c9f8afea33c65df940d02823ec394697d2786d6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74533429"
---
# <a name="manually-stop-or-start-containers-in-azure-container-instances"></a>Containers handmatig stoppen of starten in Azure Container Instances

Met [de beleidsinstelling opnieuw starten](container-instances-restart-policy.md) van een containergroep bepaalt u hoe containerexemplaren standaard worden gestart of gestopt. U de standaardinstelling overschrijven door een containergroep handmatig te stoppen of te starten.

## <a name="stop"></a>Stoppen

Handmatig een lopende containergroep stoppen - bijvoorbeeld door de opdracht [AZ-containerstop of Azure-portal][az-container-stop] te gebruiken. Voor bepaalde containerworkloads u een langlopende containergroep na een bepaalde periode stoppen om kosten te besparen. 

*Wanneer een containergroep de status Gestopt invoert, worden alle containers in de groep beëindigd en gerecycled. Het behoudt de containerstatus niet.*

Wanneer de containers worden gerecycled, worden de [resources](container-instances-container-groups.md#resource-allocation) toegewezen en stopt de facturering voor de containergroep.

De stopactie heeft geen effect als de containergroep al is beëindigd (is in een geslaagde of mislukte status). Een containergroep met run-once containertaken die is uitgevoerd, wordt bijvoorbeeld beëindigd in de status Geslaagd. Pogingen om de groep in die status te stoppen, veranderen de status niet. 

## <a name="start"></a>Starten

Wanneer een containergroep wordt gestopt - hetzij omdat de containers op zichzelf zijn beëindigd of omdat u de groep handmatig hebt gestopt - u de containers starten. Gebruik bijvoorbeeld de opdracht [start van az-container][az-container-start] of Azure-portal om de containers in de groep handmatig te starten. Als de containerafbeelding voor een container wordt bijgewerkt, wordt een nieuwe afbeelding getrokken. 

Het starten van een containergroep begint een nieuwe implementatie met dezelfde containerconfiguratie. Met deze actie u snel een bekende containergroepconfiguratie opnieuw gebruiken die werkt zoals u verwacht. U hoeft geen nieuwe containergroep te maken om dezelfde werkbelasting uit te voeren.

Alle containers in een containergroep worden door deze actie gestart. U geen specifieke container in de groep starten.

Nadat u een containergroep handmatig hebt gestart of opnieuw hebt opgestart, wordt de containergroep uitgevoerd volgens het geconfigureerde herstartbeleid.
  
## <a name="restart"></a>Opnieuw starten

U een containergroep opnieuw starten terwijl deze wordt uitgevoerd, bijvoorbeeld door de opdracht opnieuw opstarten van [az-containers te][az-container-restart] gebruiken. Met deze actie worden alle containers in de containergroep opnieuw gestart. Als de containerafbeelding voor een container wordt bijgewerkt, wordt een nieuwe afbeelding getrokken. 

Het opnieuw starten van een containergroep is handig wanneer u een implementatieprobleem wilt oplossen. Als een tijdelijke resourcebeperking bijvoorbeeld voorkomt dat uw containers succesvol worden uitgevoerd, kan het opnieuw opstarten van het probleem het probleem oplossen.

Alle containers in een containergroep worden door deze actie opnieuw gestart. U een specifieke container in de groep niet opnieuw starten.

Nadat u een containergroep handmatig opnieuw hebt opgestart, wordt de containergroep uitgevoerd volgens het geconfigureerde herstartbeleid.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het opnieuw opstarten van beleidsinstellingen](container-instances-restart-policy.md) in Azure Container Instances.

Naast het handmatig stoppen en starten van een containergroep met de bestaande configuratie, u de instellingen van een lopende containergroep [bijwerken.](container-instances-update.md)

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-restart]: /cli/azure/container?view=azure-cli-latest#az-container-restart
[az-container-start]: /cli/azure/container?view=azure-cli-latest#az-container-start
[az-container-stop]: /cli/azure/container?view=azure-cli-latest#az-container-stop
