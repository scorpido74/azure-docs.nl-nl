---
title: Opslag toevoegen aan een Azure HPC-cache
description: Hoe u opslag doelen definieert zodat uw Azure HPC-cache uw on-premises NFS-systeem of Azure Blob-containers voor lange termijn bestands opslag kan gebruiken
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: b10692e352007ee2b0fd18543d8ae2ad8f9819dc
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73621454"
---
# <a name="add-storage-targets"></a>Opslagdoelen toevoegen

*Opslag doelen* zijn back-end opslag voor bestanden die toegankelijk zijn via een Azure HPC-cache-exemplaar. U kunt NFS-opslag (zoals een on-premises hardware-systeem) toevoegen of gegevens opslaan in Azure Blob.

U kunt Maxi maal tien verschillende opslag doelen definiëren voor één cache. De cache geeft alle opslag doelen weer in één geaggregeerde naam ruimte.

Houd er rekening mee dat de export van de opslag toegankelijk moet zijn vanuit het virtuele netwerk van uw cache. Voor on-premises hardwarematige opslag moet u mogelijk een DNS-server instellen die hostnamen voor NFS-opslag toegang kan omzetten. Meer informatie vindt u in [DNS-toegang](hpc-cache-prereqs.md#dns-access).

Voeg opslag doelen toe nadat u de cache hebt gemaakt. De procedure wijkt enigszins af, afhankelijk van of u Azure Blob-opslag of een NFS-export toevoegt. Hieronder vindt u meer informatie.

## <a name="open-the-storage-targets-page"></a>De pagina opslag doelen openen

Open in de Azure Portal uw cache-exemplaar en klik op **opslag doelen** op de zijbalk aan de linkerkant. De pagina opslag doelen bevat een lijst met alle bestaande doelen en bevat een koppeling om een nieuwe toe te voegen.

![scherm afbeelding van de koppeling opslag doelen op de zijbalk, onder de kop configureren, tussen de instellingen en controle van categorie koppen](media/hpc-cache-storage-targets-sidebar.png)

## <a name="add-a-new-azure-blob-storage-target"></a>Een nieuw Azure Blob-opslag doel toevoegen

Een nieuw Blob-opslag doel vereist een lege BLOB-container of een container die is gevuld met gegevens in de bestandssysteem indeling van de Azure HPC-cache. Lees meer over het vooraf laden van een BLOB-container in [gegevens verplaatsen naar Azure Blob-opslag](hpc-cache-ingest.md).

Als u een Azure Blob-container wilt definiëren, voert u deze informatie in.

![scherm afbeelding van de pagina opslag doel toevoegen, gevuld met informatie voor een nieuw Azure Blob-opslag doel](media/hpc-cache-add-blob.png)

<!-- need to replace screenshot after note text is updated with both required RBAC roles and also with correct search term -->

* **Naam van opslag doel** : Stel een naam in die dit opslag doel identificeert in de Azure HPC-cache.
* **Doel type** : Kies **BLOB**.
* **Opslag account** : Selecteer het account met de container waarnaar moet worden verwezen.

  U moet het cache-exemplaar toestemming geven om toegang te krijgen tot het opslag account zoals beschreven in [de toegangs functies toevoegen](#add-the-access-control-roles-to-your-account).

  Lees [vereisten voor Blob Storage](hpc-cache-prereqs.md#blob-storage-requirements)voor meer informatie over het type opslag account dat u kunt gebruiken.

* **Opslag container** : Selecteer de BLOB-container voor dit doel.

* **Pad naar virtuele naam ruimte** : Stel het client gerichte bestandspad in voor dit opslag doel. Lees de [geaggregeerde naam ruimte configureren](hpc-cache-namespace.md) voor meer informatie over de functie virtuele naam ruimte.

Wanneer u klaar bent, klikt u op **OK** om het opslag doel toe te voegen.

### <a name="add-the-access-control-roles-to-your-account"></a>De toegangs beheer rollen toevoegen aan uw account

Azure HPC cache maakt gebruik [van op rollen gebaseerd toegangs beheer (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/index) voor het machtigen van de cache toepassing voor toegang tot uw opslag account voor Azure Blob Storage-doelen.

De eigenaar van het opslag account moet expliciet de Inzender rollen voor het [opslag account](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-contributor) en de [blobgegevens](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) voor de gebruiker ' HPC-cache resource provider ' toevoegen.

U kunt dit vooraf doen, of door te klikken op een koppeling op de pagina waar u een Blob-opslag doel toevoegt.

Stappen voor het toevoegen van de RBAC-rollen:

1. Open de pagina **toegangs beheer (IAM)** voor het opslag account. (De koppeling op de pagina **opslag doel toevoegen** opent deze pagina automatisch voor het geselecteerde account.)

1. Klik op de **+** boven aan de pagina en kies **een roltoewijzing toevoegen**.

1. Selecteer de rol ' Inzender voor opslag accounts ' in de lijst.

1. In het veld **toegang toewijzen aan kunt u** de geselecteerde standaard waarde (' Azure AD-gebruiker,-groep of Service-Principal ') laten staan.  

1. In het veld **selecteren zoekt u** naar ' HPC '.  Deze teken reeks moet overeenkomen met een service-principal met de naam ' HPC-cache resource provider '. Klik op die principal om deze te selecteren.

   > [!NOTE]
   > Als een zoek opdracht voor "HPC" niet werkt, kunt u de teken reeks "storagecache" gebruiken. Gebruikers die zijn toegevoegd aan de voor beelden (vóór GA), moeten mogelijk de oudere naam voor de Service-Principal gebruiken.

1. Klik op de knop **Opslaan** om de roltoewijzing toe te voegen aan het opslag account.

1. Herhaal dit proces om de rol ' Storage BLOB data contributor ' toe te wijzen.  

![scherm opname van de gebruikers interface voor roltoewijzing toevoegen](media/hpc-cache-add-role.png)

## <a name="add-a-new-nfs-storage-target"></a>Een nieuw NFS-opslag doel toevoegen

Een NFS-opslag doel bevat enkele extra velden om op te geven hoe de opslag export moet worden bereikt en hoe de gegevens op efficiënte wijze in de cache moeten worden opgeslagen. U kunt ook meerdere naam ruimte paden van een NFS-host maken als deze meer dan één export beschikbaar heeft.

![Scherm afbeelding van de pagina opslag doel toevoegen met NFS-doel gedefinieerd](media/hpc-cache-add-nfs-target.png)

Geef deze informatie op voor een opslag doel met NFS-back-ups:

* **Naam van opslag doel** : Stel een naam in die dit opslag doel identificeert in de Azure HPC-cache.

* **Doel type** : Kies voor **NFS**.

* **Hostnaam** : Voer het IP-adres of de Fully Qualified Domain name voor uw NFS-opslag systeem in. (Gebruik alleen een domein naam als uw cache toegang heeft tot een DNS-server die de naam kan omzetten.)

* **Gebruiks model** : Kies een van de gegevens cache profielen op basis van uw werk stroom, zoals wordt beschreven in [een gebruiks model kiezen](#choose-a-usage-model).

### <a name="nfs-namespace-paths"></a>NFS-naam ruimte paden

Een NFS-opslag doel kan meerdere virtuele paden hebben, zolang elk pad een andere export of submap op hetzelfde opslag systeem vertegenwoordigt.

Alle paden van het ene opslag doel maken.
<!-- You can create multiple namespace paths to represent different exports on the same NFS storage system, but you must create them all from one storage target. -->

Vul deze waarden in voor elk pad naar de naam ruimte:

* **Pad naar virtuele naam ruimte** : Stel het client gerichte bestandspad in voor dit opslag doel. Lees de [geaggregeerde naam ruimte configureren](hpc-cache-namespace.md) voor meer informatie over de functie virtuele naam ruimte.

<!--  The virtual path should start with a slash ``/``. -->

* **Pad naar NFS-export** -Geef het pad op naar de NFS-export.

* **Pad naar de submap** : als u een specifieke submap van de export wilt koppelen, voert u deze hier in. Als dat niet het geval is, laat u dit veld leeg.

Wanneer u klaar bent, klikt u op **OK** om het opslag doel toe te voegen.

### <a name="choose-a-usage-model"></a>Kies een gebruiks model
<!-- referenced from GUI - update aka.ms link if you change this heading -->

Wanneer u een opslag doel maakt dat verwijst naar een NFS-opslag systeem, moet u het *gebruiks model* voor dat doel kiezen. Dit model bepaalt hoe de gegevens in de cache worden opgeslagen.

* Zwaren: als u de cache voornamelijk gebruikt voor het versnellen van gegevens lees toegang, kiest u deze optie.

* Lezen/schrijven: als clients de cache gebruiken om te lezen en te schrijven, kiest u deze optie.

* Clients slaan de cache over. Kies deze optie als uw clients gegevens rechtstreeks naar het opslag systeem schrijven zonder eerst naar de cache te schrijven.

## <a name="next-steps"></a>Volgende stappen

Nadat u opslag doelen hebt gemaakt, kunt u een van de volgende taken uitvoeren:

* [De Azure HPC-cache koppelen](hpc-cache-mount.md)
* [Gegevens verplaatsen naar Azure Blob-opslag](hpc-cache-ingest.md)

Als u een opslag doel moet wijzigen, lees dan [opslag doelen bewerken](hpc-cache-edit-storage.md) voor meer informatie.
