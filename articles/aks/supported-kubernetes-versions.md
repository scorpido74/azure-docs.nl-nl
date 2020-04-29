---
title: Ondersteunde Kubernetes-versies in Azure Kubernetes Service
description: Meer informatie over het ondersteunings beleid voor Kubernetes-versies en de levens cyclus van clusters in azure Kubernetes service (AKS)
services: container-service
author: sauryadas
ms.topic: article
ms.date: 12/09/2019
ms.author: saudas
ms.openlocfilehash: 77d64391888957a1697a5823a1485413686682d1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77593441"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Ondersteunde Kubernetes-versies in AKS (Azure Kubernetes Service)

De Kubernetes-Community brengt ongeveer elke drie maanden secundaire versies uit. Deze releases bevatten nieuwe functies en verbeteringen. Patch releases zijn vaker (soms wekelijks) en zijn alleen bedoeld voor essentiële probleem oplossingen in een secundaire versie. Deze patches bevatten oplossingen voor beveiligings problemen of grote fouten die van invloed zijn op een groot aantal klanten en producten die worden uitgevoerd op productie op basis van Kubernetes.

AKS streeft ernaar nieuwe Kubernetes-versies te certificeren en vrij te geven binnen 30 dagen na een upstream-release, afhankelijk van de stabiliteit van de release.

## <a name="kubernetes-versions"></a>Kubernetes-versies

Kubernetes maakt gebruik van het standaard versie beheer schema van [semantische versie](https://semver.org/) . Dit betekent dat elke versie van Kubernetes dit Nummerings schema volgt:

```
[major].[minor].[patch]

Example:
  1.12.14
  1.12.15
```

Elk nummer in de versie geeft algemene compatibiliteit met de vorige versie aan:

* Primaire versies worden gewijzigd wanneer incompatibele API-wijzigingen of achterwaartse compatibiliteit mogelijk wordt verbroken.
* Secundaire versies worden gewijzigd wanneer er wijzigingen in de functionaliteit worden aangebracht die achterwaarts compatibel zijn met de andere secundaire releases.
* Patch versies worden gewijzigd wanneer er achterwaarts compatibele fout oplossingen worden uitgevoerd.

Gebruikers moeten zich richten op het uitvoeren van de nieuwste patch release van de secundaire versie die ze uitvoeren, bijvoorbeeld als uw productie cluster op *1.12.14* is en *1.12.15* de meest recente beschik bare patch versie is die beschikbaar is voor de *1,12* -serie, moet u een upgrade uitvoeren naar *1.12.15* zodra u zeker weet dat uw cluster volledig wordt gerepareerd en ondersteund.

## <a name="kubernetes-version-support-policy"></a>Ondersteunings beleid voor Kubernetes-versie

AKS ondersteunt drie secundaire versies van Kubernetes:

* De huidige secundaire versie die wordt uitgebracht in AKS (N)
* Twee vorige secundaire versies. Elke ondersteunde secundaire versie biedt ook ondersteuning voor twee stabiele patches.

Dit staat bekend als ' N-2 ': (N (meest recente versie)-2 (secundaire versies)).

Als bijvoorbeeld AKS introduceert *1.15. a* vandaag, wordt ondersteuning geboden voor de volgende versies:

Nieuwe secundaire versie    |    Lijst met ondersteunde versies
-----------------    |    ----------------------
1.15. a               |    1.15. a, 1.15. b, 1.14. c, 1.14. d, 1.13. e, 1.13. f

Waarbij '. letter ' representatief is voor patch versies.

Zie ' communicatie ' hieronder voor meer informatie over de communicatie met versie wijzigingen en verwachtingen.

Wanneer een nieuwe secundaire versie wordt geïntroduceerd, worden de oudste secundaire versie-en patch releases die worden ondersteund, afgeschaft en verwijderd. Als de huidige lijst met ondersteunde versies bijvoorbeeld:

```
1.15.a
1.15.b
1.14.c
1.14.d
1.13.e
1.13.f
```

En AKS Releases 1,16. *Dit betekent dat de 1,13.* versies (alle 1,13 versies) worden verwijderd en worden niet meer ondersteund.

> [!NOTE]
> Houd er rekening mee dat als klanten een niet-ondersteunde Kubernetes-versie uitvoeren, wordt gevraagd om een upgrade uit te voeren bij het aanvragen van ondersteuning voor het cluster. Clusters met niet-ondersteunde Kubernetes-releases vallen niet onder het [beleid voor AKS-ondersteuning](https://docs.microsoft.com/azure/aks/support-policies).

Naast het bovenstaande in secundaire versies ondersteunt AKS ook de twee nieuwste **patch** releases van een bepaalde secundaire versie. Bijvoorbeeld, met de volgende ondersteunde versies:

```
Current Supported Version List
------------------------------
1.15.2, 1.15.1, 1.14.5, 1.14.4
```

Als de Kubernetes van 1.15.3 en 1.14.6 en AKS van de upstream-versie van deze patch versies worden vrijgegeven, worden de oudste patch versies afgeschaft en verwijderd, en wordt de lijst met ondersteunde versies:

```
New Supported Version List
----------------------
1.15.*3*, 1.15.*2*, 1.14.*6*, 1.14.*5*
```

### <a name="communications"></a>Communicatie

* Voor nieuwe **secundaire** versies van Kubernetes
  * Alle gebruikers worden op de hoogte gesteld van de nieuwe versie en de versie die wordt verwijderd.
  * Wanneer een nieuwe patch versie wordt uitgebracht, wordt de oudste patch release op hetzelfde moment verwijderd.
  * Klanten hebben **30 dagen** na de datum van de open bare melding voor een upgrade naar een ondersteunde versie van de secundaire release.
* Voor nieuwe **patch** versies van Kubernetes
  * Alle gebruikers ontvangen een melding dat de nieuwe patch versie wordt vrijgegeven en een upgrade naar de nieuwste patch release.
  * Gebruikers hebben **30 dagen** een upgrade naar een nieuwere, ondersteunde patch release voordat de oudste wordt verwijderd.

AKS definieert een ' vrijgegeven versie ' als de algemeen beschik bare versies, ingeschakeld in alle ondergeschikten voor SLO/Quality of Service en beschikbaar in alle regio's. AKS kan ook ondersteuning bieden voor Preview-versies die expliciet zijn voorzien van een voor beeld van voor waarden en bepalingen.

#### <a name="notification-channels-for-aks-changes"></a>Meldings kanalen voor AKS-wijzigingen

AKS publiceert reguliere service-updates die een overzicht van nieuwe Kubernetes-versies, service wijzigingen en onderdeel updates die zijn uitgebracht voor de service op [github](https://github.com/Azure/AKS/releases).

Deze wijzigingen worden naar alle klanten gedistribueerd als onderdeel van het regel matig onderhoud dat wordt aangeboden als onderdeel van de beheerde service, wat expliciete upgrades vereisen, terwijl anderen geen actie nodig hebben.

Meldingen worden ook verzonden via:

* [Release opmerkingen bij AKS](https://aka.ms/aks/releasenotes)
* Meldingen in Azure-portal
* [Azure update-kanaal][azure-update-channel]

### <a name="supported-versions-policy-exceptions"></a>Ondersteunde versies beleids uitzonderingen

AKS behoudt zich het recht voor om nieuwe/bestaande versies toe te voegen of te verwijderen die zijn geïdentificeerd om een of meer kritieke productie problemen op te lossen, zonder voorafgaande kennisgeving.

Specifieke patch releases kunnen worden overgeslagen of de implementatie wordt versneld, afhankelijk van de ernst van de fout of het beveiligings probleem.

### <a name="azure-portal-and-cli-default-versions"></a>Standaard versies van Azure Portal en CLI

Wanneer u een AKS-cluster implementeert in de portal of met de Azure CLI, wordt het cluster standaard gedistribueerd naar de N-1-secundaire versie en de meest recente patch. Als AKS bijvoorbeeld *1.15. a*, *1.15. b*, *1.14. c*, *1.14. d*, *1.13. e*en *1.13. f*ondersteunt, is de geselecteerde standaard versie *1.14. c*.

AKS kiest de standaard waarde van N-1 om klanten standaard een bekende, stabiele en patched versie te bieden.

## <a name="list-currently-supported-versions"></a>Momenteel ondersteunde versies weer geven

Gebruik de opdracht [AZ AKS Get-verse][az-aks-get-versions] voor meer informatie over welke versies momenteel beschikbaar zijn voor uw abonnement en regio. In het volgende voor beeld ziet u een lijst met de beschik bare Kubernetes-versies voor de regio *oostus* :

```azurecli-interactive
az aks get-versions --location eastus --output table
```

## <a name="faq"></a>Veelgestelde vragen

**Wat gebeurt er wanneer een klant een Kubernetes-cluster upgradet met een secundaire versie die niet wordt ondersteund?**

Als u een *n-3-* versie hebt, hebt u geen ondersteuning meer en wordt u gevraagd om te upgraden. Als uw upgrade van versie n-3 naar n-2 slaagt, bent u nu in ons ondersteunings beleid. Bijvoorbeeld:

- Als de oudste ondersteunde AKS-versie *1.13 is. a* en u zich op *1.12. b* of ouder bevindt, hebt u geen ondersteuning meer.
- Als de upgrade van *1.12. b* naar *1.13. a* of hoger is geslaagd, bent u terug in het ondersteunings beleid.

Upgrades naar oudere versies dan het ondersteunde venster van *N-2* worden niet ondersteund. In dergelijke gevallen raden klanten aan om nieuwe AKS-clusters te maken en hun workloads opnieuw te implementeren met versies in het ondersteunde venster.

**Wat betekent ' buiten het ondersteunings team '**

' Buiten het ondersteunings team ' betekent dat de versie die u uitvoert, zich buiten de lijst met ondersteunde versies bevindt en u wordt gevraagd het cluster bij te werken naar een ondersteunde versie bij het aanvragen van ondersteuning. Daarnaast maakt AKS geen runtime of andere garanties voor clusters buiten de lijst met ondersteunde versies.

**Wat gebeurt er wanneer een klant een Kubernetes-cluster schaalt met een secundaire versie die niet wordt ondersteund?**

Voor secundaire versies die niet worden ondersteund door AKS, moet u de schaal in-of uitschalen, maar het wordt ten zeerste aanbevolen om een upgrade uit te voeren om uw cluster weer in de ondersteuning te brengen.

**Kan een klant permanent blijven beschikken over een Kubernetes-versie?**

Ja. Als het cluster echter niet is opgenomen in een van de versies die worden ondersteund door AKS, heeft het cluster geen ondersteuning meer voor het AKS-ondersteunings beleid. Uw cluster wordt niet automatisch bijgewerkt of verwijderd door Azure.

**Welke versie van het besturings element wordt ondersteund als de knooppunt groep zich niet in een van de ondersteunde AKS-versies bevindt?**

Het besturings vlak moet zich in een venster met versies van alle knooppunt groepen bevinden. Ga voor meer informatie over het bijwerken van het besturings vlak of de knooppunt groepen naar documentatie over het [upgraden van knooppunt groepen](use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools).

## <a name="next-steps"></a>Volgende stappen

Zie [een Azure Kubernetes service-cluster (AKS) bijwerken][aks-upgrade]voor meer informatie over het upgraden van uw cluster.

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
