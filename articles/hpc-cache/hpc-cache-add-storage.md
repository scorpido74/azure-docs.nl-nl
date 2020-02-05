---
title: Opslag toevoegen aan een Azure HPC-cache
description: Hoe u opslag doelen definieert zodat uw Azure HPC-cache uw on-premises NFS-systeem of Azure Blob-containers voor lange termijn bestands opslag kan gebruiken
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: rohogue
ms.openlocfilehash: a68bf06bad995f71bedf6a5bdedcb676737a8c61
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990603"
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

U kunt op deze pagina een nieuwe container maken voordat u deze toevoegt.

Als u een Azure Blob-container wilt definiëren, voert u deze informatie in.

![scherm afbeelding van de pagina opslag doel toevoegen, gevuld met informatie voor een nieuw Azure Blob-opslag doel](media/hpc-cache-add-blob.png)

* **Naam van opslag doel** : Stel een naam in die dit opslag doel identificeert in de Azure HPC-cache.
* **Doel type** : Kies **BLOB**.
* **Opslag account** : Selecteer het account dat u wilt gebruiken.

  U moet het cache-exemplaar toestemming geven om toegang te krijgen tot het opslag account zoals beschreven in [de toegangs functies toevoegen](#add-the-access-control-roles-to-your-account).

  Lees [vereisten voor Blob Storage](hpc-cache-prereqs.md#blob-storage-requirements)voor meer informatie over het type opslag account dat u kunt gebruiken.

* **Opslag container** : Selecteer de BLOB-container voor dit doel of klik op **nieuwe maken**.

  ![scherm afbeelding van het dialoog venster voor het opgeven van naam en toegangs niveau (privé) voor een nieuwe container](media/add-blob-new-container.png)

* **Pad naar virtuele naam ruimte** : Stel het client gerichte bestandspad in voor dit opslag doel. Lees de [geaggregeerde naam ruimte configureren](hpc-cache-namespace.md) voor meer informatie over de functie virtuele naam ruimte.

Wanneer u klaar bent, klikt u op **OK** om het opslag doel toe te voegen.

> [!NOTE]
> Als de firewall van uw opslag account is ingesteld op het beperken van de toegang tot alleen geselecteerde netwerken, gebruikt u de tijdelijke oplossing die is beschreven in de firewall-instellingen voor het [werk account voor Blob Storage](hpc-cache-blob-firewall-fix.md).

### <a name="add-the-access-control-roles-to-your-account"></a>De toegangs beheer rollen toevoegen aan uw account

Azure HPC cache maakt gebruik [van op rollen gebaseerd toegangs beheer (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/index) voor het machtigen van de cache service voor toegang tot uw opslag account voor Azure Blob Storage-doelen.

De eigenaar van het opslag account moet expliciet de Inzender rollen voor het [opslag account](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-contributor) en de [blobgegevens](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) voor de gebruiker ' HPC-cache resource provider ' toevoegen.

U kunt dit vooraf doen, of door te klikken op een koppeling op de pagina waar u een Blob-opslag doel toevoegt. Houd er rekening mee dat het Maxi maal vijf minuten kan duren voordat de rolinstellingen zijn door gegeven via de Azure-omgeving. u moet dus een paar minuten wachten nadat u de rollen hebt toegevoegd voordat u een opslag doel maakt.

Stappen voor het toevoegen van de RBAC-rollen:

1. Open de pagina **toegangs beheer (IAM)** voor het opslag account. (De koppeling op de pagina **opslag doel toevoegen** opent deze pagina automatisch voor het geselecteerde account.)

1. Klik op de **+** boven aan de pagina en kies **een roltoewijzing toevoegen**.

1. Selecteer de rol ' Inzender voor opslag accounts ' in de lijst.

1. In het veld **toegang toewijzen aan kunt u** de geselecteerde standaard waarde (' Azure AD-gebruiker,-groep of Service-Principal ') laten staan.  

1. In het veld **selecteren zoekt u** naar ' HPC '.  Deze teken reeks moet overeenkomen met een service-principal met de naam ' HPC-cache resource provider '. Klik op die principal om deze te selecteren.

   > [!NOTE]
   > Als een zoek opdracht voor "HPC" niet werkt, kunt u de teken reeks "storagecache" gebruiken. Gebruikers die zijn toegevoegd aan de voor beelden (vóór GA), moeten mogelijk de oudere naam voor de Service-Principal gebruiken.

1. Klik onderaan op de knop **Opslaan** .

1. Herhaal dit proces om de rol ' Storage BLOB data contributor ' toe te wijzen.  

![scherm opname van de gebruikers interface voor roltoewijzing toevoegen](media/hpc-cache-add-role.png)

## <a name="add-a-new-nfs-storage-target"></a>Een nieuw NFS-opslag doel toevoegen

Een NFS-opslag doel heeft meer velden dan het Blob-opslag doel. In deze velden wordt aangegeven hoe u de opslag exporteert en hoe u de gegevens efficiënt in de cache opslaat. Daarnaast kunt u met een NFS-opslag doel meerdere naam ruimte paden maken als de NFS-host meer dan één export beschikbaar heeft.

![Scherm afbeelding van de pagina opslag doel toevoegen met NFS-doel gedefinieerd](media/hpc-cache-add-nfs-target.png)

Geef deze informatie op voor een opslag doel met NFS-back-ups:

* **Naam van opslag doel** : Stel een naam in die dit opslag doel identificeert in de Azure HPC-cache.

* **Doel type** : Kies voor **NFS**.

* **Hostnaam** : Voer het IP-adres of de Fully Qualified Domain name voor uw NFS-opslag systeem in. (Gebruik alleen een domein naam als uw cache toegang heeft tot een DNS-server die de naam kan omzetten.)

* **Gebruiks model** : Kies een van de gegevens cache profielen op basis van uw werk stroom, zoals wordt beschreven in [een gebruiks model kiezen](#choose-a-usage-model).

### <a name="nfs-namespace-paths"></a>NFS-naam ruimte paden

Een NFS-opslag doel kan meerdere virtuele paden hebben, zolang elk pad een andere export of submap op hetzelfde opslag systeem vertegenwoordigt.

Alle paden van het ene opslag doel maken.

U kunt op elk gewenst moment naam ruimte paden op een opslag doel [toevoegen en bewerken](hpc-cache-edit-storage.md) .

Vul deze waarden in voor elk pad naar de naam ruimte:

* **Pad naar virtuele naam ruimte** : Stel het client gerichte bestandspad in voor dit opslag doel. Lees de [geaggregeerde naam ruimte configureren](hpc-cache-namespace.md) voor meer informatie over de functie virtuele naam ruimte.

<!--  The virtual path should start with a slash ``/``. -->

* **Pad naar NFS-export** -Geef het pad op naar de NFS-export.

* **Pad naar de submap** : als u een specifieke submap van de export wilt koppelen, voert u deze hier in. Als dat niet het geval is, laat u dit veld leeg.

Wanneer u klaar bent, klikt u op **OK** om het opslag doel toe te voegen.

### <a name="choose-a-usage-model"></a>Kies een gebruiks model
<!-- referenced from GUI - update aka.ms link if you change this heading -->

Wanneer u een opslag doel maakt dat verwijst naar een NFS-opslag systeem, moet u het *gebruiks model* voor dat doel kiezen. Dit model bepaalt hoe de gegevens in de cache worden opgeslagen.

Er zijn drie opties:

* **Lees zware, incidentele schrijf bewerkingen** : gebruik deze optie als u lees toegang tot bestanden wilt versnellen die statisch zijn of zelden worden gewijzigd.

  Met deze optie worden bestanden die door clients worden gelezen, in de cache opgeslagen, maar worden direct naar de back-end-opslag door gegeven. Bestanden die in de cache zijn opgeslagen, worden nooit vergeleken met de bestanden op het NFS-opslag volume.

  Gebruik deze optie niet als er een risico bestaat dat een bestand rechtstreeks op het opslag systeem kan worden gewijzigd zonder het eerst naar de cache te schrijven. Als dat gebeurt, wordt de versie van het bestand in de cache nooit bijgewerkt met de wijzigingen van de back-end en kan de gegevensset inconsistent worden.

* **Meer dan 15% schrijf bewerkingen** : deze optie versnelt de lees-en schrijf prestaties. Wanneer u deze optie gebruikt, moeten alle clients toegang hebben tot bestanden via de Azure HPC-cache in plaats van de back-end-opslag rechtstreeks te koppelen. De bestanden in de cache hebben recente wijzigingen die niet worden opgeslagen op de back-end.

  In dit gebruiks model worden bestanden in de cache niet gecontroleerd op basis van de bestanden in de back-end-opslag. Er wordt ervan uitgegaan dat de cache versie van het bestand meer actueel is. Een gewijzigd bestand in de cache wordt alleen naar het back-end-opslag systeem geschreven nadat het een uur in de cache heeft bevinden zonder extra wijzigingen.

* **Clients schrijven naar het NFS-doel, waarbij de cache wordt omzeild** : Kies deze optie als clients in uw werk stroom gegevens rechtstreeks naar het opslag systeem schrijven zonder eerst naar de cache te schrijven. Bestanden die door clients worden aangevraagd, worden in de cache opgeslagen, maar eventuele wijzigingen aan deze bestanden van de client worden onmiddellijk door gegeven aan het back-end-opslag systeem.

  Met dit gebruiks model worden de bestanden in de cache vaak gecontroleerd op basis van de back-end-versies voor updates. Met deze verificatie kunnen bestanden buiten de cache worden gewijzigd terwijl de consistentie van gegevens wordt behouden.

Deze tabel bevat een overzicht van de verschillen in het gebruiks model:

| Gebruiks model | Cache modus | Back-end-verificatie | Maximale vertraging voor terugschrijven |
| ---- | ---- | ---- | ---- |
| Zware, incidentele schrijf bewerkingen lezen | Lezen | Nooit | Geen |
| Meer dan 15% schrijf bewerkingen | Lezen/schrijven | Nooit | 1 uur |
| Clients slaan de cache over | Lezen | 30 seconden | Geen |

## <a name="next-steps"></a>Volgende stappen

Nadat u opslag doelen hebt gemaakt, kunt u een van de volgende taken uitvoeren:

* [De Azure HPC-cache koppelen](hpc-cache-mount.md)
* [Gegevens verplaatsen naar Azure Blob-opslag](hpc-cache-ingest.md)

Als u instellingen moet bijwerken, kunt u [een opslag doel bewerken](hpc-cache-edit-storage.md).
