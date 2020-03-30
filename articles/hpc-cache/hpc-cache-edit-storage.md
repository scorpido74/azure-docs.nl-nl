---
title: Opslagdoelen voor Azure HPC-cache bijwerken
description: Azure HPC-cacheopslagdoelen bewerken
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 1/08/2020
ms.author: rohogue
ms.openlocfilehash: 5635bfc6ea5faea41b125037c76c0b8635e0f528
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75867008"
---
# <a name="edit-storage-targets"></a>Opslagdoelen bewerken

U een opslagdoel verwijderen of wijzigen op de pagina **Opslagdoelen** in de cache.

## <a name="remove-a-storage-target"></a>Een opslagdoel verwijderen

Als u een opslagdoel wilt verwijderen, selecteert u deze in de lijst en klikt u op **verwijderen.**

Met deze actie wordt de koppeling met het opslagdoel met dit Azure HPC-cachesysteem verwijderd, maar wordt het back-endopslagsysteem niet gewijzigd. Als u bijvoorbeeld een Azure Blob-opslagcontainer hebt gebruikt, bestaan de container en de inhoud ervan nog steeds nadat u deze uit de cache hebt verwijderd. U de container toevoegen aan een andere Azure HPC-cache, deze opnieuw toevoegen aan deze cache of verwijderen met de Azure-portal.

Bestandswijzigingen die in de cache zijn opgeslagen, worden naar het back-endopslagsysteem geschreven voordat het opslagdoel wordt verwijderd. Dit proces kan een uur of langer duren als er veel gewijzigde gegevens in de cache liggen.

## <a name="update-storage-targets"></a>Opslagdoelen bijwerken

U opslagdoelen bewerken om een aantal van hun eigenschappen te wijzigen. Verschillende eigenschappen zijn bewerkbaar voor verschillende soorten opslag:

* Voor blob-opslagdoelen u het naamruimtepad wijzigen.

* Voor NFS-opslagdoelen u deze eigenschappen wijzigen:

  * Naamruimtepad
  * Gebruiksmodel
  * Exporteren
  * Submap exporteren

U de naam, het type of het back-endopslagsysteem van een opslagdoel niet bewerken (Blob-container- of NFS-hostnaam/IP-adres). Als u deze eigenschappen wilt wijzigen, verwijdert u het opslagdoel en maakt u een vervanging met de nieuwe waarde.

Als u een opslagdoel wilt wijzigen, klikt u op de naam van het opslagdoel om de pagina met details te openen. Sommige velden op de pagina zijn bewerkbaar.

![schermafbeelding van de bewerkingspagina voor een NFS-opslagdoel](media/hpc-cache-edit-storage-nfs.png)

## <a name="update-an-nfs-storage-target"></a>Een NFS-opslagdoel bijwerken

Voor een NFS-opslagdoel u verschillende eigenschappen bijwerken. (Raadpleeg de bovenstaande schermafbeelding voor een voorbeeldbewerkingspagina.)

* **Gebruiksmodel** - Het gebruiksmodel beïnvloedt hoe de cache gegevens bewaart. Lees [Een gebruiksmodel kiezen](hpc-cache-add-storage.md#choose-a-usage-model) voor meer informatie.
* **Virtueel naamruimtepad** - Het pad dat clients gebruiken om dit opslagdoel te monteren. Lees [De samengevoegde naamruimte](hpc-cache-namespace.md) plannen voor meer informatie.
* **NFS-exportpad** - Het opslagsysteem exporteert naar het gebruik voor dit naamruimtepad.
* **Submappad** - De submap (onder de export) om te koppelen aan dit naamruimtepad. Laat dit veld leeg als u geen submap hoeft op te geven.

Elk naamruimtepad heeft een unieke combinatie van export en submap nodig. Dat wil zeggen, je niet twee verschillende client-facing paden naar exact dezelfde directory op de back-end opslagsysteem.

Nadat u wijzigingen hebt aangebracht, klikt u op **OK** om het opslagdoel bij te werken of klikt u op **Annuleren** om wijzigingen te verwijderen.

## <a name="update-an-azure-blob-storage-target"></a>Een Azure Blob-opslagdoel bijwerken

Op de pagina details voor een Blob-opslagdoel u het virtuele naamruimtepad wijzigen.

![schermafbeelding van de bewerkingspagina voor een blobopslagdoel](media/hpc-cache-edit-storage-blob.png)

Als u klaar bent, klikt u op **OK** om het opslagdoel bij te werken of klik u op **Annuleren** om wijzigingen te verwijderen.

## <a name="next-steps"></a>Volgende stappen

* Lees [Opslagdoelen toevoegen](hpc-cache-add-storage.md) voor meer informatie over deze opties.
* Lees [De samengevoegde naamruimte plannen](hpc-cache-namespace.md) voor meer tips over het gebruik van virtuele paden.
