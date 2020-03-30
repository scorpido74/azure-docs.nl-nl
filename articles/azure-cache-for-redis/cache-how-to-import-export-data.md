---
title: Gegevens importeren en exporteren in Azure-cache voor Redis
description: Meer informatie over het importeren en exporteren van gegevens van en naar blob-opslag met uw premium Azure-cache voor Redis-exemplaren
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/31/2017
ms.author: yegu
ms.openlocfilehash: 29ad5ca6c9058b88a539c7a3bb8ace4d9a65083a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278088"
---
# <a name="import-and-export-data-in-azure-cache-for-redis"></a>Gegevens importeren en exporteren in Azure-cache voor Redis
Importeren/exporteren is een Azure-cache voor Redis-gegevensbeheerbewerking, waarmee u gegevens importeren in Azure Cache voor Redis of gegevens uit Azure Cache voor Redis exporteren door een Azure Cache voor Redis Database (RDB) te importeren van een premiumcache naar een blob in een Azure Storage-account.

- **Exporteren** - u uw Azure-cache voor Redis RDB-momentopnamen exporteren naar een paginablob.
- **Importeren** - u uw Azure-cache importeren voor Redis RDB-momentopnamen uit een paginablob of een blokblob.

Met Importeren/exporteren u migreren tussen verschillende Azure-cache voor Redis-exemplaren of de cache vullen met gegevens voordat u deze gebruikt.

In dit artikel vindt u een handleiding voor het importeren en exporteren van gegevens met Azure Cache voor Redis en worden de antwoorden op veelgestelde vragen gegeven.

> [!IMPORTANT]
> Import/export is alleen beschikbaar voor [caches met premiumlagen.](cache-premium-tier-intro.md)
>
>

## <a name="import"></a>Importeren
Import kan worden gebruikt om Redis-compatibele RDB-bestanden van elke Redis-server te brengen die in elke cloud of omgeving wordt uitgevoerd, waaronder Redis die wordt uitgevoerd op Linux, Windows of een cloudprovider zoals Amazon Web Services en anderen. Het importeren van gegevens is een eenvoudige manier om een cache te maken met vooraf ingevulde gegevens. Tijdens het importproces laadt Azure Cache voor Redis de RDB-bestanden uit Azure-opslag in het geheugen en voegt u de sleutels vervolgens in de cache in.

> [!NOTE]
> Voordat u begint met de importbewerking, moet u ervoor zorgen dat uw RDB-bestand of bestanden (Redis Database) worden geüpload naar pagina's of blobs blokkeren in Azure-opslag, in dezelfde regio en abonnement als uw Azure-cache voor redis-instantie. Zie [Aan de slag met Azure Blob-opslag](../storage/blobs/storage-dotnet-how-to-use-blobs.md)voor meer informatie. Als u uw RDB-bestand hebt geëxporteerd met de functie [Azure Cache for Redis Export,](#export) is uw RDB-bestand al opgeslagen in een paginablob en is het klaar om te worden geïmporteerd.
>
>

1. Als u een of meer blobs met geëxporteerde cache wilt importeren, [bladert u naar uw cache](cache-configure.md#configure-azure-cache-for-redis-settings) in de Azure-portal en klikt u op Gegevens **importeren** in het **menu Resource**.

    ![Gegevens importeren](./media/cache-how-to-import-export-data/cache-import-data.png)
2. Klik **op Blob(s) kiezen** en selecteer het opslagaccount dat de gegevens bevat die u wilt importeren.

    ![Opslagaccount kiezen](./media/cache-how-to-import-export-data/cache-import-choose-storage-account.png)
3. Klik op de container die de gegevens bevat die u wilt importeren.

    ![Container kiezen](./media/cache-how-to-import-export-data/cache-import-choose-container.png)
4. Selecteer een of meer blobs die u wilt importeren door op het gebied links van de blobnaam te klikken en klik vervolgens op **Selecteren**.

    ![Blobs kiezen](./media/cache-how-to-import-export-data/cache-import-choose-blobs.png)
5. Klik **op Importeren** om het importproces te starten.

   > [!IMPORTANT]
   > De cache is niet toegankelijk voor cacheclients tijdens het importproces en alle bestaande gegevens in de cache worden verwijderd.
   >
   >

    ![Importeren](./media/cache-how-to-import-export-data/cache-import-blobs.png)

    U de voortgang van de importbewerking controleren door de meldingen van de Azure-portal te volgen of door de gebeurtenissen in het [controlelogboek te](../azure-resource-manager/management/view-activity-logs.md)bekijken.

    ![Voortgang importeren](./media/cache-how-to-import-export-data/cache-import-data-import-complete.png)

## <a name="export"></a>Exporteren
Met exporteren u de gegevens die zijn opgeslagen in Azure Cache voor Redis exporteren naar RDB-bestanden die compatibel zijn met Redis. U deze functie gebruiken om gegevens van de ene Azure-cache voor Redis-instantie naar een andere of naar een andere Redis-server te verplaatsen. Tijdens het exportproces wordt een tijdelijk bestand gemaakt op de VM waarmee de Azure-cache voor de serverinstantie Van Redis wordt gehost en wordt het bestand geüpload naar het aangewezen opslagaccount. Wanneer de exportbewerking is voltooid met een status van succes of fout, wordt het tijdelijke bestand verwijderd.

1. Als u de huidige inhoud van de cache naar opslag wilt exporteren, [bladert u naar uw cache](cache-configure.md#configure-azure-cache-for-redis-settings) in de Azure-portal en klikt u op Gegevens **exporteren** in het **menu Resource**.

    ![Opslagcontainer kiezen](./media/cache-how-to-import-export-data/cache-export-data-choose-storage-container.png)
2. Klik **op Opslagcontainer kiezen** en selecteer het gewenste opslagaccount. Het opslagaccount moet zich in hetzelfde abonnement en dezelfde regio bevinden als uw cache.

   > [!IMPORTANT]
   > Exporteren werkt met paginablobs, die worden ondersteund door zowel klassieke als Resource Manager-opslagaccounts, maar op dit moment niet worden ondersteund door Blob-opslagaccounts. Zie [Overzicht van Azure-opslagaccount](../storage/common/storage-account-overview.md) voor meer informatie.
   >

    ![Storage-account](./media/cache-how-to-import-export-data/cache-export-data-choose-account.png)
3. Kies de gewenste blobcontainer en klik op **Selecteren**. Als u een nieuwe container wilt gebruiken, klikt u op **Container toevoegen** om deze eerst toe te voegen en selecteert u deze in de lijst.

    ![Opslagcontainer kiezen](./media/cache-how-to-import-export-data/cache-export-data-container.png)
4. Typ een **voorvoegsel van blobnaam** en klik op **Exporteren** om het exportproces te starten. Het voorvoegsel van de blobnaam wordt gebruikt om de namen van bestanden die door deze exportbewerking worden gegenereerd, vooraf aan te gaan.

    ![Exporteren](./media/cache-how-to-import-export-data/cache-export-data.png)

    U de voortgang van de exportbewerking controleren door de meldingen van de Azure-portal te volgen of door de gebeurtenissen in het [controlelogboek te](../azure-resource-manager/management/view-activity-logs.md)bekijken.

    ![Gegevens volledig exporteren](./media/cache-how-to-import-export-data/cache-export-data-export-complete.png)

    Caches blijven beschikbaar voor gebruik tijdens het exportproces.

## <a name="importexport-faq"></a>Veelgestelde vragen over importeren/exporteren
Deze sectie bevat veelgestelde vragen over de functie Importeren/exporteren.

* [Welke prijsniveaus kunnen Import/Export gebruiken?](#what-pricing-tiers-can-use-importexport)
* [Kan ik gegevens importeren van een Redis-server?](#can-i-import-data-from-any-redis-server)
* [Welke RDB-versies kan ik importeren?](#what-rdb-versions-can-i-import)
* [Is mijn cache beschikbaar tijdens een import/exportbewerking?](#is-my-cache-available-during-an-importexport-operation)
* [Kan ik Import/Export gebruiken met het redis-cluster?](#can-i-use-importexport-with-redis-cluster)
* [Hoe werkt Importeren/exporteren met een aangepaste databasesinstelling?](#how-does-importexport-work-with-a-custom-databases-setting)
* [Hoe verschilt import/export van de persistentie van Redis?](#how-is-importexport-different-from-redis-persistence)
* [Kan ik Import/Export automatiseren met PowerShell, CLI of andere beheerclients?](#can-i-automate-importexport-using-powershell-cli-or-other-management-clients)
* [Ik heb een time-outfout ontvangen tijdens mijn import/export-bewerking. Wat betekent het?](#i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean)
* [Er is een fout opgetreden bij het exporteren van mijn gegevens naar Azure Blob Storage. Wat is er gebeurd?](#i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened)

### <a name="what-pricing-tiers-can-use-importexport"></a>Welke prijsniveaus kunnen Import/Export gebruiken?
Import/export is alleen beschikbaar in de prijscategorie premium.

### <a name="can-i-import-data-from-any-redis-server"></a>Kan ik gegevens importeren van een Redis-server?
Ja, naast het importeren van gegevens die zijn geëxporteerd vanuit Azure Cache voor Redis-exemplaren, u RDB-bestanden importeren vanaf elke Redis-server die wordt uitgevoerd in elke cloud of omgeving, zoals Linux, Windows of cloudproviders zoals Amazon Web Services. Upload hiervoor het RDB-bestand van de gewenste Redis-server naar een pagina of blokkeert blob in een Azure Storage-account en importeert het vervolgens in uw premium Azure-cache voor bijvoorbeeld Redis. U bijvoorbeeld de gegevens uit uw productiecache exporteren en importeren in een cache die wordt gebruikt als onderdeel van een faseringsomgeving voor testen of migratie.

> [!IMPORTANT]
> Als u gegevens wilt importeren die zijn geëxporteerd vanaf andere Redis-servers dan Azure Cache voor Redis wanneer u een paginablob gebruikt, moet de paginablobgrootte worden uitgelijnd op een grens van 512 byte. Zie [Voorbeeldpagina blob uploaden](https://github.com/JimRoberts-MS/SamplePageBlobUpload)voor voorbeeldcode om vereiste byte-opvulling uit te voeren.
>
>

### <a name="what-rdb-versions-can-i-import"></a>Welke RDB-versies kan ik importeren?

Azure Cache for Redis ondersteunt RDB-import via RDB-versie 7.

### <a name="is-my-cache-available-during-an-importexport-operation"></a>Is mijn cache beschikbaar tijdens een import/exportbewerking?
* **Exporteren** - Caches blijven beschikbaar en u uw cache blijven gebruiken tijdens een exportbewerking.
* **Import** - Caches worden niet meer beschikbaar wanneer een importbewerking wordt gestart en worden beschikbaar voor gebruik wanneer de importbewerking is voltooid.

### <a name="can-i-use-importexport-with-redis-cluster"></a>Kan ik Import/Export gebruiken met het redis-cluster?
Ja, en u importeren/exporteren tussen een geclusterde cache en een niet-geclusterde cache. Aangezien het Redis-cluster [alleen database 0 ondersteunt,](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)worden alle gegevens in andere databases dan 0 niet geïmporteerd. Wanneer geclusterde cachegegevens worden geïmporteerd, worden de sleutels opnieuw verdeeld over de shards van het cluster.

### <a name="how-does-importexport-work-with-a-custom-databases-setting"></a>Hoe werkt Importeren/exporteren met een aangepaste databasesinstelling?
Sommige prijsniveaus hebben verschillende [databaseslimieten,](cache-configure.md#databases)dus er zijn enkele overwegingen bij `databases` het importeren als u een aangepaste waarde hebt geconfigureerd voor de instelling tijdens het maken van cache.

* Wanneer u importeert naar een `databases` prijscategorie met een ondergrens dan de laag waaruit u exporteerde:
  * Als u het standaardnummer `databases`van , dat is 16 voor alle prijsniveaus, gebruikt, gaan er geen gegevens verloren.
  * Als u een aangepast `databases` aantal gebruikt dat binnen de limieten valt voor de laag waarop u importeert, gaan er geen gegevens verloren.
  * Als uw geëxporteerde gegevens gegevens bevatten in een database die de limieten van de nieuwe laag overschrijdt, worden de gegevens uit die hogere databases niet geïmporteerd.

### <a name="how-is-importexport-different-from-redis-persistence"></a>Hoe verschilt import/export van de persistentie van Redis?
Azure Cache for Redis-persistentie stelt u in staat om gegevens die zijn opgeslagen in Redis tot Azure Storage te blijven bestaan. Wanneer persistentie is geconfigureerd, blijft Azure Cache voor Redis een momentopname van de Azure-cache voor Redis in een Redis-binaire indeling naar schijf op basis van een configureerbare back-upfrequentie. Als er een catastrofale gebeurtenis optreedt die zowel de primaire als de replicacache uitschakelt, worden de cachegegevens automatisch hersteld met behulp van de meest recente momentopname. Zie [Gegevenspersistentie configureren voor een Premium Azure-cache voor Redis voor](cache-how-to-premium-persistence.md)meer informatie.

Met Importeren/exporteren u gegevens naar Azure Cache voor Redis brengen of exporteren. Het configureert geen back-up en herstel met behulp van Redis persistentie.

### <a name="can-i-automate-importexport-using-powershell-cli-or-other-management-clients"></a>Kan ik Import/Export automatiseren met PowerShell, CLI of andere beheerclients?
Ja, voor PowerShell-instructies zie [Een Azure-cache importeren voor Redis](cache-how-to-manage-redis-cache-powershell.md#to-import-an-azure-cache-for-redis) en [Een Azure-cache exporteren voor Redis](cache-how-to-manage-redis-cache-powershell.md#to-export-an-azure-cache-for-redis).

### <a name="i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean"></a>Ik heb een time-outfout ontvangen tijdens mijn import/export-bewerking. Wat betekent dit?
Als u langer dan 15 minuten op het **gegevensblad Importeren** of **Gegevens exporteren** blijft voordat u de bewerking start, ontvangt u een foutmelding met een foutbericht dat vergelijkbaar is met het volgende voorbeeld:

    The request to import data into cache 'contoso55' failed with status 'error' and error 'One of the SAS URIs provided could not be used for the following reason: The SAS token end time (se) must be at least 1 hour from now and the start time (st), if given, must be at least 15 minutes in the past.

Om dit op te lossen, start u de import- of exportbewerking voordat 15 minuten zijn verstreken.

### <a name="i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened"></a>Er is een fout opgetreden bij het exporteren van mijn gegevens naar Azure Blob Storage. Wat is er gebeurd?
Exporteren werkt alleen met RDB-bestanden die zijn opgeslagen als paginablobs. Andere blobtypen worden momenteel niet ondersteund, waaronder Blob-opslagaccounts met hot- en cool-lagen. Zie [Overzicht van Azure-opslagaccount](../storage/common/storage-account-overview.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het gebruik van meer premium cachefuncties.

* [Inleiding tot de azure-cache voor de laag Redis Premium](cache-premium-tier-intro.md)
