---
title: Opslag toevoegen aan een Azure HPC-cache
description: Hoe u opslag doelen definieert zodat uw Azure HPC-cache uw on-premises NFS-systeem of Azure Blob-containers voor lange termijn bestands opslag kan gebruiken
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: v-erkell
ms.openlocfilehash: 4554214b74b4d09fa40e355270208bebda4076b7
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70775258"
---
# <a name="add-storage"></a>Opslag toevoegen

*Opslag doelen* zijn back-end opslag voor bestanden die toegankelijk zijn via een Azure HPC-cache-exemplaar. U kunt NFS-opslag, zoals een on-premises hardwaresysteem, toevoegen of gegevens opslaan in Azure Blob.

U kunt Maxi maal tien verschillende opslag doelen definiëren voor één cache. De cache geeft alle opslag doelen weer in één geaggregeerde naam ruimte.

Houd er rekening mee dat de export van de opslag toegankelijk moet zijn vanuit het virtuele netwerk van uw cache. Voor on-premises hardwarematige opslag moet u mogelijk een DNS-server instellen die hostnamen voor NFS-opslag toegang kan omzetten. Meer informatie vindt u in [DNS-toegang](hpc-cache-prereqs.md#dns-access).

U kunt opslag doelen toevoegen tijdens het maken van uw Azure HPC-cache of later. De procedure wijkt enigszins af, afhankelijk van of u Azure Blob-opslag of een NFS-export toevoegt. Hieronder vindt u meer informatie.

## <a name="add-storage-targets-while-creating-the-cache"></a>Opslag doelen toevoegen tijdens het maken van de cache

Gebruik het tabblad **opslag doelen** van de wizard cache maken om opslag te definiëren op het moment dat u het cache-exemplaar maakt.

![scherm afbeelding van de pagina opslag doelen](media/create-targets.png)

Klik op de koppeling **opslag doel toevoegen** om opslag toe te voegen.

## <a name="add-storage-targets-from-the-cache"></a>Opslag doelen uit de cache toevoegen

Open in de Azure Portal uw cache-exemplaar en klik op **opslag doelen** op de zijbalk aan de linkerkant. De pagina opslag doel bevat een lijst met alle bestaande doelen en bevat een koppeling om een nieuwe toe te voegen.

## <a name="add-a-new-azure-blob-storage-target"></a>Een nieuw Azure Blob-opslag doel toevoegen

Een nieuw Blob-opslag doel vereist een lege BLOB-container of een container die is gevuld met gegevens in de bestands indeling van de Azure HPC-cache in de Cloud. Lees meer over het vooraf laden van een BLOB-container in [gegevens verplaatsen naar Azure Blob-opslag](hpc-cache-ingest.md).

Als u een Azure Blob-container wilt definiëren, voert u deze informatie in.

![scherm afbeelding van de pagina opslag doel toevoegen, gevuld met informatie voor een nieuw Azure Blob-opslag doel](media/hpc-cache-add-blob.png)

* **Naam van opslag doel** : Stel een naam in die dit opslag doel identificeert in de Azure HPC-cache.
* **Doel type** : Kies **BLOB**.
* **Opslag account** : Selecteer het account met de container waarnaar moet worden verwezen.

  U moet het cache-exemplaar toestemming geven om toegang te krijgen tot het opslag account zoals beschreven in [de toegangs functies toevoegen](#add-the-access-control-roles-to-your-account).
* **Opslag container** : Selecteer de BLOB-container voor dit doel.

* **Pad naar virtuele naam ruimte** : Stel het client gerichte bestandspad in voor dit opslag doel. Lees de [geaggregeerde naam ruimte configureren](hpc-cache-namespace.md) voor meer informatie over de functie virtuele naam ruimte.

<!--  The namespace path value must end with a slash (``/``) and should not start with one.  -->

Wanneer u klaar bent, klikt u op **OK** om het opslag doel toe te voegen.

### <a name="add-the-access-control-roles-to-your-account"></a>De toegangs beheer rollen toevoegen aan uw account

De Azure HPC-cache maakt gebruik [van op rollen gebaseerd toegangs beheer (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/index) om de cache toepassing toegang te geven tot uw opslag account voor Azure Blob Storage-doelen.

De eigenaar van het opslag account moet expliciet de rol [Inzender voor het opslag account](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-contributor) inrollen en de [Gegevensinzender voor opslag-blobs](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) voor de gebruiker StorageCache resource provider toevoegen.

U kunt dit vooraf doen, of door te klikken op een koppeling op de pagina waar u een Blob-opslag doel toevoegt.

Stappen voor het toevoegen van de RBAC-rollen:

1. Open de pagina **toegangs beheer (IAM)** voor het opslag account. (De koppeling op de pagina **opslag doel toevoegen** opent deze pagina automatisch voor het geselecteerde account.)

1. Klik boven aan de pagina en kies **een roltoewijzing toevoegen.** **+**

1. Selecteer de rol ' Inzender voor opslag accounts ' in de lijst.

1. In het veld **toegang toewijzen aan kunt u** de geselecteerde standaard waarde (' Azure AD-gebruiker,-groep of Service-Principal ') laten staan.  

1. Zoek in het veld **selecteren** naar ' storagecache '.  Deze teken reeks moet overeenkomen met een beveiligingsprincipal met de naam ' HPC-cache resource provider '. Klik op die principal om deze te selecteren.

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

U kunt meerdere naam ruimte paden maken die verschillende exports op hetzelfde NFS-opslag systeem vertegenwoordigen, maar u moet ze allemaal maken op basis van één opslag doel.

Vul voor elke export deze waarden in:

* **Pad naar virtuele naam ruimte** : Stel het client gerichte bestandspad in voor dit opslag doel. Lees de [geaggregeerde naam ruimte configureren](hpc-cache-namespace.md) voor meer informatie over de functie virtuele naam ruimte.

<!--  The virtual path should start with a slash ``/``. -->

* **Pad naar NFS-export** -Geef het pad op naar de NFS-export.

* **Pad naar de submap** : als u een specifieke submap van de export wilt koppelen, voert u deze hier in. Als dat niet het geval is, laat u dit veld leeg. 

Wanneer u klaar bent, klikt u op **OK** om het opslag doel toe te voegen.

### <a name="choose-a-usage-model"></a>Kies een gebruiks model 
<!-- link in GUI to this heading -->

Wanneer u een opslag doel maakt dat verwijst naar een NFS-opslag systeem, moet u het *gebruiks model* voor dat doel kiezen. Dit model bepaalt hoe de gegevens in de cache worden opgeslagen.

* Zwaren: als u de cache voornamelijk gebruikt voor het versnellen van gegevens lees toegang, kiest u deze optie. 

* Lezen/schrijven: als clients de cache gebruiken om te lezen en te schrijven, kiest u deze optie.

* Clients slaan de cache over. Kies deze optie als uw clients gegevens rechtstreeks naar het opslag systeem schrijven zonder eerst naar de cache te schrijven.

## <a name="next-steps"></a>Volgende stappen

Nadat u opslag doelen hebt gemaakt, kunt u een van de volgende taken uitvoeren:

* [De Azure HPC-cache koppelen](hpc-cache-mount.md)
* [Gegevens verplaatsen naar Azure Blob-opslag](hpc-cache-ingest.md)