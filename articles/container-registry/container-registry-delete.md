---
title: Afbeeldingsbronnen verwijderen
description: Details over het effectief beheren van de registergrootte door containerimagegegevens te verwijderen met Azure CLI-opdrachten.
ms.topic: article
ms.date: 07/31/2019
ms.openlocfilehash: 449a1c09bf88e3e0e0aeca4d3b687371d2a6b91a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78403345"
---
# <a name="delete-container-images-in-azure-container-registry-using-the-azure-cli"></a>Containerafbeeldingen verwijderen in Azure Container Registry met de Azure CLI

Als u de grootte van uw Azure-containerregister wilt behouden, moet u op gezette tijden verouderde afbeeldingsgegevens verwijderen. Hoewel sommige containerafbeeldingen die in productie worden geïmplementeerd, op langere termijn kunnen worden opgeslagen, kunnen andere doorgaans sneller worden verwijderd. In een geautomatiseerd build- en testscenario kan uw register bijvoorbeeld snel vullen met afbeeldingen die mogelijk nooit worden geïmplementeerd en kan het kort na het voltooien van de build- en testpas worden verwijderd.

Omdat u afbeeldingsgegevens op verschillende manieren verwijderen, is het belangrijk om te begrijpen hoe elke verwijderingsbewerking van invloed is op het opslaggebruik. In dit artikel worden verschillende methoden voor het verwijderen van afbeeldingsgegevens verwerkt:

* Een [opslagplaats verwijderen](#delete-repository): verwijdert alle afbeeldingen en alle unieke lagen in de repository.
* Verwijderen op [tag](#delete-by-tag): hiermee verwijdert u een afbeelding, de tag, alle unieke lagen waarnaar de afbeelding verwijst en alle andere tags die aan de afbeelding zijn gekoppeld.
* Delete by [manifest digest](#delete-by-manifest-digest): Verwijdert een afbeelding, alle unieke lagen waarnaar de afbeelding verwijst en alle tags die aan de afbeelding zijn gekoppeld.

Voorbeeldscripts worden verstrekt om verwijderingsbewerkingen te automatiseren.

Zie [Over registers, opslagplaatsen en afbeeldingen](container-registry-concepts.md)voor een inleiding tot deze concepten.

## <a name="delete-repository"></a>Repository verwijderen

Als u een opslagplaats verwijdert, worden alle afbeeldingen in de opslagplaats verwijderd, inclusief alle tags, unieke lagen en manifesten. Wanneer u een opslagplaats verwijdert, herstelt u de opslagruimte die wordt gebruikt door de afbeeldingen die verwijzen naar unieke lagen in die opslagplaats.

Met de volgende opdracht Azure CLI worden de "acr-helloworld"-opslagplaats en alle tags en manifesten in de repository verwijderd. Als lagen waarnaar wordt verwezen door de verwijderde manifesten niet worden verwezen door andere afbeeldingen in het register, worden hun laaggegevens ook verwijderd, waarbij de opslagruimte wordt hersteld.

```azurecli
 az acr repository delete --name myregistry --repository acr-helloworld
```

## <a name="delete-by-tag"></a>Verwijderen op tag

U afzonderlijke afbeeldingen uit een opslagplaats verwijderen door de naam en de tag van de opslagplaats op te geven in de verwijderingsbewerking. Wanneer u per tag verwijdert, herstelt u de opslagruimte die wordt gebruikt door unieke lagen in de afbeelding (lagen die niet worden gedeeld door andere afbeeldingen in het register).

Als u de tag wilt verwijderen, gebruikt u az `--image` [acr-repository verwijderen][az-acr-repository-delete] en geeft u de afbeeldingsnaam op in de parameter. Alle lagen die uniek zijn voor de afbeelding en alle andere tags die aan de afbeelding zijn gekoppeld, worden verwijderd.

Bijvoorbeeld, het verwijderen van de "acr-helloworld:latest" afbeelding van register "myregistry":

```azurecli
az acr repository delete --name myregistry --image acr-helloworld:latest
```

```output
This operation will delete the manifest 'sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108' and all the following images: 'acr-helloworld:latest', 'acr-helloworld:v3'.
Are you sure you want to continue? (y/n):
```

> [!TIP]
> Verwijderen *op tag* mag niet worden verward met het verwijderen van een tag (untagging). U een tag verwijderen met de Azure CLI-opdracht [az acr-repository untag][az-acr-repository-untag]. Er wordt geen ruimte vrijgemaakt wanneer u de tag van een afbeelding ongedaan maakt omdat de [manifest-](container-registry-concepts.md#manifest) en laaggegevens in het register blijven staan. Alleen de tagverwijzing zelf wordt verwijderd.

## <a name="delete-by-manifest-digest"></a>Verwijderen door manifestsamenvatting

Een [manifestdigest](container-registry-concepts.md#manifest-digest) kan worden gekoppeld aan één, geen of meerdere tags. Wanneer u verwijdert door digest, worden alle tags waarnaar naar het manifest verwijst, verwijderd, evenals laaggegevens voor alle lagen die uniek zijn voor de afbeelding. Gedeelde laaggegevens worden niet verwijderd.

Als u wilt verwijderen door digest, moet u eerst de manifestsamenvattingen voor de opslagplaats weergeven met de afbeeldingen die u wilt verwijderen. Bijvoorbeeld:

```azurecli
az acr repository show-manifests --name myregistry --repository acr-helloworld
```

```output
[
  {
    "digest": "sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108",
    "tags": [
      "latest",
      "v3"
    ],
    "timestamp": "2018-07-12T15:52:00.2075864Z"
  },
  {
    "digest": "sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57",
    "tags": [
      "v2"
    ],
    "timestamp": "2018-07-12T15:50:53.5372468Z"
  }
]
```

Geef vervolgens de samenvatting op die u wilt verwijderen in de opdracht [az acr repository delete.][az-acr-repository-delete] De indeling van de opdracht is als volgt:

```azurecli
az acr repository delete --name <acrName> --image <repositoryName>@<digest>
```

Bijvoorbeeld om het laatste manifest in de voorgaande uitvoer te verwijderen (met de tag "v2"):

```azurecli
az acr repository delete --name myregistry --image acr-helloworld@sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57
```

```output
This operation will delete the manifest 'sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57' and all the following images: 'acr-helloworld:v2'.
Are you sure you want to continue? (y/n): 
```

De `acr-helloworld:v2` afbeelding wordt uit het register verwijderd, evenals alle laaggegevens die uniek zijn voor die afbeelding. Als een manifest is gekoppeld aan meerdere tags, worden ook alle bijbehorende tags verwijderd.

## <a name="delete-digests-by-timestamp"></a>Digesten verwijderen door tijdstempel

Als u de grootte van een opslagplaats of register wilt behouden, moet u mogelijk manifestsamenvattingen die ouder zijn dan een bepaalde datum periodiek verwijderen.

Met de volgende opdracht Azure CLI worden alle manifestdigest weergegeven in een opslagplaats die ouder is dan een opgegeven tijdstempel, in oplopende volgorde. Vervang `<acrName>` `<repositoryName>` en met waarden die geschikt zijn voor uw omgeving. De tijdstempel kan een volledige datum-tijdexpressie of een datum zijn, zoals in dit voorbeeld.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> \
--orderby time_asc -o tsv --query "[?timestamp < '2019-04-05'].[digest, timestamp]"
```

Nadat u verouderde manifestdigesten hebt geïdentificeerd, u het volgende Bash-script uitvoeren om manifestsamenvattingen te verwijderen die ouder zijn dan een opgegeven tijdstempel. Hiervoor zijn de Azure CLI en **xargs**vereist. Standaard wordt het script niet verwijderd. Wijzig `ENABLE_DELETE` de `true` waarde om het verwijderen van afbeeldingen in te schakelen.

> [!WARNING]
> Gebruik het volgende voorbeeldscript met de nodige voorzichtigheid: verwijderde afbeeldingsgegevens zijn onherstelbaar. Als u systemen hebt die afbeeldingen opmanifesten trekken (in tegenstelling tot de naam van de afbeelding), moet u deze scripts niet uitvoeren. Als u de manifestsamenvattingen verwijderde, voorkomt u dat deze systemen de afbeeldingen uit uw register halen. In plaats van te trekken door manifest, overwegen de vaststelling van een *unieke tagging* regeling, een [aanbevolen beste praktijk](container-registry-image-tag-version.md). 

```bash
#!/bin/bash

# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to 'true' to enable image delete
ENABLE_DELETE=false

# Modify for your environment
# TIMESTAMP can be a date-time string such as 2019-03-15T17:55:00.
REGISTRY=myregistry
REPOSITORY=myrepository
TIMESTAMP=2019-04-05  

# Delete all images older than specified timestamp.

if [ "$ENABLE_DELETE" = true ]
then
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY \
    --orderby time_asc --query "[?timestamp < '$TIMESTAMP'].digest" -o tsv \
    | xargs -I% az acr repository delete --name $REGISTRY --image $REPOSITORY@% --yes
else
    echo "No data deleted."
    echo "Set ENABLE_DELETE=true to enable deletion of these images in $REPOSITORY:"
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY \
   --orderby time_asc --query "[?timestamp < '$TIMESTAMP'].[digest, timestamp]" -o tsv
fi
```

## <a name="delete-untagged-images"></a>Niet-gelabelde afbeeldingen verwijderen

Zoals vermeld in de sectie [Manifest digest,](container-registry-concepts.md#manifest-digest) het indrukken van een gewijzigde afbeelding met behulp van een bestaande tag **ontleden** de eerder geduwde afbeelding, wat resulteert in een verweesde (of "bungelende") afbeelding. De eerder gepushte afbeelding manifest - en de laag gegevens - blijft in het register. Houd rekening met de volgende volgorde van gebeurtenissen:

1. Push afbeelding *acr-helloworld* met tag **nieuwste:**`docker push myregistry.azurecr.io/acr-helloworld:latest`
1. Controleer manifesten voor repository *acr-helloworld:*

   ```azurecli
   az acr repository show-manifests --name myregistry --repository acr-helloworld
   
   ```
   
   ```output
   [
     {
       "digest": "sha256:d2bdc0c22d78cde155f53b4092111d7e13fe28ebf87a945f94b19c248000ceec",
       "tags": [
         "latest"
       ],
       "timestamp": "2018-07-11T21:32:21.1400513Z"
     }
   ]
   ```

1. *Acr-helloworld* Dockerfile wijzigen
1. Push afbeelding *acr-helloworld* met tag **nieuwste:**`docker push myregistry.azurecr.io/acr-helloworld:latest`
1. Controleer manifesten voor repository *acr-helloworld:*

   ```azurecli
   az acr repository show-manifests --name myregistry --repository acr-helloworld
   ```
   
   ```output
   [
     {
       "digest": "sha256:7ca0e0ae50c95155dbb0e380f37d7471e98d2232ed9e31eece9f9fb9078f2728",
       "tags": [
         "latest"
       ],
       "timestamp": "2018-07-11T21:38:35.9170967Z"
     },
     {
       "digest": "sha256:d2bdc0c22d78cde155f53b4092111d7e13fe28ebf87a945f94b19c248000ceec",
       "tags": [],
       "timestamp": "2018-07-11T21:32:21.1400513Z"
     }
   ]
   ```

Zoals u zien in de uitvoer van de laatste stap in `"tags"` de reeks, is er nu een verweesd manifest waarvan de eigenschap een lege lijst is. Dit manifest bestaat nog steeds binnen het register, samen met alle unieke laaggegevens waarnaar wordt verwezen. **Als u dergelijke zwevende afbeeldingen en hun laaggegevens wilt verwijderen, moet u verwijderen door manifestdigest**.

## <a name="delete-all-untagged-images"></a>Alle niet-gelabelde afbeeldingen verwijderen

U alle niet-gelabelde afbeeldingen in uw opslagplaats aanbieden met de volgende opdracht Azure CLI. Vervang `<acrName>` `<repositoryName>` en met waarden die geschikt zijn voor uw omgeving.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> --query "[?tags[0]==null].digest"
```

Met deze opdracht in een script u alle niet-gelabelde afbeeldingen in een opslagplaats verwijderen.

> [!WARNING]
> Gebruik de volgende voorbeeldscripts met de nodige voorzichtigheid: verwijderde afbeeldingsgegevens zijn onherstelbaar. Als u systemen hebt die afbeeldingen opmanifesten trekken (in tegenstelling tot de naam van de afbeelding), moet u deze scripts niet uitvoeren. Als u niet-gelabelde afbeeldingen verwijderde, wordt voorkomen dat deze systemen de afbeeldingen uit uw register halen. In plaats van te trekken door manifest, overwegen de vaststelling van een *unieke tagging* regeling, een [aanbevolen beste praktijk](container-registry-image-tag-version.md).

**Azure CLI in Bash**

Met het volgende Bash-script worden alle niet-gelabelde afbeeldingen uit een opslagplaats verwijderd. Hiervoor zijn de Azure CLI en **xargs**vereist. Standaard wordt het script niet verwijderd. Wijzig `ENABLE_DELETE` de `true` waarde om het verwijderen van afbeeldingen in te schakelen.

```bash
#!/bin/bash

# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to 'true' to enable image delete
ENABLE_DELETE=false

# Modify for your environment
REGISTRY=myregistry
REPOSITORY=myrepository

# Delete all untagged (orphaned) images
if [ "$ENABLE_DELETE" = true ]
then
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY  --query "[?tags[0]==null].digest" -o tsv \
    | xargs -I% az acr repository delete --name $REGISTRY --image $REPOSITORY@% --yes
else
    echo "No data deleted."
    echo "Set ENABLE_DELETE=true to enable image deletion of these images in $REPOSITORY:"
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY --query "[?tags[0]==null]" -o tsv
fi
```

**Azure CLI in PowerShell**

Met het volgende PowerShell-script worden alle niet-gelabelde afbeeldingen uit een opslagplaats verwijderd. Hiervoor zijn PowerShell en de Azure CLI vereist. Standaard wordt het script niet verwijderd. Wijzig `$enableDelete` de `$TRUE` waarde om het verwijderen van afbeeldingen in te schakelen.

```powershell
# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to '$TRUE' to enable image delete
$enableDelete = $FALSE

# Modify for your environment
$registry = "myregistry"
$repository = "myrepository"

if ($enableDelete) {
    az acr repository show-manifests --name $registry --repository $repository --query "[?tags[0]==null].digest" -o tsv `
    | %{ az acr repository delete --name $registry --image $repository@$_ --yes }
} else {
    Write-Host "No data deleted."
    Write-Host "Set `$enableDelete = `$TRUE to enable image deletion."
    az acr repository show-manifests --name $registry --repository $repository --query "[?tags[0]==null]" -o tsv
}
```


## <a name="automatically-purge-tags-and-manifests-preview"></a>Automatisch tags en manifesten leegmaken (preview)

Voer als alternatief voor het scripten van Azure CLI-opdrachten een on-demand of geplande ACR-taak uit om alle tags te verwijderen die ouder zijn dan een bepaalde duur of overeenkomen met een opgegeven naamfilter. Zie [Afbeeldingen automatisch verwijderen uit een Azure-containerregister](container-registry-auto-purge.md)voor meer informatie.

Stel optioneel een [bewaarbeleid](container-registry-retention-policy.md) in voor elk register om niet-gecodeerde manifesten te beheren. Wanneer u een bewaarbeleid inschakelt, worden afbeeldingsmanifesten in het register die geen bijbehorende tags hebben en worden de onderliggende laaggegevens na een bepaalde periode automatisch verwijderd.

## <a name="next-steps"></a>Volgende stappen

Zie [Containerimagestorage in Azure Container Registry](container-registry-storage.md)voor meer informatie over afbeeldingsopslag in Azure Container Registry.

<!-- IMAGES -->
[manifest-digest]: ./media/container-registry-delete/01-manifest-digest.png

<!-- LINKS - External -->
[docker-manifest-inspect]: https://docs.docker.com/edge/engine/reference/commandline/manifest/#manifest-inspect
[portal]: https://portal.azure.com

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
