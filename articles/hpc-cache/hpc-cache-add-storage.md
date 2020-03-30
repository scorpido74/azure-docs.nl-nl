---
title: Opslag toevoegen aan een Azure HPC-cache
description: Opslagdoelen definiëren zodat uw Azure HPC-cache uw on-premises NFS-systeem of Azure Blob-containers kan gebruiken voor bestandsopslag op lange termijn
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: rohogue
ms.openlocfilehash: a68bf06bad995f71bedf6a5bdedcb676737a8c61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271887"
---
# <a name="add-storage-targets"></a>Opslagdoelen toevoegen

*Opslagdoelen* zijn back-endopslag voor bestanden die toegankelijk zijn via een Azure HPC-cache-exemplaar. U NFS-opslag toevoegen (zoals een on-premises hardwaresysteem) of gegevens opslaan in Azure Blob.

U maximaal tien verschillende opslagdoelen voor één cache definiëren. De cache presenteert alle opslagdoelen in één geaggregeerde naamruimte.

Houd er rekening mee dat de opslagexport toegankelijk moet zijn vanuit het virtuele netwerk van uw cache. Voor on-premises hardwareopslag moet u mogelijk een DNS-server instellen die hostnamen voor NFS-opslagtoegang kan oplossen. Lees meer in [DNS access](hpc-cache-prereqs.md#dns-access).

Voeg opslagdoelen toe na het maken van uw cache. De procedure is iets anders, afhankelijk van of u Azure Blob-opslag of een NFS-export toevoegt. Details voor elk zijn hieronder.

## <a name="open-the-storage-targets-page"></a>De pagina opslagdoelen openen

Open in de Azure-portal uw cache-exemplaar en klik op **Opslagdoelen** op de linkerzijbalk. De pagina opslagdoelen bevat alle bestaande doelen en geeft een koppeling om een nieuwe toe te voegen.

![schermafbeelding van de koppeling opslagdoelen op de zijbalk, onder het kopje Configureren, tussen de categoriekoppen Instellingen en Controle](media/hpc-cache-storage-targets-sidebar.png)

## <a name="add-a-new-azure-blob-storage-target"></a>Een nieuw Azure Blob-opslagdoel toevoegen

Een nieuw Blob-opslagdoel heeft een lege Blob-container of een container nodig die wordt gevuld met gegevens in de cloudbestandssysteemindeling azure HPC-cache. Lees meer over het vooraf laden van een Blob-container in [Move-gegevens naar Azure Blob-opslag](hpc-cache-ingest.md).

U een nieuwe container vanaf deze pagina maken voordat u deze toevoegt.

Voer deze gegevens in om een Azure Blob-container te definiëren.

![schermafbeelding van de pagina Opslagdoel toevoegen, gevuld met informatie voor een nieuw Azure Blob-opslagdoel](media/hpc-cache-add-blob.png)

* **Naam opslagdoel** : stel een naam in die dit opslagdoel identificeert in de Azure HPC-cache.
* **Doeltype** - **Blob**kiezen .
* **Opslagaccount** - Selecteer het account dat u wilt gebruiken.

  U moet de cache-instantie autoriseren om toegang te krijgen tot het opslagaccount zoals beschreven in [De toegangsrollen toevoegen.](#add-the-access-control-roles-to-your-account)

  Lees [blob-opslagvereisten](hpc-cache-prereqs.md#blob-storage-requirements)voor informatie over het soort opslagaccount dat u gebruiken.

* **Opslagcontainer** : selecteer de Blob-container voor dit doel of klik op **Nieuw maken**.

  ![schermafbeelding van het dialoogvenster om naam en toegangsniveau (privé) voor nieuwe container op te geven](media/add-blob-new-container.png)

* **Pad voor virtuele naamruimte** - Stel het clientgerichte bestandspad in voor dit opslagdoel. Lees [Geaggregeerde naamruimte configureren](hpc-cache-namespace.md) voor meer informatie over de functie virtuele naamruimte.

Klik op **OK** om het opslagdoel toe te voegen als u klaar bent.

> [!NOTE]
> Als de firewall van uw opslagaccount is ingesteld om de toegang tot alleen 'geselecteerde netwerken' te beperken, gebruikt u de tijdelijke tijdelijke oplossing die is gedocumenteerd in [Firewall-instellingen voor blob-opslagaccount .](hpc-cache-blob-firewall-fix.md)

### <a name="add-the-access-control-roles-to-your-account"></a>De rollen voor toegangsbeheer toevoegen aan uw account

Azure HPC Cache maakt gebruik [van rbac (role-based access control)](https://docs.microsoft.com/azure/role-based-access-control/index) om de cacheservice te autoriseren om toegang te krijgen tot uw opslagaccount voor Azure Blob-opslagdoelen.

De eigenaar van het opslagaccount moet expliciet de rollen [Opslagaccountinzender](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-contributor) en [opslagblobgegevensbijdrager](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) toevoegen voor de gebruiker 'HPC-cachebronprovider'.

U dit van tevoren doen of door op een koppeling op de pagina te klikken waar u een Blob-opslagdoel toevoegt. Houd er rekening mee dat het tot vijf minuten kan duren voordat de rolinstellingen zich verspreiden via de Azure-omgeving, dus u moet een paar minuten wachten nadat u de rollen hebt toegevoegd voordat u een opslagdoel maakt.

Stappen om de RBAC-rollen toe te voegen:

1. Open de pagina **IAM (Access control)** voor het opslagaccount. (De koppeling op de pagina **Opslagdoel toevoegen** opent deze pagina automatisch voor het geselecteerde account.)

1. Klik **+** boven aan de pagina en kies **Een roltoewijzing toevoegen**.

1. Selecteer de rol 'Opslagaccountbijdrager' in de lijst.

1. Laat in het veld **Toegang tot** toewijzen de standaardwaarde geselecteerd ('Azure AD-gebruiker, -groep of serviceprincipal').  

1. Zoek **in het** veld Selecteren naar 'hpc'.  Deze tekenreeks moet overeenkomen met één serviceprincipal, genaamd "HPC Cache Resource Provider". Klik op die principal om het te selecteren.

   > [!NOTE]
   > Als een zoekopdracht naar "hpc" niet werkt, probeert u in plaats daarvan de tekenreeks "storagecache" te gebruiken. Gebruikers die lid zijn geworden van de previews (vóór GA) moeten mogelijk de oudere naam voor de serviceprincipal gebruiken.

1. Klik onderin op de knop **Opslaan.**

1. Herhaal dit proces om de rol 'Opslagblobgegevensbijdrager' toe te wijzen.  

![schermafbeelding van GUI voor roltoewijzing toevoegen](media/hpc-cache-add-role.png)

## <a name="add-a-new-nfs-storage-target"></a>Een nieuw NFS-opslagdoel toevoegen

Een NFS-opslagdoel heeft meer velden dan het blob-opslagdoel. In deze velden wordt aangegeven hoe u de opslagexport bereiken en hoe u de gegevens efficiënt in de cache opslaan. Met een NFS-opslagdoel u ook meerdere naamruimtepaden maken als de NFS-host meer dan één exportbeschikbaar heeft.

![Schermafbeelding van de pagina Opslagdoel toevoegen met NFS-doel gedefinieerd](media/hpc-cache-add-nfs-target.png)

Geef deze informatie op voor een door NFS backed storage-doel:

* **Naam opslagdoel** : stel een naam in die dit opslagdoel identificeert in de Azure HPC-cache.

* **Doeltype** - Kies **NFS**.

* **Hostname** - Voer het IP-adres of de volledig gekwalificeerde domeinnaam voor uw NFS-opslagsysteem in. (Gebruik een domeinnaam alleen als uw cache toegang heeft tot een DNS-server die de naam kan oplossen.)

* **Gebruiksmodel** - Kies hieronder een van de gegevenscachingprofielen op basis van uw werkstroom, beschreven in [Een gebruiksmodel kiezen.](#choose-a-usage-model)

### <a name="nfs-namespace-paths"></a>NFS-naamruimtepaden

Een NFS-opslagdoel kan meerdere virtuele paden hebben, zolang elk pad een andere export- of submap op hetzelfde opslagsysteem vertegenwoordigt.

Maak alle paden van één opslagdoel.

U op elk gewenst moment [naamruimtepaden toevoegen en bewerken](hpc-cache-edit-storage.md) op een opslagdoel.

Vul deze waarden in voor elk naamruimtepad:

* **Pad voor virtuele naamruimte** - Stel het clientgerichte bestandspad in voor dit opslagdoel. Lees [Geaggregeerde naamruimte configureren](hpc-cache-namespace.md) voor meer informatie over de functie virtuele naamruimte.

<!--  The virtual path should start with a slash ``/``. -->

* **NFS-exportpad** - Voer het pad naar de NFS-export in.

* **Submappad** - Als u een specifieke submap van de export wilt monteren, voert u deze hier in. Zo niet, laat dit veld leeg.

Klik op **OK** om het opslagdoel toe te voegen als u klaar bent.

### <a name="choose-a-usage-model"></a>Een gebruiksmodel kiezen
<!-- referenced from GUI - update aka.ms link if you change this heading -->

Wanneer u een opslagdoel maakt dat verwijst naar een NFS-opslagsysteem, moet u het *gebruiksmodel* voor dat doel kiezen. Dit model bepaalt hoe uw gegevens in de cache worden opgeslagen.

Er zijn drie opties:

* **Lees zware, zeldzame schrijft -** Gebruik deze optie als u wilt versnellen leestoegang tot bestanden die statisch zijn of zelden veranderd.

  Met deze optie worden bestanden opgeslagen die clients lezen, maar wordt er onmiddellijk doorgeschreven naar de back-endopslag. Bestanden die in de cache zijn opgeslagen, worden nooit vergeleken met de bestanden op het NFS-opslagvolume.

  Gebruik deze optie niet als er een risico bestaat dat een bestand rechtstreeks op het opslagsysteem wordt gewijzigd zonder het eerst naar de cache te schrijven. Als dat gebeurt, wordt de in de cache opgeslagen versie van het bestand nooit bijgewerkt met wijzigingen van de back-end en kan de gegevensset inconsistent worden.

* **Meer dan 15% schrijft** - Deze optie versnelt zowel lees- als schrijfprestaties. Wanneer u deze optie gebruikt, moeten alle clients toegang krijgen tot bestanden via de Azure HPC-cache in plaats van de back-endopslag rechtstreeks te monteren. De bestanden in de cache hebben recente wijzigingen die niet op de back-end zijn opgeslagen.

  In dit gebruiksmodel worden bestanden in de cache niet gecontroleerd op de bestanden op back-endopslag. De in de cache opgeslagen versie van het bestand wordt verondersteld actueler te zijn. Een gewijzigd bestand in de cache wordt alleen naar het back-endopslagsysteem geschreven nadat het een uur in de cache heeft gestaan zonder extra wijzigingen.

* **Clients schrijven naar het NFS-doel en omzeilen de cache** - Kies deze optie als clients in uw werkstroom gegevens rechtstreeks naar het opslagsysteem schrijven zonder eerst naar de cache te schrijven. Bestanden die clients aanvragen, worden in de cache opgeslagen, maar eventuele wijzigingen in die bestanden van de client worden onmiddellijk teruggestuurd naar het back-endopslagsysteem.

  Met dit gebruiksmodel worden de bestanden in de cache vaak gecontroleerd op de back-endversies voor updates. Met deze verificatie kunnen bestanden buiten de cache worden gewijzigd met behoud van de consistentie van de gegevens.

In deze tabel worden de verschillen in gebruiksmodel samengevat:

| Gebruiksmodel | Cachemodus | Back-endverificatie | Maximale terugschrijfvertraging |
| ---- | ---- | ---- | ---- |
| Lees zware, zeldzame schrijft | Lezen | Nooit | Geen |
| Meer dan 15% schrijft | Lezen/schrijven | Nooit | 1 uur |
| Clients omzeilen de cache | Lezen | 30 seconden | Geen |

## <a name="next-steps"></a>Volgende stappen

Na het maken van opslagdoelen moet u rekening houden met een van de volgende taken:

* [De Azure HPC-cache monteren](hpc-cache-mount.md)
* [Gegevens verplaatsen naar Azure Blob-opslag](hpc-cache-ingest.md)

Als u instellingen wilt bijwerken, u [een opslagdoel bewerken.](hpc-cache-edit-storage.md)
