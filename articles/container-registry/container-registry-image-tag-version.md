---
title: Aanbevolen procedures voor afbeeldingstag
description: Aanbevolen procedures voor het taggen en versien van Docker-containerafbeeldingen bij het pushen van afbeeldingen naar en het trekken van afbeeldingen uit een Azure-containerregister
author: stevelasker
ms.topic: article
ms.date: 07/10/2019
ms.author: stevelas
ms.openlocfilehash: b483317960409fe1fbea181706f12375606fe659
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445735"
---
# <a name="recommendations-for-tagging-and-versioning-container-images"></a>Aanbevelingen voor het taggen en versien van containerafbeeldingen

Wanneer u containerafbeeldingen naar een containerregister pusht en deze vervolgens implementeert, hebt u een strategie nodig voor het taggen en versien van afbeeldingen. In dit artikel worden twee benaderingen besproken en waar elk past tijdens de levenscyclus van de container:

* **Stabiele tags** - Tags die u opnieuw gebruikt, bijvoorbeeld om een grote of secundaire versie aan te geven, zoals *mycontainerimage:1.0*.
* **Unieke tags** - Een andere tag voor elke afbeelding die u naar een register pusht, zoals *mycontainerimage:abc123*.

## <a name="stable-tags"></a>Stabiele tags

**Aanbeveling:** Gebruik stabiele tags om **basisafbeeldingen** voor uw containerbuilds te behouden. Vermijd implementaties met stabiele tags, omdat deze tags updates blijven ontvangen en inconsistenties in productieomgevingen kunnen introduceren.

*Stabiele tags* betekenen dat een ontwikkelaar, of een buildsysteem, een specifieke tag kan blijven trekken, die nog steeds updates krijgt. Stabiel betekent niet dat de inhoud bevroren is. Integendeel, stabiel impliceert dat het beeld stabiel moet zijn voor de bedoeling van die versie. Om "stabiel" te blijven, kan het worden onderhouden om beveiligingspatches of framework-updates toe te passen.

### <a name="example"></a>Voorbeeld

Een framework team verzendt versie 1.0. Ze weten dat ze updates verzenden, inclusief kleine updates. Om stabiele tags voor een bepaalde grote en kleine versie te ondersteunen, hebben ze twee sets stabiele tags.

* `:1`– een stabiele tag voor de belangrijkste versie. `1`vertegenwoordigt de "nieuwste" of "nieuwste" 1.* versie.
* `:1.0`- een stabiele tag voor versie 1.0, waardoor een ontwikkelaar zich kan binden aan updates van 1.0 en niet naar 1.1 kan worden gerold wanneer deze wordt uitgebracht.

Het team maakt `:latest` ook gebruik van de tag, die wijst op de nieuwste stabiele tag, ongeacht wat de huidige grote versie is.

Wanneer basisafbeeldingsupdates beschikbaar zijn, of een type onderhoudsversie van het framework, worden afbeeldingen met de stabiele tags bijgewerkt naar de nieuwste samenvatting die de meest recente stabiele versie van die versie vertegenwoordigt.

In dit geval worden zowel de belangrijkste als de kleine tags voortdurend onderhouden. Vanuit een basisafbeeldingsscenario kan de eigenaar van de afbeelding serviced images leveren.

### <a name="delete-untagged-manifests"></a>Niet-gecodeerde manifesten verwijderen

Als een afbeelding met een stabiele tag wordt bijgewerkt, wordt de eerder gelabelde afbeelding niet getagd, wat resulteert in een zwevende afbeelding. De manifest- en unieke laaggegevens van de vorige afbeelding blijven in het register. Als u uw registergrootte wilt behouden, u ongelabelde manifesten die afkomstig zijn van stabiele afbeeldingsupdates periodiek verwijderen. U bijvoorbeeld niet-gelabelde manifesten die ouder zijn dan een bepaalde duur automatisch verwijderen of een [bewaarbeleid](container-registry-retention-policy.md) instellen voor [niet-gelabelde](container-registry-auto-purge.md) manifesten.

## <a name="unique-tags"></a>Unieke tags

**Aanbeveling:** Gebruik unieke tags voor **implementaties,** vooral in een omgeving die kan worden geschaald op meerdere knooppunten. U wilt waarschijnlijk opzettelijke implementaties van een consistente versie van componenten. Als uw container opnieuw wordt opgestart of als een orchestrator meer exemplaren schaalt, zullen uw hosts niet per ongeluk een nieuwere versie trekken, in strijd met de andere knooppunten.

Unieke tagging betekent gewoon dat elke afbeelding geduwd naar een register heeft een unieke tag. Tags worden niet opnieuw gebruikt. Er zijn verschillende patronen die u volgen om unieke tags te genereren, waaronder:

* **Datum-tijdstempel** - Deze aanpak komt vrij vaak voor, omdat u duidelijk zien wanneer de afbeelding is gebouwd. Maar, hoe het te correleren terug naar uw build-systeem? Moet je de build vinden die tegelijkertijd werd voltooid? In welke tijdzone zit je? Zijn al uw bouwsystemen gekalibreerd op UTC?
* **Git commit** – Deze aanpak werkt totdat je beginnen met het ondersteunen van basisafbeeldingsupdates. Als er een basisafbeeldingsupdate plaatsvindt, start je buildsysteem met dezelfde Git commit als de vorige build. De basisafbeelding heeft echter nieuwe inhoud. Over het algemeen biedt een Git commit een *semi-stabiele*tag.
* **Manifest digest** - Elke containerafbeelding die naar een containerregister wordt gepusht, wordt geassocieerd met een manifest, geïdentificeerd door een unieke SHA-256-hash of digest. Hoewel uniek, de samenvatting is lang, moeilijk te lezen, en niet gecorreleerd met uw gebouwde omgeving.
* **Build ID** - Deze optie kan het beste zijn omdat het waarschijnlijk incrementeel is, en het stelt u in staat om terug te correleren naar de specifieke build om alle artefacten en logboeken te vinden. Echter, als een duidelijke verteren, kan het moeilijk zijn voor een mens om te lezen.

  Als uw organisatie meerdere buildsystemen heeft, is het vooraf bevestigen van `<build-system>-<build-id>`de tag met de naam van het buildsysteem een variatie op deze optie: . U bijvoorbeeld builds onderscheiden van het Jenkins-buildsysteem van het API-team en het Azure Pipelines-buildsysteem van het webteam.

### <a name="lock-deployed-image-tags"></a>Geïmplementeerde afbeeldingstags vergrendelen

Als aanbevolen toepassing raden we u aan een geïmplementeerde afbeeldingstag [te vergrendelen](container-registry-image-lock.md) door het `write-enabled` kenmerk in te stellen op `false`. Deze praktijk voorkomt dat u per ongeluk een afbeelding uit het register verwijdert en mogelijk uw implementaties verstoort. U de vergrendelingsstap opnemen in uw releasepijplijn.

Als u een geïmplementeerde afbeelding vergrendelt, u nog steeds andere, niet-geïmplementeerde afbeeldingen uit uw register verwijderen met behulp van Azure Container Registry-functies om uw register bij te houden. Verwijder bijvoorbeeld niet-gelabelde manifesten of ontgrendelde afbeeldingen die ouder zijn dan een bepaalde duur, of stel een [bewaarbeleid](container-registry-retention-policy.md) in voor [niet-gecodeerde](container-registry-auto-purge.md) manifesten.

## <a name="next-steps"></a>Volgende stappen

Voor een meer gedetailleerde bespreking van de concepten in dit artikel, zie de blogpost [Docker Tagging: Best practices voor tagging en versioning docker afbeeldingen](https://stevelasker.blog/2018/03/01/docker-tagging-best-practices-for-tagging-and-versioning-docker-images/).

Zie Aanbevolen procedures voor Azure Container Registry om de prestaties en het kosteneffectieve gebruik van uw Azure-containerregister te [maximaliseren.](container-registry-best-practices.md)

<!-- IMAGES -->


<!-- LINKS - Internal -->

