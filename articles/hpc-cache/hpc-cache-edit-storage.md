---
title: Doel doelen voor Azure HPC-cache bijwerken
description: Doel doelen van de Azure HPC-cache bewerken
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/02/2020
ms.author: v-erkel
ms.openlocfilehash: f11e12c4f30977514e04b09c7e1c3012eb7888a7
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87092453"
---
# <a name="edit-storage-targets"></a>Opslagdoelen bewerken

U kunt een opslag doel verwijderen of wijzigen op de portal pagina voor **opslag doelen** van de cache of via de Azure cli.

> [!TIP]
> De [video](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) voor het beheren van de HPC-cache van Azure laat zien hoe u een opslag doel bewerkt in de Azure Portal.

## <a name="remove-a-storage-target"></a>Een opslag doel verwijderen

### <a name="portal"></a>[Portal](#tab/azure-portal)

Als u een opslag doel wilt verwijderen, selecteert u het in de lijst en klikt u op de knop **verwijderen** .

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

Gebruik [AZ HPC-cache Storage-doel Remove](/cli/azure/ext/hpc-cache/hpc-cache/storage-target#ext-hpc-cache-az-hpc-cache-storage-target-remove) om een opslag doel uit de cache te verwijderen.

```azurecli
$ az hpc-cache storage-target remove --resource-group cache-rg --cache-name doc-cache0629 --name blob1

{- Finished ..
  "endTime": "2020-07-09T21:45:06.1631571+00:00",
  "name": "2f95eac1-aded-4860-b19c-3f089531a7ec",
  "startTime": "2020-07-09T21:43:38.5461495+00:00",
  "status": "Succeeded"
}
```

---

Met deze actie wordt de opslag doel koppeling met dit Azure HPC-cache systeem verwijderd, maar wordt het back-end-opslag systeem niet gewijzigd. Als u bijvoorbeeld een Azure Blob Storage-container hebt gebruikt, bestaan de container en de inhoud ervan nog steeds nadat u deze uit de cache hebt verwijderd. U kunt de container toevoegen aan een andere Azure HPC-cache door deze opnieuw toe te voegen aan deze cache of door de Azure Portal te verwijderen.

Alle bestands wijzigingen die in de cache zijn opgeslagen, worden naar het back-end-opslag systeem geschreven voordat het opslag doel wordt verwijderd. Dit proces kan een uur of langer duren als er veel gewijzigde gegevens in de cache staan.

## <a name="update-storage-targets"></a>Opslag doelen bijwerken

U kunt opslag doelen bewerken om sommige eigenschappen te wijzigen. Verschillende eigenschappen zijn bewerkbaar voor verschillende soorten opslag:

* Voor Blob Storage-doelen kunt u het pad naar de naam ruimte wijzigen.

* Voor NFS-opslag doelen kunt u de volgende eigenschappen wijzigen:

  * Pad naar naam ruimte
  * Gebruiks model
  * Exporteren
  * Submap exporteren

U kunt de naam, het type of het back-end-opslag systeem (BLOB-container of NBS-hostnaam/IP-adres) van een opslag doel niet bewerken. Als u deze eigenschappen wilt wijzigen, verwijdert u het opslag doel en maakt u een vervanging met de nieuwe waarde.

In de Azure Portal kunt u zien welke velden kunnen worden bewerkt door te klikken op de naam van de opslag doelstelling en de pagina Details te openen. U kunt ook opslag doelen wijzigen met de Azure CLI.

![scherm afbeelding van de bewerkings pagina voor een NFS-opslag doel](media/hpc-cache-edit-storage-nfs.png)

## <a name="update-an-nfs-storage-target"></a>Een NFS-opslag doel bijwerken

Voor een NFS-opslag doel kunt u verschillende eigenschappen bijwerken. (Raadpleeg de bovenstaande scherm afbeelding voor een voor beeld van een pagina bewerken.)

* **Gebruiks model** : het gebruiks model is van invloed op de manier waarop de cache gegevens behoudt. Lees [een gebruiks model kiezen](hpc-cache-add-storage.md#choose-a-usage-model) voor meer informatie.
* **Pad naar virtuele naam ruimte** : het pad dat clients gebruiken om dit opslag doel te koppelen. [Plan de geaggregeerde naam ruimte](hpc-cache-namespace.md) voor meer informatie.
* **Pad naar NFS-export** -de export van het opslag systeem die moet worden gebruikt voor dit pad naar de naam ruimte.
* **Pad** naar de submap: de submap (onder de export) die moet worden gekoppeld aan dit pad naar de naam ruimte. Laat dit veld leeg als u geen submap hoeft op te geven.

Elk pad naar een naam ruimte moet een unieke combi natie van export en submap hebben. Dat wil zeggen dat u niet twee verschillende client paden naar dezelfde map op het back-end-opslag systeem kunt maken.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Nadat u wijzigingen hebt aangebracht, klikt u op **OK** om het opslag doel bij te werken of klikt u op **Annuleren** om de wijzigingen te negeren.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

Gebruik de opdracht [AZ NFS-Storage-target](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target) om het gebruiks model, het pad naar de virtuele naam ruimte en de NFS-export of-submap voor een opslag doel te wijzigen.

* Gebruik de optie om het gebruiks model te wijzigen ``--nfs3-usage-model`` . Voorbeeld: ``--nfs3-usage-model WRITE_WORKLOAD_15``

* Gebruik de optie voor het wijzigen van het pad van de naam ruimte, het exporteren of exporteren van de submap ``--junction`` .

  De ``--junction`` para meter gebruikt deze waarden:

  * ``namespace-path``-Het pad naar het client gerichte virtuele bestand
  * ``nfs-export``-Het opslag systeem exporteren om te koppelen aan het client gerichte pad
  * ``target-path``(optioneel): een submap van de export, indien nodig

  Voorbeeld: ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

De cache naam, de naam van het opslag doel en de resource groep zijn vereist in alle update-opdrachten.

Opdracht voorbeeld: <!-- having problem testing this -->

```azurecli
az hpc-cache nfs-storage-target update --cache-name mycache \
    --name rivernfs0 --resource-group doc-rg0619 \
    --nfs3-usage-model READ_HEAVY_INFREQ
```

Als de cache is gestopt of niet in orde is, wordt de update toegepast nadat de cache in orde is.

---

## <a name="update-an-azure-blob-storage-target"></a>Een Azure Blob-opslag doel bijwerken

Voor een Blob Storage-doel kunt u het pad van de virtuele naam ruimte wijzigen.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Op de pagina Details voor een Blob Storage-doel kunt u het pad van de virtuele naam ruimte wijzigen.

![scherm afbeelding van de bewerkings pagina voor een Blob Storage-doel](media/hpc-cache-edit-storage-blob.png)

Wanneer u klaar bent, klikt u op **OK** om het opslag doel bij te werken of klikt u op **Annuleren** om de wijzigingen te negeren.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

Gebruik [AZ HPC-cache Blob-Storage-target update](/cli/azure/ext/hpc-cache/hpc-cache/blob-storage-target#ext-hpc-cache-az-hpc-cache-blob-storage-target-update) om het pad van de naam ruimte van het doel bij te werken.

```azurecli
az hpc-cache blob-storage-target update --cache-name cache-name --name target-name \
    --resource-group rg --storage-account "/subscriptions/<subscription_ID>/resourceGroups/erinazcli/providers/Microsoft.Storage/storageAccounts/rg"  \
    --container-name "container-name" --virtual-namespace-path "/new-path"
```

Als de cache is gestopt of niet in orde is, wordt de update toegepast nadat de cache in orde is.

---

## <a name="next-steps"></a>Volgende stappen

* Lees [opslag doelen toevoegen](hpc-cache-add-storage.md) voor meer informatie over deze opties.
* [Plan de geaggregeerde naam ruimte](hpc-cache-namespace.md) voor meer tips over het gebruik van virtuele paden.
