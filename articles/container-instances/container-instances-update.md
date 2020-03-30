---
title: Containergroep bijwerken
description: Meer informatie over het bijwerken van lopende containers in uw containergroepen voor Azure Container Instances.
ms.topic: article
ms.date: 09/03/2019
ms.openlocfilehash: f57ebcf050b5563b45f10af57c1721338df88ff9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74533305"
---
# <a name="update-containers-in-azure-container-instances"></a>Containers in Azure Container Instances bijwerken

Tijdens de normale werking van uw containerexemplaren kan het nodig zijn om de lopende containers in een containergroep bij te [werken.](container-instances-container-groups.md) U bijvoorbeeld de afbeeldingsversie bijwerken, een DNS-naam wijzigen, omgevingsvariabelen bijwerken of de status van een container vernieuwen waarvan de toepassing is gecrasht.

> [!NOTE]
> Beëindigde of verwijderde containergroepen kunnen niet worden bijgewerkt. Zodra een containergroep is beëindigd (is geslaagd of mislukt) of is verwijderd, moet de groep als nieuw worden geïmplementeerd.

## <a name="update-a-container-group"></a>Een containergroep bijwerken

Werk de containers in een lopende containergroep bij door een bestaande groep opnieuw te implementeren met ten minste één gewijzigde eigenschap. Wanneer u een containergroep bijwerkt, worden alle lopende containers in de groep opnieuw gestart, meestal op dezelfde onderliggende containerhost.

Implementeer een bestaande containergroep opnieuw door de opdracht Maken (of gebruik de Azure-portal) uit te geven en de naam van een bestaande groep op te geven. Wijzig ten minste één geldige eigenschap van de groep wanneer u de opdracht Maken uitgeeft om de herschikking te activeren en de resterende eigenschappen ongewijzigd te laten (of standaardwaarden te blijven gebruiken). Niet alle eigenschappen van containergroepen zijn geldig voor herplaatsing. Zie [Eigenschappen die moeten worden verwijderd](#properties-that-require-container-delete) voor een lijst met niet-ondersteunde eigenschappen.

In het volgende voorbeeld van Azure CLI wordt een containergroep bijgewerkt met een nieuw DNS-naamlabel. Omdat de eigenschap DNS-naamlabel van de groep een eigenschap is die kan worden bijgewerkt, wordt de containergroep opnieuw geïmplementeerd en worden de containers opnieuw gestart.

Eerste implementatie met DNS-naamlabel *myapplication-staging:*

```azurecli-interactive
# Create container group
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication-staging
```

Werk de containergroep bij met een nieuw DNS-naamlabel, *mijntoepassing*en laat de resterende eigenschappen ongewijzigd:

```azurecli-interactive
# Update DNS name label (restarts container), leave other properties unchanged
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication
```

## <a name="update-benefits"></a>Voordelen bijwerken

Het primaire voordeel van het bijwerken van een bestaande containergroep is een snellere implementatie. Wanneer u een bestaande containergroep opnieuw implementeert, worden de lagen van de containerafbeelding uit de lagen van de containerafbeeldingen getrokken die door de vorige implementatie zijn opgeslagen. In plaats van alle afbeeldingslagen vers uit het register te halen, zoals met nieuwe implementaties wordt gedaan, worden alleen gewijzigde lagen (indien aanwezig) getrokken.

Toepassingen op basis van grotere containerafbeeldingen zoals Windows Server Core kunnen een aanzienlijke verbetering in implementatiesnelheid zien wanneer u updatet in plaats van nieuwe te verwijderen en te implementeren.

## <a name="limitations"></a>Beperkingen

Niet alle eigenschappen van een containergroep ondersteunen updates. Als u bepaalde eigenschappen van een containergroep wilt wijzigen, moet u eerst verwijderen en vervolgens de groep opnieuw implementeren. Zie Eigenschappen [waarvoor containers moeten worden verwijderd](#properties-that-require-container-delete)voor meer informatie.

Alle containers in een containergroep worden opnieuw gestart wanneer u de containergroep bijwerkt. U een update of in-place herstart van een specifieke container in een groep met meerdere containers niet uitvoeren.

Het IP-adres van een container verandert meestal niet tussen updates, maar het is niet gegarandeerd dat het hetzelfde blijft. Zolang de containergroep wordt geïmplementeerd op dezelfde onderliggende host, behoudt de containergroep zijn IP-adres. Hoewel zeldzaam en hoewel Azure Container Instances alles in het werk stelt om opnieuw te worden geïmplementeerd naar dezelfde host, zijn er enkele Azure-interne gebeurtenissen die kunnen leiden tot herplaatsing naar een andere host. Gebruik altijd een DNS-naamlabel voor uw containerinstanties om dit probleem te verhelpen.

Beëindigde of verwijderde containergroepen kunnen niet worden bijgewerkt. Zodra een containergroep is gestopt *Terminated* (is beëindigd) of is verwijderd, wordt de groep als nieuw geïmplementeerd.

## <a name="properties-that-require-container-delete"></a>Eigenschappen waarvoor containermoet worden verwijderd

Zoals eerder vermeld, kunnen niet alle eigenschappen van de containergroep worden bijgewerkt. Als u bijvoorbeeld de poorten wilt wijzigen of het beleid van een container opnieuw wilt starten, moet u eerst de containergroep verwijderen en vervolgens opnieuw maken.

Deze eigenschappen vereisen het verwijderen van containergroepen voordat deze opnieuw wordt geïmplementeerd:

* Besturingssysteemtype
* CPU
* Geheugen
* Beleid voor opnieuw starten
* Poorten

Wanneer u een containergroep verwijdert en opnieuw maakt, wordt deze niet 'opnieuw geïmplementeerd', maar nieuw gemaakt. Alle afbeeldingslagen worden vers uit het register gehaald, niet uit de lagen die in de cache zijn opgeslagen door een eerdere implementatie. Het IP-adres van de container kan ook veranderen als gevolg van implementatie naar een andere onderliggende host.

## <a name="next-steps"></a>Volgende stappen

Meerdere malen genoemd in dit artikel is de **container groep**. Elke container in Azure Container Instances wordt geïmplementeerd in een containergroep en containergroepen kunnen meer dan één container bevatten.

[Containergroepen in Azure Container Instances](container-instances-container-groups.md)

[Een groep met meerdere containers implementeren](container-instances-multi-container-group.md)

[Containers handmatig stoppen of starten in Azure Container Instances](container-instances-stop-start.md)

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[azure-cli-install]: /cli/azure/install-azure-cli
