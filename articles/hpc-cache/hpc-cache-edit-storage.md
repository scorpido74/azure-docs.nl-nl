---
title: Doel doelen voor Azure HPC-cache bijwerken
description: Doel doelen van de Azure HPC-cache bewerken
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 1/08/2020
ms.author: v-erkel
ms.openlocfilehash: 9bce4ec383b8ac7efaa9b00237044424ffb58fc0
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/03/2020
ms.locfileid: "84344251"
---
# <a name="edit-storage-targets"></a>Opslagdoelen bewerken

U kunt een opslag doel verwijderen of wijzigen op de pagina **opslag doelen** van de cache.

> [!TIP]
> De [video](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) voor het beheren van de HPC-cache van Azure laat zien hoe u een opslag doel bewerkt.

## <a name="remove-a-storage-target"></a>Een opslag doel verwijderen

Als u een opslag doel wilt verwijderen, selecteert u het in de lijst en klikt u op de knop **verwijderen** .

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

Als u een opslag doel wilt wijzigen, klikt u op de naam van de opslag doel om de detail pagina ervan te openen. Sommige velden op de pagina kunnen worden bewerkt.

![scherm afbeelding van de bewerkings pagina voor een NFS-opslag doel](media/hpc-cache-edit-storage-nfs.png)

## <a name="update-an-nfs-storage-target"></a>Een NFS-opslag doel bijwerken

Voor een NFS-opslag doel kunt u verschillende eigenschappen bijwerken. (Raadpleeg de bovenstaande scherm afbeelding voor een voor beeld van een pagina bewerken.)

* **Gebruiks model** : het gebruiks model is van invloed op de manier waarop de cache gegevens behoudt. Lees [een gebruiks model kiezen](hpc-cache-add-storage.md#choose-a-usage-model) voor meer informatie.
* **Pad naar virtuele naam ruimte** : het pad dat clients gebruiken om dit opslag doel te koppelen. [Plan de geaggregeerde naam ruimte](hpc-cache-namespace.md) voor meer informatie.
* **Pad naar NFS-export** -de export van het opslag systeem die moet worden gebruikt voor dit pad naar de naam ruimte.
* **Pad** naar de submap: de submap (onder de export) die moet worden gekoppeld aan dit pad naar de naam ruimte. Laat dit veld leeg als u geen submap hoeft op te geven.

Elk pad naar een naam ruimte moet een unieke combi natie van export en submap hebben. Dat wil zeggen dat u niet twee verschillende client paden naar dezelfde map op het back-end-opslag systeem kunt maken.

Nadat u wijzigingen hebt aangebracht, klikt u op **OK** om het opslag doel bij te werken of klikt u op **Annuleren** om de wijzigingen te negeren.

## <a name="update-an-azure-blob-storage-target"></a>Een Azure Blob-opslag doel bijwerken

Op de pagina Details voor een Blob Storage-doel kunt u het pad van de virtuele naam ruimte wijzigen.

![scherm afbeelding van de bewerkings pagina voor een Blob Storage-doel](media/hpc-cache-edit-storage-blob.png)

Wanneer u klaar bent, klikt u op **OK** om het opslag doel bij te werken of klikt u op **Annuleren** om de wijzigingen te negeren.

## <a name="next-steps"></a>Volgende stappen

* Lees [opslag doelen toevoegen](hpc-cache-add-storage.md) voor meer informatie over deze opties.
* [Plan de geaggregeerde naam ruimte](hpc-cache-namespace.md) voor meer tips over het gebruik van virtuele paden.
