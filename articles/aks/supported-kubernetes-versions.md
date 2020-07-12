---
title: Ondersteunde Kubernetes-versies in Azure Kubernetes Service
description: Meer informatie over het ondersteunings beleid voor Kubernetes-versies en de levens cyclus van clusters in azure Kubernetes service (AKS)
services: container-service
ms.topic: article
ms.date: 07/08/2020
author: palma21
ms.author: jpalma
ms.openlocfilehash: 886e6cf237df94c056ec7c592e0b535327339871
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/11/2020
ms.locfileid: "86243793"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Ondersteunde Kubernetes-versies in AKS (Azure Kubernetes Service)

De Kubernetes-Community brengt ongeveer elke drie maanden secundaire versies uit. Deze releases bevatten nieuwe functies en verbeteringen. Patch releases zijn vaker (soms wekelijks) en zijn alleen bedoeld voor essentiële probleem oplossingen in een secundaire versie. Deze patches bevatten oplossingen voor beveiligings problemen of grote fouten.

## <a name="kubernetes-versions"></a>Kubernetes-versies

Kubernetes maakt gebruik van het standaard versie beheer schema van [semantische versie](https://semver.org/) , wat betekent dat elke versie van Kubernetes dit Nummerings schema volgt:

```
[major].[minor].[patch]

Example:
  1.17.7
  1.17.8
```

Elk nummer in de versie geeft algemene compatibiliteit met de vorige versie aan:

* Primaire versies worden gewijzigd wanneer incompatibele API-wijzigingen of achterwaartse compatibiliteit mogelijk wordt verbroken.
* Secundaire versies worden gewijzigd wanneer er wijzigingen in de functionaliteit worden aangebracht die achterwaarts compatibel zijn met de andere secundaire releases.
* Patch versies worden gewijzigd wanneer er achterwaarts compatibele fout oplossingen worden uitgevoerd.

Gebruikers moeten zich richten op het uitvoeren van de meest recente patch versie van de secundaire versie die ze uitvoeren, bijvoorbeeld als uw productie cluster is ingeschakeld **`1.17.7`** en **`1.17.8`** de meest recente beschik bare patch versie beschikbaar is voor de *1,17* -serie. u moet zo snel mogelijk een upgrade naar uitvoeren, zodat u **`1.17.8`** zeker weet dat uw cluster volledig wordt gerepareerd en ondersteund.

## <a name="kubernetes-version-support-policy"></a>Ondersteunings beleid voor Kubernetes-versie

AKS definieert een algemeen beschik bare versie, als een versie die is ingeschakeld in alle SLO-of SLA-metingen en wanneer deze beschikbaar zijn in alle regio's. AKS ondersteunt drie GA secundaire versies van Kubernetes:

* De meest recente GA secundaire versie die is uitgebracht in AKS (waarnaar wordt verwezen als N). 
* Twee vorige secundaire versies. 
* Elke ondersteunde secundaire versie ondersteunt ook Maxi maal twee (2) stabiele patches.
* AKS kan ook ondersteuning bieden voor Preview-versies, die expliciet worden gelabeld en onderworpen aan de voor waarden van de [Preview][preview-terms]-versie.

> [!NOTE]
> AKS maakt gebruik van veilige implementatie procedures waarbij een geleidelijke implementatie van regio's betrokken is. Dit betekent dat het Maxi maal 10 werk dagen kan duren voordat een nieuwe release of een nieuwe versie beschikbaar is in alle regio's.

Het ondersteunde venster van Kubernetes-versies op AKS staat bekend als ' N-2 ': (N (meest recente versie)-2 (secundaire versies)).

Als bijvoorbeeld AKS introduceert *1.17. a* vandaag, wordt ondersteuning geboden voor de volgende versies:

Nieuwe secundaire versie    |    Lijst met ondersteunde versies
-----------------    |    ----------------------
1.17. a               |    1.17. a, 1.17. b, 1.16. c, 1.16. d, 1.15. e, 1.15. f

Waarbij '. letter ' representatief is voor patch versies.

Wanneer een nieuwe secundaire versie wordt geïntroduceerd, worden de oudste secundaire versie-en patch releases die worden ondersteund, afgeschaft en verwijderd. Als de huidige lijst met ondersteunde versies bijvoorbeeld:

```
1.17.a
1.17.b
1.16.c
1.16.d
1.15.e
1.15.f
```

En AKS releases van 1,18. \* , betekent dit dat alle 1,15. \* versies worden verwijderd en in 30 dagen niet meer worden ondersteund.

> [!NOTE]
> Houd er rekening mee dat als klanten een niet-ondersteunde Kubernetes-versie uitvoeren, wordt gevraagd om een upgrade uit te voeren bij het aanvragen van ondersteuning voor het cluster. Clusters met niet-ondersteunde Kubernetes-releases vallen niet onder het [beleid voor AKS-ondersteuning](./support-policies.md).

Naast het bovenstaande, ondersteunt AKS Maxi maal twee **patch** releases van een bepaalde secundaire versie. Hiertoe worden de volgende ondersteunde versies vermeld:

```
Current Supported Version List
------------------------------
1.17.8, 1.17.7, 1.16.10, 1.16.9
```

Als AKS wordt vrijgegeven `1.17.9` en `1.16.11` , worden de oudste patch versies afgeschaft en verwijderd, en wordt de lijst met ondersteunde versies:

```
New Supported Version List
----------------------
1.17.*9*, 1.17.*8*, 1.16.*11*, 1.16.*10*
```

## <a name="release-and-deprecation-process"></a>Vrijgave-en afschaffing proces

U kunt naar aanstaande release-en afschaffing-versies van de [AKS-Kubernetes](#aks-kubernetes-release-calendar).

Voor nieuwe **secundaire** versies van Kubernetes
1. AKS publiceert een pre-aankondiging met de geplande datum van de release van een nieuwe versie en de definitieve afschaffing van een oude versie in de opmerkingen bij de [AKS-release](https://aka.ms/aks/releasenotes) ten minste 30 dagen vóór de verwijdering.
2. AKS publiceert een [service status melding](../service-health/service-health-overview.md) die beschikbaar is voor alle gebruikers met toegang tot AKS en Portal en stuurt een e-mail naar de abonnements beheerders met de geplande versie van de verwijderings datum.
3. Gebruikers beschikken over **30 dagen** vanaf het verwijderen van de versie om een upgrade naar een ondersteunde versie van de secundaire release te kunnen blijven ontvangen.

Voor nieuwe **patch** versies van Kubernetes
  * Vanwege de urgente aard van patch versies kunnen deze in de service worden geïntroduceerd zodra deze beschikbaar komen.
  * Over het algemeen doet AKS geen brede communicatie voor de release van nieuwe patch versies. De beschik bare CVE-patches worden door AKS voortdurend gecontroleerd en gevalideerd om ze in AKS op tijd te ondersteunen. Als er een kritieke patch wordt gevonden of als de gebruiker actie vereist is, waarschuwt AKS gebruikers om een upgrade uit te dienen naar de nieuwe beschik bare patch.
  * Gebruikers hebben **30 dagen** vanaf het moment dat een patch release wordt verwijderd van AKS om een patch te installeren naar een ondersteunde update en de ondersteuning te blijven ontvangen.

### <a name="supported-versions-policy-exceptions"></a>Ondersteunde versies beleids uitzonderingen

AKS behoudt zich het recht voor om nieuwe/bestaande versies toe te voegen of te verwijderen die zijn geïdentificeerd om een of meer kritieke productie problemen op te lossen, zonder voorafgaande kennisgeving.

Specifieke patch releases kunnen worden overgeslagen of de implementatie wordt versneld, afhankelijk van de ernst van de fout of het beveiligings probleem.

## <a name="azure-portal-and-cli-versions"></a>Azure Portal-en CLI-versies

Wanneer u een AKS-cluster implementeert in de portal of met de Azure CLI, wordt het cluster standaard gedistribueerd naar de N-1-secundaire versie en de meest recente patch. Als AKS bijvoorbeeld *1.17. a*, *1.17. b*, *1.16. c*, *1.16. d*, *1.15. e*en *1.15. f*ondersteunt, is de geselecteerde standaard versie *1.16. c*.

Gebruik de opdracht [AZ AKS Get-verse][az-aks-get-versions] voor meer informatie over welke versies momenteel beschikbaar zijn voor uw abonnement en regio. In het volgende voor beeld ziet u een lijst met de beschik bare Kubernetes-versies voor de regio *oostus* :

```azurecli-interactive
az aks get-versions --location eastus --output table
```


## <a name="aks-kubernetes-release-calendar"></a>Release kalender voor AKS-Kubernetes

Zie [hier](https://en.wikipedia.org/wiki/Kubernetes#History)voor de eerdere release geschiedenis.

|  K8s-versie | Upstream-release  | AKS preview  | AKS GA  | Einde van de levens duur |
|--------------|-------------------|--------------|---------|-------------|
| 1,17  | Dec-09-19  | 19 januari   | Jul 20  | 1,20 GA | 
| 1,18  | Mrt-23-20  | 20 mei   | Aug 20  | 1,21 GA | 
| 1,19  | Aug-04-20  | Aug 20   | 20 nov  | 1,22 GA | 
| 1.20  | * Nov 20    | * Dec 21   | * Jan 21  | 1,23 GA | 

\*Bevestiging van release datum van de upstream in behandeling.

## <a name="faq"></a>Veelgestelde vragen

**Wat gebeurt er wanneer een gebruiker een Kubernetes-cluster upgradet met een secundaire versie die niet wordt ondersteund?**

Als u een *n-3-* versie of ouder hebt, betekent dit dat u buiten de ondersteuning bent en wordt u gevraagd om te upgraden. Als uw upgrade van versie n-3 naar n-2 slaagt, wordt u teruggeleid binnen het ondersteunings beleid. Bijvoorbeeld:

- Als de oudste ondersteunde AKS-versie *1.15. a* is en u zich op *1.14. b* of ouder bevindt, hebt u geen ondersteuning meer.
- Wanneer de upgrade van *1.14. b* naar *1.15. a* is geslaagd, bent u terug in het ondersteunings beleid.

Downgrades worden niet ondersteund.

**Wat betekent ' buiten het ondersteunings team '**

' Buiten het ondersteunings team ' betekent dat de versie die u uitvoert, zich buiten de lijst met ondersteunde versies bevindt en u wordt gevraagd het cluster bij te werken naar een ondersteunde versie bij het aanvragen van ondersteuning, tenzij u binnen de respijt periode van 30 dagen na versie afschaffing bent. Daarnaast doen AKS geen runtime of andere garanties voor clusters buiten de lijst met ondersteunde versies.

**Wat gebeurt er wanneer een gebruiker een Kubernetes-cluster schaalt met een secundaire versie die niet wordt ondersteund?**

Voor secundaire versies die niet worden ondersteund door AKS, kan het schalen in-of uitgaan werken, maar er zijn geen Quality of Service garanties. Daarom is het raadzaam om een upgrade uit te voeren om uw cluster weer in te zetten.

**Kan een gebruiker een Kubernetes-versie permanent blijven?**

Als een cluster geen ondersteuning meer heeft voor meer dan drie (3) secundaire versies en er is vastgesteld dat er beveiligings Risico's zijn, kunt u met Azure contact met u opdoen om uw cluster proactief bij te werken. Als u geen verdere actie onderneemt, behoudt Azure het recht om uw cluster automatisch namens u bij te werken.

**Welke versie van het besturings element wordt ondersteund als de knooppunt groep zich niet in een van de ondersteunde AKS-versies bevindt?**

Het besturings vlak moet zich in een venster met versies van alle knooppunt groepen bevinden. Ga voor meer informatie over het bijwerken van het besturings vlak of de knooppunt groepen naar documentatie over het [upgraden van knooppunt groepen](use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools).

**Kan ik een versie overs Laan bij een upgrade?**

Nee, het volgen van de aanbevolen kubernetes-AKS biedt alleen ondersteuning voor upgrades naar onmiddellijk volgende patch of secundaire versie. In de Azure Portal worden alleen de versies weer gegeven die u kunt upgraden naar en op de CLI die u kunt uitvoeren `az aks get-upgrades -n MyAKSCluster -g MyResourceGroup` om de beschik bare upgrades van uw huidige versie te zien.

**Hoe kan ik een upgrade uitvoeren naar een ondersteunde versie als ik meerdere versies achter de nieuwste ondersteunde versie heb?**

Om binnen de ondersteuning te blijven, moet u voor komen dat u achter meerdere versies van de momenteel ondersteunde lijst valt, maar als u in deze situatie AKS, wordt de upgrade naar de mini maal ondersteunde versie altijd toegestaan.

## <a name="next-steps"></a>Volgende stappen

Zie [een Azure Kubernetes service-cluster (AKS) bijwerken][aks-upgrade]voor meer informatie over het upgraden van uw cluster.

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
[preview-terms]: /support/legal/preview-supplemental-terms
