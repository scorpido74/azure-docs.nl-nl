---
title: Richtlijnen voor noodherstel voor Avere vFXT voor Azure
description: Gegevens in Avere vFXT voor Azure beschermen tegen onbedoelde verwijdering of uitval
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: rohogue
ms.openlocfilehash: 28278f76497d6e9d0fee221bb4ef32fe6d369db0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75966655"
---
# <a name="disaster-recovery-guidance-for-avere-vfxt-for-azure"></a>Richtlijnen voor noodherstel voor Avere vFXT voor Azure

In dit artikel worden strategieën beschreven om uw Avere vFXT voor Azure-workflow te beschermen en wordt richtlijnen gegeven voor het maken van back-ups van gegevens, zodat u herstellen van ongevallen of uitval.

Avere vFXT voor Azure slaat tijdelijk gegevens op in de cache. Gegevens worden op lange termijn opgeslagen in back-endopslagsystemen - on-premises hardwaresystemen, Azure Blob-opslagcontainers of beide.

Om te waken tegen uitval en mogelijk gegevensverlies, moet u rekening houden met deze vier gebieden:

* Bescherming tegen downtime als een Avere vFXT voor Azure-systeem niet beschikbaar wordt
* Gegevens beveiligen in de clustercache
* Gegevens beveiligen in back-end NAS-hardwareopslag
* Gegevens beveiligen in back-end Azure Blob-cloudopslag

Elke Avere vFXT voor Azure-klant moet zijn eigen uitgebreide disaster recovery-plan maken met plannen voor deze items. U ook tolerantie inbouwen in toepassingen die u met het vFXT-cluster gebruikt. Lees de links in [Volgende stappen](#next-steps) voor hulp.

## <a name="protect-against-downtime"></a>Bescherm tegen downtime

Redundantie is ingebouwd in het Avere vFXT voor Azure-product:

* Het cluster is zeer beschikbaar en afzonderlijke clusterknooppunten kunnen mislukken met minimale onderbreking.
* Gegevens die in de cache worden gewijzigd, worden regelmatig naar back-end core-filers (hardware NAS of Azure Blob) geschreven voor langdurige opslag.

Elk Avere vFXT voor Azure-cluster moet zich in één beschikbaarheidszone bevinden, maar u redundante clusters in verschillende zones of verschillende regio's gebruiken om snel toegang te bieden in het geval van een regionale storing.

U ook opslagcontainers in meerdere regio's plaatsen als u zich zorgen maakt over het verlies van toegang tot gegevens. Houd er echter rekening mee dat transacties tussen regio's een hogere latentie en hogere kosten hebben dan transacties die binnen een regio blijven.

## <a name="protect-data-in-the-cluster-cache"></a>Gegevens beveiligen in de clustercache

Gegevens in de cache worden altijd naar de kernfilers geschreven voordat u deze regelmatig afsluit, maar bij een ongecontroleerde afsluiting kunnen gewijzigde gegevens in de cache verloren gaan.

Als u het cluster gebruikt om alleen bestandsreads te optimaliseren, zijn er geen wijzigingen te verliezen. Als u het cluster ook gebruikt om bestandswijzigingen (schrijft) in de cache te plaatsen, moet u overwegen om het cachebeleid van de cator van de [cator](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_manage_cache_policies.html) of de cachevan de cator<!-- link to legacy doc --> om aan te passen hoe vaak gegevens worden geschreven naar langdurige opslag.

In het algemeen moet uw herstelplan zich richten op het maken van back-endopslagsystemen, die meer gegevens bevatten en doorgaans belangrijker zijn voor het herstellen van uw workflow na een storing.

## <a name="protect-data-in-nas-core-filers"></a>Gegevens beveiligen in NAS-kernbestanden

Gebruik geaccepteerde methoden om gegevens te beschermen die zijn opgeslagen in een on-premises NAS-hardwarecore-filer, inclusief snapshots en volledige back-ups, zoals aanbevolen door de NAS-provider. Disaster recovery voor deze core filers valt buiten het bereik van dit artikel.

## <a name="protect-data-in-azure-blob-storage"></a>Gegevens beveiligen in Azure Blob-opslag

Avere vFXT voor Azure maakt gebruik van lrs (local-redundante opslag) voor Azure Blob-kernbestanden. Dit betekent dat de gegevens in uw Blob-containers automatisch worden gekopieerd voor bescherming tegen tijdelijke hardwarefouten in een datacenter.

In dit gedeelte vindt u tips om uw gegevens in Blob-opslag verder te beschermen tegen zeldzame regio-brede uitval of onbedoelde verwijderingen.

Aanbevolen procedures voor het beveiligen van gegevens in Azure Blob-opslag zijn:

* Kopieer uw kritieke gegevens regelmatig naar een ander opslagaccount in een andere regio (zo vaak als bepaald door uw noodherstelplan).
* Controle over de toegang tot gegevens op alle doelsystemen om onbedoelde verwijdering of beschadiging te voorkomen. Overweeg [resourcevergrendelingen](../azure-resource-manager/management/lock-resources.md) te gebruiken voor gegevensopslag.
* Schakel de avere vFXT voor [Azure-cloudsnapshotfunctie](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_snapshot_policies.html>) in voor uw Blob-kernbestanden.

### <a name="copy-avere-vfxt-core-filer-data-to-a-backup-account"></a>Avere vFXT-core filergegevens kopiëren naar een back-upaccount

Volg deze stappen om een back-up van gegevens in een ander account te maken.

1. Indien nodig, het genereren van een nieuwe encryptiesleutel en sla deze veilig buiten de getroffen systemen.

   Als uw gegevens worden versleuteld door het Avere vFXT voor Azure-cluster, moet u een nieuwe versleutelingssleutel genereren voordat u de gegevens kopieert naar een ander opslagaccount. Sla die sleutel en wachtwoord veilig op in een faciliteit die veilig is en die niet wordt beïnvloed door een regionale storing.

   U moet deze sleutel leveren bij het toevoegen van de container aan een cluster , zelfs als u deze opnieuw toevoegt aan het oorspronkelijke cluster.

   [Cloud-versleutelingsinstellingen lezen](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_encryption_settings.html>)<!-- link to legacy doc site --> voor gedetailleerde informatie.

   Als uw container alleen de ingebouwde versleuteling van Azure gebruikt, u deze stap overslaan.

1. Verwijder de core filer uit het systeem. Dit dwingt het cluster om alle gewijzigde gegevens te schrijven naar de back-end opslag.

   Hoewel u de core filer opnieuw moet toevoegen na de back-up, is het verwijderen ervan de beste manier om te garanderen dat alle gegevens volledig naar de back-end zijn geschreven. (De optie 'onderbreken' kan soms gewijzigde gegevens in de cache achterlaten.) <!-- xxx true? or just metadata? -->

   Noteer de naam- en knooppuntgegevens van de core filer (vermeld op de pagina **Naamruimte** in het bedieningspaneel), zodat u deze repliceren wanneer u de container opnieuw toevoegt na de back-up.

   Gebruik het clusterconfiguratiepaneel om de kernfiler te verwijderen. [Open het clusterconfiguratiepaneel](avere-vfxt-cluster-gui.md) en kies **Core filer** > **Core-filers beheren**. Zoek het opslagsysteem waarvan u een back-up wilt maken en gebruik de knop **Verwijderen** om het uit het cluster te verwijderen.

1. Maak een nieuwe, lege Blob-opslagcontainer in een ander opslagaccount in een andere regio.

1. Gebruik een handig kopieergereedschap om de gegevens op de core filer naar de nieuwe container te kopiëren. De kopie moet de gegevens repliceren zonder wijzigingen en zonder de eigen cloudbestandssysteemindeling te verstoren die door Avere vFXT voor Azure wordt gebruikt. Azure-gebaseerde hulpprogramma's omvatten [AzCopy,](../storage/common/storage-use-azcopy-v10.md) [Azure PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md)en [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md).

1. Nadat u de gegevens naar de back-upcontainer hebt gekopieerd, voegt u de oorspronkelijke container terug aan het cluster zoals beschreven in [Opslag configureren.](avere-vfxt-add-storage.md)

   * Gebruik dezelfde kernfilernaam en knooppuntgegevens, zodat clientwerkstromen niet hoeven te worden gewijzigd.
   * Stel de **inhoudswaarde bucket in** op de bestaande gegevensoptie.
   * Als de container door het cluster is versleuteld, moet u de huidige versleutelingssleutel voor de inhoud ervan invoeren. (Dit is de sleutel die u hebt bijgewerkt in stap één.)

Voor back-ups na de eerste, hoeft u geen nieuwe opslagcontainer te maken. Overweeg echter om elke keer dat u een back-up maakt een nieuwe versleutelingssleutel te genereren om ervoor te zorgen dat u de huidige sleutel hebt opgeslagen op een plaats die u zich herinnert.

### <a name="access-a-backup-data-source-during-an-outage"></a>Toegang tot een back-upgegevensbron tijdens een storing

Volg dit proces als u toegang wilt krijgen tot de back-upcontainer vanuit een Avere vFXT voor Azure-cluster:

1. Maak indien nodig een nieuw Avere vFXT voor Azure-cluster in een onaangetaste regio.

   > [!TIP]
   > Wanneer u een Avere vFXT voor Azure-cluster maakt, u een kopie van de creatiesjabloon en -parameters opslaan. Als u deze informatie opslaat bij het maken van uw primaire cluster, u deze gebruiken om een vervangend cluster met dezelfde eigenschappen te maken. Klik op de [overzichtspagina](avere-vfxt-deploy.md#validation-and-purchase) op de koppeling **Sjabloon downloaden en parameters.** Sla de informatie op in een bestand voordat u het cluster maakt.

1. Voeg een nieuwe cloudcore-filer toe die naar de dubbele Blob-container verwijst.

   Zorg ervoor dat u opgeeft dat de doelcontainer al gegevens bevat in de instelling **bucketinhoud** van de wizard Basisfiler maken. (Het systeem moet u waarschuwen als u deze set per ongeluk aan **Leeg**laat .)  <!-- you can't add a populated volume at cluster creation time via template, only create a fresh one -->

1. Werk clients zo nodig bij, zodat ze het nieuwe cluster of de nieuwe core filer monteren in plaats van het origineel. (Als u de vervangende kernfiler toevoegt met dezelfde naam en knooppuntpad als de oorspronkelijke container, hoeft u clientprocessen niet bij te werken, tenzij u het nieuwe cluster op een nieuw IP-adres moet monteren.)

## <a name="next-steps"></a>Volgende stappen

* Lees [Clustertuning](avere-vfxt-tuning.md)voor meer informatie over het aanpassen van instellingen voor Avere vFXT voor Azure.
* Meer informatie over noodherstel en het bouwen van veerkrachtige toepassingen in Azure:

  * [Technische richtlijnen voor flexibiliteit van Azure](https://docs.microsoft.com/azure/architecture/framework/resiliency/overview)
  * [Herstel na een serviceonderbreking in de gehele regio](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region)
  * [Herstel na noodgevallen en hoge beschikbaarheid voor Azure-toepassingen](<https://docs.microsoft.com/azure/resiliency/resiliency-disaster-recovery-high-availability-azure-applications>)
  <!-- can't find these in the source tree to use relative links -->
