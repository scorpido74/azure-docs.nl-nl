---
title: Best practices voor het register
description: Leer hoe u Azure Container Registry effectief gebruikt door deze aanbevolen procedures te volgen.
ms.topic: article
ms.date: 09/27/2018
ms.openlocfilehash: 233d84b8bfa6f3d8c800e76032ef74a643db11ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247070"
---
# <a name="best-practices-for-azure-container-registry"></a>Aanbevolen procedures voor Azure Container Registry

Door deze aanbevolen procedures te volgen, kunt u de prestaties en het rendabele gebruik van uw privé-Docker-register in Azure maximaliseren.

Zie ook [Aanbevelingen voor het taggen en versien van containerafbeeldingen](container-registry-image-tag-version.md) voor strategieën om afbeeldingen in uw register te taggen en te versien. 

## <a name="network-close-deployment"></a>Implementatie dichtbij het netwerk

Maak uw containerregister in dezelfde Azure-regio waar u containers implementeert. Door uw register te plaatsen in een regio die zich dichtbij het netwerk bevindt, kunnen de hosts van uw container uw helpen zowel de latentie als de kosten te verlagen.

Implementatie dichtbij het netwerk is een van de belangrijkste redenen om een privé containerregister te gebruiken. Docker-installatiekopieën hebben een efficiënte [gelaagde constructie](https://docs.docker.com/engine/userguide/storagedriver/imagesandcontainers/) die incrementele implementaties mogelijk maakt. Nieuwe knooppunten moeten echter alle lagen ophalen die nodig zijn voor een bepaalde installatiekopie. Deze initiële `docker pull` kan al gauw meerdere gigabytes bedragen. Met een privéregister dichtbij uw implementatie wordt de netwerklatentie tot een minimum teruggebracht.
Bovendien brengen alle openbare clouds, waaronder Azure, netwerkkosten voor uitgaande gegevens in rekening. Het binnenhalen van installatiekopieën van het ene datacenter in het andere, verhoogt behalve de latentie ook de netwerkkosten voor uitgaande gegevens.

## <a name="geo-replicate-multi-region-deployments"></a>Geo-replicatie voor implementaties in meerdere regio’s

Gebruik de functie voor [geo-replicatie](container-registry-geo-replication.md) in Azure Container Registry om containers naar meerdere regio's te implementeren. Of u nu internationale klanten vanuit lokale datacentra helpt of uw dat uw ontwikkelteam zich op verschillende locaties bevindt, u kunt uw registerbeheer vereenvoudigen en latentie minimaliseren door geo-replicatie op uw register toe te passen. Geo-replicatie is alleen beschikbaar voor [Premium](container-registry-skus.md)-registers.

Zie de driedelige zelfstudie [Geo-replicatie in Azure Container Registry](container-registry-tutorial-prepare-registry.md) voor informatie over het gebruik van geo-replicatie.

## <a name="repository-namespaces"></a>Opslagplaatsnaamruimten

Dankzij het gebruik van opslagplaatsnaamruimten kunt u toestaan dat een enkel register tussen meerdere groepen binnen uw organisatie kan worden gedeeld. Registers kunnen worden gedeeld tussen implementaties en teams. Azure Container Registry biedt ondersteuning voor geneste naamruimten, waardoor met geïsoleerde groepen kan worden gewerkt.

Neem bijvoorbeeld de volgende container installatiekopielabels in overweging. Afbeeldingen die bedrijfsbreed worden `aspnetcore`gebruikt, zoals , worden in de hoofdnaamruimte geplaatst, terwijl containerafbeeldingen die eigendom zijn van de producten- en marketinggroepen elk hun eigen naamruimten gebruiken.

- *contoso.azurecr.io/aspnetcore:2.0*
- *contoso.azurecr.io/products/widget/web:1*
- *contoso.azurecr.io/products/bettermousetrap/refundapi:12.3*
- *contoso.azurecr.io/marketing/2017-fall/concertpromotions/campaign:218.42*

## <a name="dedicated-resource-group"></a>Toegewezen resourcegroep

Omdat containerregisters resources zijn die worden gebruikt voor meerdere containerhosts, moet een register zich in de eigen resourcegroep bevinden.

Hoewel u mogelijk aan het experimenteren bent met een specifiek type host, zoals Azure Container Instances, wilt u de containerinstantie waarschijnlijk verwijderen wanneer u klaar bent. Maar misschien wilt u de verzameling installatiekopieën die u naar Azure Container Registry hebt gepusht, wel houden. Door uw register in een eigen resourcegroep te plaatsen, verkleint u het risico dat u de verzameling installatiekopieën in het register per ongeluk verwijdert als u de resourcegroep van de containerinstantie verwijdert.

## <a name="authentication"></a>Authentication

Voor de verificatie van een Azure-containerregister bestaan er twee primaire scenario's: afzonderlijke verificatie en serviceverificatie (ook wel een 'headless'-verificatie genoemd). De volgende tabel bevat een kort overzicht van deze scenario's en de verificatiemethode die voor elk ervan wordt aanbevolen.

| Type | Voorbeeldscenario | Aanbevolen methode |
|---|---|---|
| Afzonderlijke identiteit | Een ontwikkelaar die installatiekopieën binnenhaalt op of pusht vanaf zijn ontwikkelcomputer. | [az acr login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) |
| Headless/service-identiteit | Bouw en implementeer pijplijnen waarbij de gebruiker niet direct is betrokken. | [Serviceprincipal](container-registry-authentication.md#service-principal) |

Zie [Verifiëren met een Azure containerregister](container-registry-authentication.md) voor gedetailleerde informatie over verificatie met Azure Container Registry.

## <a name="manage-registry-size"></a>Registergrootte beheren

De opslagbeperkingen van elke [containerregister-SKU][container-registry-skus] zijn bedoeld om te worden uitgelijnd met een typisch scenario: **Basic** om te beginnen, **Standard ** voor het merendeel van de productietoepassingen en **Premium** voor hyperschaalprestaties en [geo-replicatie][container-registry-geo-replication]. Tijdens de levensduur van het register moet u de grootte ervan beheren door regelmatig ongebruikte inhoud te verwijderen.

Gebruik de azure CLI-opdracht [az acr-showgebruik][az-acr-show-usage] om de huidige grootte van uw register weer te geven:

```azurecli
az acr show-usage --resource-group myResourceGroup --name myregistry --output table
```

```output
NAME      LIMIT         CURRENT VALUE    UNIT
--------  ------------  ---------------  ------
Size      536870912000  185444288        Bytes
Webhooks  100                            Count
```

U de huidige opslag die wordt gebruikt ook vinden in het **overzicht** van uw register in de Azure-portal:

![Informatie over het registergebruik in Azure Portal][registry-overview-quotas]

### <a name="delete-image-data"></a>Afbeeldingsgegevens verwijderen

Azure Container Registry ondersteunt verschillende methoden voor het verwijderen van afbeeldingsgegevens uit uw containerregister. U afbeeldingen verwijderen op tag of manifest digest, of een hele opslagplaats verwijderen.

Zie [Containerafbeeldingen](container-registry-delete.md)verwijderen in Azure Container Registry voor meer informatie over het verwijderen van afbeeldingsgegevens uit uw register, inclusief afbeeldingen zonder tag (ook wel 'bungelend' of 'verweesd' genoemd).

## <a name="next-steps"></a>Volgende stappen

Azure Container Registry is beschikbaar in verschillende categorieën, ook wel SKU's genoemd, die elk verschillende mogelijkheden bieden. Zie [Azure Container Registry-SKU's](container-registry-skus.md) voor meer informatie over de beschikbare SKU's.

<!-- IMAGES -->
[delete-repository-portal]: ./media/container-registry-best-practices/delete-repository-portal.png
[registry-overview-quotas]: ./media/container-registry-best-practices/registry-overview-quotas.png

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-show-usage]: /cli/azure/acr#az-acr-show-usage
[azure-cli]: /cli/azure
[azure-portal]: https://portal.azure.com
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-skus]: container-registry-skus.md
