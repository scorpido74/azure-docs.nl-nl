---
title: Ondersteunde Kubernetes-versies in Azure Kubernetes Service
description: Inzicht in het beleid voor ondersteuning van kubernetes-versie en levenscyclus van clusters in Azure Kubernetes Service (AKS)
services: container-service
author: sauryadas
ms.topic: article
ms.date: 12/09/2019
ms.author: saudas
ms.openlocfilehash: 77d64391888957a1697a5823a1485413686682d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593441"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Ondersteunde Kubernetes-versies in AKS (Azure Kubernetes Service)

De Kubernetes-community brengt ongeveer om de drie maanden kleine versies uit. Deze releases bevatten nieuwe functies en verbeteringen. Patch releases komen vaker voor (soms wekelijks) en zijn alleen bedoeld voor kritieke bug fixes in een secundaire versie. Deze patchreleases bevatten oplossingen voor beveiligingsproblemen of grote bugs die van invloed zijn op een groot aantal klanten en producten die in productie worden uitgevoerd op basis van Kubernetes.

AKS streeft ernaar om nieuwe Kubernetes-versies te certificeren en uit te brengen binnen 30 dagen na een upstream-release, afhankelijk van de stabiliteit van de release.

## <a name="kubernetes-versions"></a>Kubernetes-versies

Kubernetes maakt gebruik van het standaard [semantische versieschema.](https://semver.org/) Dit betekent dat elke versie van Kubernetes dit nummeringsschema volgt:

```
[major].[minor].[patch]

Example:
  1.12.14
  1.12.15
```

Elk nummer in de versie geeft algemene compatibiliteit met de vorige versie aan:

* Belangrijke versies veranderen wanneer incompatibele API-wijzigingen of backwards compatibility kunnen worden verbroken.
* Kleine versies veranderen wanneer functionaliteitswijzigingen worden aangebracht die achterwaarts compatibel zijn met de andere kleine versies.
* Patchversies veranderen wanneer achterwaarts compatibele bugfixes worden gemaakt.

Gebruikers moeten streven naar de nieuwste patch release van de secundaire versie die ze draaien, bijvoorbeeld als uw productie cluster is op *1.12.14* en *1.12.15* is de nieuwste beschikbare patch versie beschikbaar voor de *1.12* serie, moet u upgraden naar *1.12.15* zodra u in staat zijn om ervoor te zorgen uw cluster is volledig gepatcht en ondersteund.

## <a name="kubernetes-version-support-policy"></a>Ondersteuningsbeleid voor Kubernetes-versie

AKS ondersteunt drie kleine versies van Kubernetes:

* De huidige secundaire versie die wordt uitgebracht in AKS (N)
* Twee eerdere secundaire versies. Elke ondersteunde secundaire versie ondersteunt ook twee stabiele patches.

Dit staat bekend als "N-2": (N (Laatste release) - 2 (kleine versies)).

Als AKS vandaag *bijvoorbeeld 1.15.a* introduceert, wordt ondersteuning geboden voor de volgende versies:

Nieuwe secundaire versie    |    Lijst met ondersteunde versies
-----------------    |    ----------------------
1.15.a.               |    1.15.a, 1.15.b, 1.14.c, 1.14.d, 1.13.e, 1.13.f.

Waar ".letter" representatief is voor patchversies.

Zie 'Communicatie' hieronder voor meer informatie over communicatie over wijzigingen en verwachtingen in de versie.

Wanneer een nieuwe secundaire versie wordt geïntroduceerd, worden de oudste secundaire versie en patchreleases die worden ondersteund, afgeschaft en verwijderd. Als de huidige lijst met ondersteunde versies bijvoorbeeld:

```
1.15.a
1.15.b
1.14.c
1.14.d
1.13.e
1.13.f
```

En AKS releases 1.16. *, betekent dit dat de 1.13.* versies (alle 1.13 versies) worden verwijderd en zijn buiten de ondersteuning.

> [!NOTE]
> Houd er rekening mee dat als klanten een niet-ondersteunde Kubernetes-versie draaien, ze worden gevraagd om te upgraden wanneer ze ondersteuning voor het cluster aanvragen. Clusters met niet-ondersteunde Kubernetes-releases vallen niet onder het [AKS-ondersteuningsbeleid.](https://docs.microsoft.com/azure/aks/support-policies)

Naast het bovenstaande op kleine versies, ondersteunt AKS de twee nieuwste **patchreleases** van een bepaalde secundaire versie. Bijvoorbeeld, gezien de volgende ondersteunde versies:

```
Current Supported Version List
------------------------------
1.15.2, 1.15.1, 1.14.5, 1.14.4
```

Als upstream Kubernetes 1.15.3 en 1.14.6 heeft uitgebracht en AKS deze patchversies uitbrengt, worden de oudste patchversies afgeschaft en verwijderd en wordt de lijst met ondersteunde versies:

```
New Supported Version List
----------------------
1.15.*3*, 1.15.*2*, 1.14.*6*, 1.14.*5*
```

### <a name="communications"></a>Communicatie

* Voor nieuwe **kleine** versies van Kubernetes
  * Alle gebruikers worden publiekelijk op de hoogte gebracht van de nieuwe versie en welke versie wordt verwijderd.
  * Wanneer een nieuwe patchversie wordt uitgebracht, wordt de oudste patchrelease tegelijkertijd verwijderd.
  * Klanten hebben **30 dagen** vanaf de openbare meldingsdatum om te upgraden naar een ondersteunde secundaire versie.
* Voor nieuwe **patchversies** van Kubernetes
  * Alle gebruikers worden op de hoogte gesteld van de nieuwe patchversie die wordt uitgebracht en om te upgraden naar de nieuwste patchrelease.
  * Gebruikers hebben **30 dagen** om te upgraden naar een nieuwere, ondersteunde patchrelease voordat de oudste wordt verwijderd.

AKS definieert een "vrijgegeven versie" als de algemeen beschikbare versies, ingeschakeld in alle SLO / Quality of Service metingen en beschikbaar in alle regio's. AKS kan ook preview-versies ondersteunen die expliciet zijn gelabeld en onderworpen zijn aan de algemene voorwaarden van Preview.

#### <a name="notification-channels-for-aks-changes"></a>Meldingskanalen voor AKS-wijzigingen

AKS publiceert regelmatige service-updates die nieuwe Kubernetes-versies, servicewijzigingen en componentupdates samenvatten die zijn uitgebracht op de service op [GitHub.](https://github.com/Azure/AKS/releases)

Deze wijzigingen worden naar alle klanten gerold als onderdeel van regulier onderhoud dat wordt aangeboden als onderdeel van de beheerde service, sommige vereisen expliciete upgrades, terwijl andere geen actie vereisen.

Meldingen worden ook verzonden via:

* [AKS Release notes](https://aka.ms/aks/releasenotes)
* Meldingen in Azure-portal
* [Azure-updatekanaal][azure-update-channel]

### <a name="supported-versions-policy-exceptions"></a>Uitzonderingen op beleid voor ondersteunde versies

AKS behoudt zich het recht voor om nieuwe/bestaande versies toe te voegen of te verwijderen waarvan is vastgesteld dat deze zonder voorafgaande kennisgeving een of meer kritieke productieproblemen hebben die van invloed zijn op bugs of beveiligingsproblemen.

Specifieke patchreleases kunnen worden overgeslagen of de implementatie worden versneld, afhankelijk van de ernst van de bug of het beveiligingsprobleem.

### <a name="azure-portal-and-cli-default-versions"></a>Standaardversies Azure-portal en CLI

Wanneer u een AKS-cluster implementeert in de portal of met de Azure CLI, wordt het cluster standaard ingesteld op de N-1-secundaire versie en de nieuwste patch. Als AKS bijvoorbeeld *1.15.a*, *1.15.b*, *1.14.c*, *1.14.d*, *1.13.e*, en *1.13.f*ondersteunt , is de geselecteerde standaardversie *1.14.c*.

AKS kiest standaard n-1 om klanten standaard een bekende, stabiele en gepatchte versie te bieden.

## <a name="list-currently-supported-versions"></a>Lijst met momenteel ondersteunde versies

Als u wilt weten welke versies momenteel beschikbaar zijn voor uw abonnement en regio, gebruikt u de opdracht [az aks-versieversies.][az-aks-get-versions] In het volgende voorbeeld worden de beschikbare Kubernetes-versies voor de *EastUS-regio* weergegeven:

```azurecli-interactive
az aks get-versions --location eastus --output table
```

## <a name="faq"></a>Veelgestelde vragen

**Wat gebeurt er als een klant een Kubernetes-cluster upgradet met een secundaire versie die niet wordt ondersteund?**

Als u op de *n-3* versie, bent u buiten de ondersteuning en zal worden gevraagd om te upgraden. Als uw upgrade van versie n-3 naar n-2 slaagt, bent u nu binnen ons ondersteuningsbeleid. Bijvoorbeeld:

- Als de oudste ondersteunde AKS-versie *1.13.a* is en u op *1.12.b* of ouder bent, bent u buiten de ondersteuning.
- Als de upgrade van *1.12.b* naar *1.13.a* of hoger slaagt, bent u terug binnen ons ondersteuningsbeleid.

Upgrades naar versies die ouder zijn dan het ondersteunde venster van *N-2* worden niet ondersteund. In dergelijke gevallen raden we klanten aan nieuwe AKS-clusters te maken en hun workloads opnieuw te implementeren met versies in het ondersteunde venster.

**Wat betekent 'Buiten ondersteuning'**

'Buiten ondersteuning' betekent dat de versie die u uitvoert buiten de lijst met ondersteunde versies valt en dat u wordt gevraagd om het cluster te upgraden naar een ondersteunde versie wanneer u ondersteuning aanvraagt. Bovendien maakt AKS geen runtime of andere garanties voor clusters buiten de lijst met ondersteunde versies.

**Wat gebeurt er als een klant een Kubernetes-cluster opschaalt met een secundaire versie die niet wordt ondersteund?**

Voor secundaire versies die niet door AKS worden ondersteund, moet het in- of uitchecken blijven werken, maar het wordt ten zeerste aanbevolen om te upgraden om uw cluster weer in ondersteuning te brengen.

**Kan een klant voor altijd op een Kubernetes-versie blijven?**

Ja. Als het cluster zich echter niet op een van de door AKS ondersteunde versies bevindt, is het cluster buiten het AKS-ondersteuningsbeleid. Azure upgradet uw cluster niet automatisch of verwijdert het niet automatisch.

**Welke versie ondersteunt het besturingsvlak als de knooppuntgroep zich niet in een van de ondersteunde AKS-versies bevindt?**

Het besturingsvlak moet zich binnen een venster van versies van alle knooppuntpools bevindt. Voor meer informatie over het upgraden van het controlevlak of knooppuntzwembaden, raadpleegt u documentatie over [het upgraden van knooppuntzwembaden.](use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools)

## <a name="next-steps"></a>Volgende stappen

Zie [Een AKS-cluster (Azure Kubernetes Service) upgraden][aks-upgrade]voor informatie over het upgraden van uw cluster.

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
