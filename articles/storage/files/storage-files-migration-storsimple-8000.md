---
title: StorSimple 8000-serie migreren naar Azure File Sync
description: Meer informatie over het migreren van een StorSimple 8100-of 8600-apparaat naar Azure File Sync.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 10/16/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: dcb690829aaed1d5a256f53766f05c922f8b2a9a
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92331079"
---
# <a name="storsimple-8100-and-8600-migration-to-azure-file-sync"></a>StorSimple 8100 en 8600 migratie naar Azure File Sync

De StorSimple 8000-serie wordt vertegenwoordigd door de 8100 of de 8600 fysieke, on-premises apparaten en hun Cloud service onderdelen. Het is mogelijk om de gegevens van een van deze apparaten te migreren naar een Azure File Sync omgeving. Azure File Sync is de standaard-en strategische Azure-service voor de lange termijn waarmee StorSimple-apparaten kunnen worden gemigreerd.

De StorSimple 8000-serie bereikt het [einde van de levens duur](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%208000%20Series) in december 2022. Het is belang rijk dat u zo snel mogelijk begint met het plannen van de migratie. Dit artikel bevat de benodigde achtergrond kennis en migraties stappen voor een geslaagde migratie naar Azure File Sync. 

## <a name="phase-1-prepare-for-migration"></a>Fase 1: voorbereiden op migratie

Deze sectie bevat de stappen die u moet nemen aan het begin van de migratie van StorSimple-volumes naar Azure-bestands shares.

### <a name="inventory"></a>Inventaris
Wanneer u begint met het plannen van de migratie, moet u eerst alle StorSimple-apparaten en-volumes identificeren die u nodig hebt om te migreren. Zodra u dit hebt gedaan, kunt u besluiten op het beste migratie traject voor u.

* StorSimple fysieke apparaten (8000 Series) deze migratie handleiding gebruiken. 
* Virtuele apparaten, [StorSimple 1200-serie, gebruiken een andere migratie handleiding](storage-files-migration-storsimple-1200.md).

### <a name="migration-cost-summary"></a>Samen vatting migratie kosten
Migraties naar Azure-bestands shares van StorSimple-volumes via gegevens transformatie taken in een StorSimple Data Manager resource zijn gratis. Er zijn echter andere kosten die kunnen worden gemaakt tijdens en na een migratie:

* **Netwerk** uitgaand: Uw StorSimple-bestanden bevinden zich in een opslag account binnen een specifieke Azure-regio. Als u de Azure-bestands shares inricht in in een opslag account dat zich in dezelfde Azure-regio bevindt, worden er geen kosten voor uitgaand verkeer gegenereerd. U kunt uw bestanden verplaatsen naar een opslag account in een andere regio als onderdeel van deze migratie. In dat geval zijn de kosten voor uitgaand verkeer van toepassing op u.
* **Azure-bestands share transacties:** Wanneer bestanden worden gekopieerd naar een Azure-bestands share (als onderdeel van een migratie of buiten één), worden de transactie kosten toegepast als bestanden en meta gegevens worden geschreven. Start als best practice uw Azure-bestands share tijdens de migratie op de laag geoptimaliseerd voor trans acties. Schakel over naar de gewenste laag nadat de migratie is voltooid. Met de onderstaande fasen wordt dit op het juiste punt aangeroepen.
* **Een Azure-bestands share-laag wijzigen:** De laag van de kosten transacties van een Azure-bestands share wijzigen. In de meeste gevallen is het rendabeler om het advies van het vorige punt te volgen.
* **Opslag kosten:** Wanneer deze migratie begint met het kopiëren van bestanden naar een Azure-bestands share, wordt Azure Files opslag verbruikt en gefactureerd. 
* **StorSimple:** Totdat u de mogelijkheid hebt om de inrichting van de StorSimple-apparaten en-opslag accounts ongedaan te maken, blijven de StorSimple kosten voor opslag, back-ups en toestellen gewoon optreden.

### <a name="direct-share-access-vs-azure-file-sync"></a>Rechtstreekse toegang tot share versus Azure File Sync
Azure-bestands shares openen een volledige nieuwe wereld van kansen voor het structureren van uw implementatie van bestands Services. Een Azure-bestands share is slechts een SMB-share in de Cloud, die u kunt instellen om gebruikers rechtstreeks toegang te geven tot het SMB-protocol met de vertrouwde Kerberos-verificatie en bestaande NTFS-machtigingen (bestanden en mappen Acl's) die systeem eigen werken. Meer [informatie over toegang op basis van identiteiten naar Azure-bestands shares](storage-files-active-directory-overview.md).

Een alternatief voor directe toegang is een [Azure file sync](https://aka.ms/AFS). Azure File Sync is een direct analoog voor de mogelijkheid van StorSimple om veelgebruikte bestanden on-premises op te slaan in de cache. 

Azure File Sync is een micro soft-Cloud service, gebaseerd op twee hoofd onderdelen:

* Bestands synchronisatie en Cloud lagen.
* Bestands shares als systeem eigen opslag in azure, waartoe toegang kan worden verkregen via meerdere protocollen zoals SMB en de REST van het bestand. 

Azure-bestands shares behouden belang rijke aspecten van de bestands kwaliteit van opgeslagen bestanden als kenmerken, machtigingen en tijds tempels. Met Azure-bestands shares is het niet meer nodig om een toepassing of service te interpreteren van de bestanden en mappen die zijn opgeslagen in de Cloud. U kunt deze systeem eigen toegang tot de vertrouwde protocollen en clients, zoals Windows Verkenner. Met Azure-bestands shares kunt u algemene bestands Server gegevens en toepassings gegevens opslaan in de Cloud. Het maken van een back-up van een Azure-bestands share is een ingebouwde functionaliteit die verder kan worden verbeterd door Azure Backup.

Dit artikel is gericht op de migratie stappen. Als u vóór de migratie meer wilt weten over Azure File Sync, raden we u aan om de volgende artikelen te lezen:

* [Azure File Sync-overzicht](https://aka.ms/AFS "Overzicht")
* [Azure File Sync-implementatie handleiding](storage-sync-files-deployment-guide.md)

### <a name="storsimple-service-data-encryption-key"></a>Versleutelings sleutel voor StorSimple-service gegevens
Wanneer u uw StorSimple-apparaat voor het eerst instelt, is er een '*service gegevens versleutelings sleutel*' gegenereerd en wordt u geadviseerd om de sleutel veilig op te slaan.
Deze sleutel wordt gebruikt voor het versleutelen van alle gegevens in het gekoppelde Azure Storage-account. in het StorSimple-apparaat worden uw bestanden opgeslagen in.

De versleutelings sleutel voor service gegevens is nodig voor een geslaagde migratie. Het is nu een goed moment om deze sleutel op te halen uit uw records, voor elk van de apparaten in uw inventaris.

Als u de sleutels in uw records niet kunt vinden, kunt u de sleutel van het apparaat ophalen. Elk apparaat heeft een unieke versleutelings sleutel.

* Een ondersteunings aanvraag met Microsoft Azure indienen via de Azure Portal. De inhoud van de aanvraag moet de serie nummers van het StorSimple-apparaat en de aanvraag voor het ophalen van de versleutelings sleutel voor de service gegevens hebben. 
* Een StorSimple-ondersteunings technicus neemt contact met u op voor een aanvraag voor een scherm deel van de vergadering.
* Zorg ervoor dat voordat de vergadering begint, u verbinding maakt met uw StorSimple-apparaat [via een seriële console](../../storsimple/storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console) of via een [externe Power shell-sessie](../../storsimple/storsimple-8000-windows-powershell-administration.md#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple). 

> [!CAUTION]
> Wanneer u wilt bepalen hoe u verbinding maakt met uw StorSimple-apparaat, kunt u het volgende overwegen:
> * Het maken van een verbinding via een HTTPS-sessie is de veiligste en aanbevolen optie.
> * Rechtstreeks verbinding maken met de seriële console van het apparaat is beveiligd, maar het maken van verbinding met de seriële console via netwerk switches is niet. 
> * HTTP-sessie verbindingen zijn een optie, maar zijn **niet versleuteld** en worden daarom ook niet aanbevolen, tenzij ze worden gebruikt in een gesloten, vertrouwd netwerk.

### <a name="storsimple-volume-backups"></a>StorSimple volume back-ups
StorSimple biedt differentiële back-ups op volume niveau. Azure-bestands shares hebben ook de mogelijkheid om share-moment opnamen te noemen.

Bepaal of u als onderdeel van de migratie ook een back-up wilt verplaatsen.

> [!CAUTION]
> Stop hier als u back-ups van StorSimple-volumes moet migreren.
>
> U kunt momenteel alleen de meest recente back-up van het volume migreren. Ondersteuning voor back-upmigratie arriveert aan het einde van 2020.
> Als u nu begint, kunt u uw back-ups later niet meer uitvoeren.
> In de aanstaande versie moeten back-ups worden teruggezet naar de Azure-bestands shares van oudste naar nieuwste, waarbij de moment opnamen van de Azure-bestands shares zijn gemaakt.

Als u alleen de Live gegevens wilt migreren en geen vereisten voor back-ups hebt, kunt u door gaan met het volgen van deze hand leiding.
Als u slechts een zeer korte termijn een retentie vereiste voor een back-up van een maand of twee hebt, kunt u ervoor kiezen om de migratie nu voort te zetten en de inrichting van uw StorSimple-resources na die periode alleen ongedaan te maken. Met deze aanpak kunt u zoveel Back-upgeschiedenis maken op de Azure-bestands share als u dat nodig hebt. Voor de tijd dat u beide systemen uitvoert, zijn er extra kosten van toepassing, waardoor dit geen benadering is die u moet overwegen als u meer dan een zeer korte retentie van back-ups nodig hebt.

### <a name="map-your-existing-storsimple-volumes-to-azure-file-shares"></a>Uw bestaande StorSimple-volumes toewijzen aan Azure-bestands shares
[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="number-of-storage-accounts"></a>Aantal opslag accounts
Uw migratie zal waarschijnlijk profiteren van een implementatie van meerdere opslag accounts, die elk een kleiner aantal Azure-bestands shares bevatten.

Als uw bestands shares Maxi maal actief zijn (gebruikt door veel gebruikers en/of toepassingen), kunnen er slechts twee Azure-bestands shares de prestatie limiet van uw opslag account bereiken. Daarom is het best practice te migreren naar meerdere opslag accounts, elk met hun eigen afzonderlijke bestands shares en meestal niet meer dan twee of drie shares per opslag account.

Een best practice is het implementeren van opslag accounts met één bestands share. U kunt meerdere Azure-bestands shares in hetzelfde opslag account poolen voor het geval u archief shares hebt.

Deze overwegingen zijn van toepassing op [directe Cloud toegang](#direct-share-access-vs-azure-file-sync) (via een Azure VM of service) dan Azure file sync. Als u van plan bent om Azure File Sync alleen op deze shares te gebruiken, kunt u de groepering van meerdere accounts in één Azure-opslag account prima. U kunt ook overwegen om een app in de cloud te zetten en te verplaatsen, waarna u rechtstreeks toegang krijgt tot een bestands share. U kunt ook beginnen met het gebruik van een service in azure, waardoor er meer IOPS-en doorvoer nummers beschikbaar zouden zijn. 

Als u een lijst met shares hebt gemaakt, moet u elke share toewijzen aan het opslag account waarin deze zich bevindt.

> [!IMPORTANT]
> Zorg ervoor dat u beslist over een Azure-regio en dat elk opslag account en Azure File Sync bron overeenkomen met de regio die u hebt geselecteerd.

### <a name="phase-1-summary"></a>Samen vatting fase 1
Aan het einde van fase 1:
* U hebt een goed overzicht van uw StorSimple-apparaten en-volumes.
* De service voor gegevens transformatie is gereed om toegang te krijgen tot uw StorSimple-volumes in de Cloud, omdat u de versleutelings sleutel voor de service gegevens hebt opgehaald voor elk StorSimple-apparaat.
* U hebt een abonnement voor niet alleen de volumes die moeten worden gemigreerd, maar ook hoe u uw volumes wilt toewijzen aan het juiste aantal Azure-bestands shares en-opslag accounts.

> [!CAUTION]
> Als u back-ups moet migreren van StorSimple-volumes, kunt u **hier stoppen**.
>
> Deze migratie aanpak is afhankelijk van de nieuwe functies voor gegevens transformatie service die momenteel geen back-ups kunnen migreren. Ondersteuning voor back-upmigratie arriveert aan het einde van 2020.
> U kunt momenteel alleen uw Live gegevens migreren. Als u nu begint, kunt u uw back-ups later niet meer uitvoeren.
> Back-ups moeten worden ' afgespeeld ' naar de Azure-bestands shares van oudste naar nieuwste tot dynamische gegevens, met moment opnamen van Azure-bestands shares in.

Als u alleen de Live gegevens wilt migreren en geen vereisten voor back-ups hebt, kunt u door gaan met het volgen van deze hand leiding.

## <a name="phase-2-deploying-azure-storage-and-migration-resources"></a>Fase 2: Azure Storage-en migratie bronnen implementeren

In deze sectie worden de overwegingen beschreven voor het implementeren van de verschillende resource typen die nodig zijn in Azure. Sommige worden uw gegevens na de migratie bewaard en sommige zijn alleen nodig voor de migratie. Begin niet met het implementeren van resources voordat u uw implementatie plan hebt voltooid. Het is moeilijk, soms onmogelijk om bepaalde aspecten van uw Azure-resources te wijzigen nadat ze zijn geïmplementeerd.

### <a name="deploy-storage-accounts"></a>Opslag accounts implementeren
Waarschijnlijk moet u verschillende Azure-opslag accounts implementeren. Elk abonnement bevat een kleiner aantal Azure-bestands shares, conform uw implementatie plan, dat u in de vorige sectie van dit artikel hebt voltooid. U hebt de Azure Portal voor het [implementeren van uw geplande opslag accounts](../common/storage-account-create.md#create-a-storage-account). Overweeg de volgende basis instellingen voor een nieuw opslag account te volgen:

##### <a name="subscription"></a>Abonnement
U kunt hetzelfde abonnement gebruiken dat u hebt gebruikt voor uw StorSimple-implementatie of een andere. De enige beperking is dat uw abonnement zich in dezelfde Azure AD-Tenant moet bevindt als het StorSimple-abonnement. Overweeg het StorSimple-abonnement te verplaatsen naar de juiste Tenant voordat u een migratie start. U kunt alleen het hele abonnement verplaatsen. afzonderlijke StorSimple-resources kunnen niet worden verplaatst naar een andere Tenant of een ander abonnement.

##### <a name="resource-group"></a>Resourcegroep
Resource groepen helpen bij het beheren van de organisatie van resources en beheerders machtigingen. Meer informatie over [resource groepen vindt u in azure](../../azure-resource-manager/management/manage-resource-groups-portal.md#what-is-a-resource-group).

##### <a name="storage-account-name"></a>Naam van het opslagaccount
De naam van uw opslag account wordt onderdeel van een URL en heeft bepaalde teken beperkingen. In uw naam Conventie kunt u overwegen dat namen van opslag accounts uniek moeten zijn voor de hele wereld, alleen kleine letters en cijfers toestaan, tussen 3 en 24 tekens zijn vereist en geen speciale tekens zoals afbreek streepjes of onderstrepingen toestaan. Zie ook: [naamgevings regels voor Azure storage-resources](../../azure-resource-manager/management/resource-name-rules.md#microsoftstorage).

##### <a name="location"></a>Locatie
De locatie of de Azure-regio van een opslag account is zeer belang rijk. Als u Azure File Sync gebruikt, moeten al uw opslag accounts zich in dezelfde regio bevinden als de bron voor de opslag synchronisatie service. De Azure-regio die u kiest, moet sluiten of centraal zijn voor uw lokale servers/gebruikers. Zodra de resource is geïmplementeerd, kunt u de regio niet meer wijzigen.

U kunt een andere regio kiezen waar uw StorSimple-gegevens (opslag account) momenteel zich bevinden.

> [!IMPORTANT]
> Als u een andere regio kiest op basis van uw huidige, StorSimple opslag locatie, [worden er kosten voor uitgaand verkeer toegepast](https://azure.microsoft.com/pricing/details/bandwidth) tijdens de migratie. Gegevens gaan in de regio StorSimple en voeren de nieuwe regio voor het opslag account in. Er worden geen bandbreedte kosten in rekening gebracht als u binnen dezelfde Azure-regio blijft.

##### <a name="performance"></a>Prestaties
U kunt kiezen voor Premium Storage (SSD) voor Azure-bestands shares of standaard opslag. Standaard opslag bevat [verschillende lagen voor een bestands share](storage-how-to-create-file-share.md#changing-the-tier-of-an-azure-file-share). Standaard opslag is de juiste optie voor de meeste klanten die van StorSimple worden gemigreerd.

Nog steeds niet zeker?
* Kies Premium Storage als u de [prestaties van een Premium Azure-bestands share](storage-files-planning.md#understanding-provisioning-for-premium-file-shares)nodig hebt.
* Kies standaard opslag voor de werk belasting van bestands servers voor algemene doel einden, met inbegrip van gegevens over dynamische gegevens en archieven. Kies ook standaard opslag als de enige werk belasting op de share in de Cloud wordt Azure File Sync.

##### <a name="account-kind"></a>Soort account
* Kies voor standaard opslag: *StorageV2 (algemeen gebruik v2)*
* Kies voor Premium-bestands shares: *FileStorage*

##### <a name="replication"></a>Replicatie
Er zijn verschillende replicatie-instellingen beschikbaar. Meer informatie over de verschillende replicatie typen.

Kies alleen een van de volgende twee opties:
* *Lokaal redundante opslag (LRS)*
* *Zone redundant Storage (ZRS)* , die niet beschikbaar is in alle Azure-regio's.

> [!NOTE]
> Alleen LRS-en ZRS-redundantie typen zijn compatibel met de grote, 100TiB capaciteit van Azure-bestands shares.

Wereld wijd redundante opslag (alle variaties) wordt momenteel niet ondersteund. U kunt later overschakelen op het redundantie type en naar GRS wanneer ondersteuning voor de service arriveert in Azure.

##### <a name="enable-100tib-capacity-file-shares"></a>Bestands shares met 100TiB capaciteit inschakelen

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png" alt-text="Een afbeelding met het tabblad Geavanceerd in de Azure Portal voor het maken van een opslag account.":::
    :::column-end:::
    :::column:::
        Onder de sectie *Geavanceerd* van de wizard Nieuw opslag account in de Azure Portal, kunt u ondersteuning voor *grote bestands shares* inschakelen in dit opslag account. Als deze optie niet beschikbaar is, hebt u waarschijnlijk het verkeerde type redundantie geselecteerd. Zorg ervoor dat u alleen LRS of ZRS selecteert om deze optie beschikbaar te stellen.
    :::column-end:::
:::row-end:::

Voor de grote bestands shares met een 100TiB-capaciteit geldt een aantal voor delen:
* De prestaties worden aanzienlijk verhoogd ten opzichte van de kleinere bestands shares met een 5TiB-capaciteit. (bijvoorbeeld: 10 keer de IOPS)
* Uw migratie wordt aanzienlijk sneller voltooid.
* U kunt ervoor zorgen dat een bestands share voldoende capaciteit heeft om alle gegevens die u naar de bestanden wilt migreren, te bevatten.
* Toekomstige groei wordt gedekt.

### <a name="azure-file-shares"></a>Azure-bestands shares
Als uw opslag accounts zijn gemaakt, kunt u naar de sectie *Bestands share* van het opslag account navigeren en het juiste aantal Azure-bestands shares implementeren volgens uw migratie plan vanuit fase 1. U kunt overwegen om te voldoen aan de volgende basis instellingen voor uw nieuwe bestands shares in Azure:

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-share.png" alt-text="Een afbeelding met het tabblad Geavanceerd in de Azure Portal voor het maken van een opslag account.":::
    :::column-end:::
    :::column:::
        </br>**Naam**</br>Kleine letters, cijfers en afbreek streepjes worden ondersteund.</br></br>**Quota**</br>Quotum hier is vergelijkbaar met het SMB-vaste quotum op een Windows-Server. De best practice heeft geen quotum dat hier wordt ingesteld als uw migratie en andere services mislukken wanneer het quotum is bereikt.</br></br>**Laag**</br>Selecteer *geoptimaliseerde trans actie* voor uw nieuwe bestands share. Tijdens de migratie worden veel trans acties uitgevoerd en is het rendabeler om uw laag later te wijzigen in de laag die het beste past bij uw werk belasting.
    :::column-end:::
:::row-end:::

### <a name="storsimple-data-manager"></a>StorSimple-gegevensbeheer
De Azure-resource die u migratie taken bevat, wordt een *' StorSimple Data Manager '* genoemd. Klik op nieuwe resource en zoek ernaar en klik vervolgens op *maken*.

Dit is een tijdelijke resource die wordt gebruikt voor indeling. u kunt de inrichting ongedaan maken nadat de migratie is voltooid. Het moet worden geïmplementeerd in hetzelfde abonnement, dezelfde resource groep en regio als uw StorSimple-opslag account.

### <a name="azure-file-sync"></a>Azure File Sync
Met Azure File Sync (AFS) kunt u on-premises caching van de meest gebruikte bestanden toevoegen. Net als bij de cache mogelijkheden van StorSimple biedt de AFS-functie voor het uitvoeren van Cloud lagen een latentie van lokale toegang in combi natie met verbeterde controle over de beschik bare cache capaciteit op de Windows-Server en multi-site-synchronisatie. Als u een on-premises cache hebt, moet u in uw lokale netwerk een Windows Server-VM voorbereiden (fysieke servers en failover-clusters worden ook ondersteund), met voldoende ' Direct Attached Storage (DAS) '-capaciteit. 

> [!IMPORTANT]
> Azure File Sync nog niet instellen. u kunt Azure File Sync het beste instellen nadat de migratie van de share is voltooid. Het implementeren van AFS mag niet beginnen vóór fase 4 van een migratie.

### <a name="phase-2-summary"></a>Samen vatting fase 2
Aan het einde van fase 2 hebt u uw opslag accounts geïmplementeerd, alle Azure-bestands shares erin en ook beschikt u over een StorSimple Data Manager bron. U zult de laatste in fase 3 gebruiken, wanneer u uw migratie taken daad werkelijk hebt geconfigureerd.

## <a name="phase-3-create-and-run-a-migration-job"></a>Fase 3: een migratie taak maken en uitvoeren
In deze sectie wordt beschreven hoe u een migratie taak instelt en hoe u de mappen op een StorSimple-volume zorgvuldig toewijst die moeten worden gekopieerd naar de Azure-doel bestands share die u selecteert. Om aan de slag te gaan, gaat u naar uw StorSimple Data Manager, zoekt u naar **taak definities** in het menu en selecteert u **+ taak definitie**. Het doel opslag type is de standaard instelling: *Azure-bestands share*.

![Typen migratie taken StorSimple 8000-serie.](media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-job-type.png "Een scherm opname van de taak definities Azure Portal met een nieuw dialoog venster voor taak definitie geopend, waarin wordt gevraagd om het type taak: kopiëren naar een bestands share of een BLOB-container.")

> [!IMPORTANT]
> Voordat u een migratie taak uitvoert, stopt u automatisch geplande back-ups van uw StorSimple-volumes.

:::row:::
    :::column:::
        ![Migratie taak voor de StorSimple 8000-serie.](media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-job.png "Een scherm opname van het nieuwe formulier voor het maken van een taak voor een gegevens transformatie taak.")
    :::column-end:::
    :::column:::
        **Naam van taakdefinitie**</br>Deze naam moet duiden op de set bestanden die u wilt verplaatsen. Het is een goede gewoonte om het een soort gelijke naam te geven als uw Azure-bestands share. </br></br>**Locatie waar de taak wordt uitgevoerd**</br>Wanneer u een regio selecteert, moet u dezelfde regio als het opslag account van uw StorSimple selecteren of, als deze niet beschikbaar is, vervolgens een regio sluiten. </br></br><h3>Bron</h3>**Bron abonnement**</br>Kies het abonnement waarin u de StorSimple-Apparaatbeheer resource opslaat. </br></br>**StorSimple-resource**</br>Kies uw StorSimple Apparaatbeheer uw apparaat is geregistreerd bij. </br></br>**Gegevensversleutelingssleutel van service**</br>Controleer deze [vorige sectie in dit artikel](#storsimple-service-data-encryption-key), voor het geval u de sleutel niet in uw records kunt vinden. </br></br>**Apparaat**</br>Selecteer uw StorSimple-apparaat dat het volume bevat waarnaar u wilt migreren. </br></br>**Volume**</br>Selecteer het bron volume. Later beslist u of u het hele volume of de submappen wilt migreren naar de Azure-doel bestands share. </br></br><h3>Doel</h3>Kies het abonnement, het opslag account en de Azure-bestands share als doel van deze migratie taak.
    :::column-end:::
:::row-end:::

> [!IMPORTANT]
> De meest recente volume back-up wordt gebruikt om de migratie uit te voeren. Zorg ervoor dat er ten minste één volume back-up aanwezig is of dat de taak mislukt. Zorg er ook voor dat de meest recente back-up die u hebt, vrij recent is, zodat u de Live-share zo klein mogelijk kunt blijven gebruiken. Het kan hand matig worden geactiveerd en een ander volume back-up te volt ooien **voordat** u de zojuist gemaakte taak uitvoert.

### <a name="directory-mapping"></a>Adreslijst toewijzing
Dit is optioneel voor uw migratie taak. Als u dit leeg laat, worden **alle** bestanden en mappen op de hoofdmap van uw StorSimple-volume verplaatst naar de hoofdmap van de Azure-doel bestands share. In de meeste gevallen is het opslaan van de inhoud van een volledig volume in een Azure-bestands share niet de beste benadering. Het is vaak beter om de inhoud van een volume te splitsen over meerdere bestands shares in Azure. Als u nog geen abonnement hebt gemaakt, raadpleegt u deze sectie eerst: [uw StorSimple-volume toewijzen aan Azure-bestands shares](#map-your-existing-storsimple-volumes-to-azure-file-shares)

Als onderdeel van uw migratie plan hebt u mogelijk besloten dat de mappen op een StorSimple-volume moeten worden opgesplitst op meerdere Azure-bestands shares. Als dat het geval is, kunt u die splitsing uitvoeren door:
1. Het definiëren van meerdere taken voor het migreren van de mappen op één volume heeft elk dezelfde StorSimple volume bron, maar een andere Azure-bestands share als het doel.
1. Opgeven welke mappen van het StorSimple-volume moeten worden gemigreerd naar de opgegeven bestands share, door gebruik te maken van de sectie *Directory toewijzing* van het formulier voor het maken van een taak en het volgen van de specifieke [toewijzings semantiek](#semantic-elements).

> [!IMPORTANT]
> De paden en toewijzings expressies in dit formulier kunnen niet worden gevalideerd wanneer het formulier wordt ingediend. Als toewijzingen onjuist zijn opgegeven, kan een taak volledig mislukken of een ongewenste resultaat opleveren. In dat geval kunt u de Azure-bestands share doorgaans het beste verwijderen, opnieuw maken en vervolgens de toewijzings instructies herstellen in een nieuwe migratie taak voor de share. Als u een nieuwe taak met een vaste toewijzings instructie uitvoert, kunnen wegge laten mappen worden omgezet en naar de bestaande share worden gebracht. Er zijn echter alleen mappen die zijn wegge laten vanwege het pad naar spel fouten, op deze manier kunnen worden verholpen.

#### <a name="semantic-elements"></a>Semantische elementen
Een toewijzing wordt uitgedrukt van links naar rechts: [\Source pad] \> [\target pad].

|Semantisch teken          | Betekenis  |
|:---------------------------|:---------|
| **\\**                     | indicator op basis niveau        |
| **\>**                     | de toewijzings operator [Bron] en [doel]        |
|**\|** of RETURN (nieuwe regel) | schei ding van twee instructies voor toewijzing van mappen. </br>U kunt dit teken ook weglaten en op ENTER drukken om de volgende toewijzings expressie op een aparte regel op te halen.        |

### <a name="examples"></a>Voorbeelden
Hiermee wordt de inhoud van de map "gebruikers gegevens" verplaatst naar de hoofdmap van de doel bestands share:
``` console
\User data > \\
```
Verplaatst de inhoud van het hele volume naar een nieuw pad op de doel bestands share:
``` console
\ \> \Apps\HR tracker
```
Verplaatst de inhoud van de bronmap naar een nieuw pad op de doel bestands share:
``` console
\HR resumes-Backup \> \Backups\HR\resumes
```
Hiermee worden meerdere bron locaties in een nieuwe mapstructuur gesorteerd:
``` console
\HR\Candidate Tracker\v1.0 > \Apps\Candidate tracker
\HR\Candidates\Resumes > \HR\Candidates\New
\Archive\HR\Old Resumes > \HR\Candidates\Archived
```

### <a name="semantic-rules"></a>Semantische regels
* Geef altijd mappaden op die relatief zijn ten opzichte van het hoofd niveau. 
* Begin elke mappad met een indicator op hoofd niveau \" . 
* Geen stationsletters bevatten. 
* Wanneer u meerdere paden opgeeft, kunnen de bron-of doel paden elkaar niet overlappen:</br>
   Ongeldig overlappend voor beeld van bronpad:</br>
    *\\folder\1 > \\ map*</br>
    *\\map \\ 1 \\ 2 > \\ map2*</br>
   Ongeldig doelpad-voor beeld:</br>
   *\\map > \\*</br>
   *\\map2 > \\*</br>
* De bron mappen die niet bestaan, worden genegeerd. 
* Mapstructuren die niet bestaan op het doel, worden gemaakt. 
* Net als bij Windows: mapnamen zijn niet hoofdletter gevoelig, maar worden behouden.

> [!NOTE]
> de inhoud van de map*\System Volume Information*en het *$Recycle. bin*op uw StorSimple-volume wordt niet gekopieerd door de transformatie taak.

### <a name="phase-3-summary"></a>Samen vatting fase 3
Aan het einde van fase 3 voert u uw gegevens transformatie taken uit van StorSimple-volumes in azure-bestands shares. U kunt zich nu richten op het instellen van een Azure File Sync voor de share (nadat de migratie taken voor een share zijn voltooid) of de toegang direct delen voor uw informatie medewerkers en apps naar de Azure-bestands share.

## <a name="phase-4-accessing-your-azure-file-shares"></a>Fase 4: toegang tot uw Azure-bestands shares

Er zijn twee belang rijke strategieën voor het openen van uw Azure-bestands shares:

* **Azure File Sync:** [Azure file sync implementeren](#deploy-azure-file-sync) op een on-premises Windows-Server. AFS heeft alle voor delen van een lokale cache, net als bij StorSimple.
* **Rechtstreekse toegang tot share:** de [implementatie van direct share-Access](#deploy-direct-share-access). Gebruik deze strategie als uw toegangs scenario voor een bepaalde Azure-bestands share niet van toepassing is op lokale caching of als u niet langer de mogelijkheid hebt om een on-premises Windows Server te hosten. Hier blijven uw gebruikers en apps toegang tot SMB-shares via het SMB-protocol, maar deze shares bevinden zich niet meer op een on-premises server, maar rechtstreeks in de Cloud.

U moet al bepalen welke optie het meest geschikt is voor u, in [fase 1](#phase-1-prepare-for-migration) van deze hand leiding.

De rest van deze sectie richt zich op implementatie-instructies.

### <a name="deploy-azure-file-sync"></a>Azure Files SYNC implementeren

Het is tijd om een deel van Azure File Sync te implementeren:
1. Maak de Azure File Sync Cloud resource.
1. Implementeer de Azure File Sync-agent op uw on-premises server.
1. De server registreren bij de Cloud resource

Maak nog geen synchronisatie groepen. Het instellen van synchronisatie met een Azure-bestands share mag alleen optreden als uw migratie taken naar een Azure-bestands share zijn voltooid. Als u begint met het gebruik van File Sync voordat de migratie is voltooid, zal de migratie onnodig moeilijk worden, omdat u niet eenvoudig kunt zien wanneer het tijd is om een cut te initiëren.

#### <a name="deploy-the-azure-file-sync-cloud-resource"></a>De Azure File Sync Cloud resource implementeren

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

> [!TIP]
> Als u wijzigingen wilt aanbrengen in de Azure-regio waarin uw gegevens zich bevinden wanneer de migratie is voltooid, implementeert u de opslag synchronisatie service in dezelfde regio als de doel opslag accounts voor deze migratie.

#### <a name="deploy-an-on-premises-windows-server"></a>Een on-premises Windows-Server implementeren

* Maak een Windows Server 2019-met een minimale 2012R2-als een virtuele machine of fysieke server. Een failover-cluster van Windows Server wordt ook ondersteund. Gebruik de server niet opnieuw op de StorSimple 8100 of 8600.
* Het inrichten of toevoegen van direct gekoppelde opslag (DAS in vergelijking met NAS), wat niet wordt ondersteund.

Het is best practice om uw nieuwe Windows-Server evenveel of grotere opslag ruimte te geven dan uw StorSimple 8100-of 8600-apparaat lokaal beschikbaar is voor caching. U gebruikt de Windows-Server op dezelfde manier als waarop u het StorSimple-apparaat hebt gebruikt, als dit dezelfde hoeveelheid opslag ruimte heeft als het apparaat, dan moet de cache-ervaring gelijk zijn, als dat niet zo is.
U kunt op uw Windows-Server ook opslag ruimte toevoegen of verwijderen. Zo kunt u de grootte van uw lokale volume en de hoeveelheid lokale opslag die beschikbaar is voor caching, schalen.

#### <a name="prepare-the-windows-server-for-file-sync"></a>De Windows-Server voorbereiden voor bestands synchronisatie

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

#### <a name="configure-azure-file-sync-on-the-windows-server"></a>Azure File Sync configureren op de Windows-Server

Uw geregistreerde on-premises Windows-Server moet gereed zijn en verbonden zijn met internet voor dit proces.

> [!IMPORTANT]
> Uw StorSimple-migratie van bestanden en mappen naar de Azure-bestands share moet zijn voltooid voordat u verder kunt gaan. Zorg ervoor dat er geen wijzigingen meer worden aangebracht aan de bestands share.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> Zorg ervoor dat u Cloud lagen inschakelt. Cloud lagen is de Azure File Sync-functie waarmee de lokale server minder opslag capaciteit kan hebben dan is opgeslagen in de Cloud, maar de volledige naam ruimte beschikbaar is. Lokaal interessante gegevens worden ook lokaal in de cache opgeslagen voor snelle, lokale toegangs prestaties. Een andere reden om Cloud-Tiering in deze stap in te scha kelen, is dat er op dit moment geen bestands inhoud in deze fase kan worden gesynchroniseerd.

### <a name="deploy-direct-share-access"></a>Direct share-toegang implementeren

:::row:::
    :::column:::
        [![Stapsgewijze hand leiding en demo voor het veilig beschikbaar maken van Azure-bestands shares voor informatie medewerkers en apps-Klik om af te spelen.](./media/storage-files-migration-storsimple-8000/azure-files-direct-access-video-placeholder.png)](https://youtu.be/KG0OX0RgytI)
    :::column-end:::
    :::column:::
        Deze video bevat een hand leiding en demo voor het veilig beschikbaar maken van Azure-bestands shares aan informatie medewerkers en apps in vijf eenvoudige stappen.</br>
        De video verwijst naar specifieke documentatie voor sommige onderwerpen:
* [Overzicht van identiteit](storage-files-active-directory-overview.md)
* [Een domein toevoegen aan een opslag account](storage-files-identity-auth-active-directory-enable.md)
* [Overzicht van netwerken voor Azure-bestands shares](storage-files-networking-overview.md)
* [Open bare en privé-eind punten configureren](storage-files-networking-endpoints.md)
* [Een S2S-VPN configureren](storage-files-configure-s2s-vpn.md)
* [Een Windows P2S VPN configureren](storage-files-configure-p2s-vpn-windows.md)
* [Een Linux P2S-VPN configureren](storage-files-configure-p2s-vpn-linux.md)
* [DNS-door sturen configureren](storage-files-networking-dns.md)
* [DFS configureren-N](https://aka.ms/AzureFiles/Namespaces)
   :::column-end:::
:::row-end:::

### <a name="phase-4-summary"></a>Samen vatting fase 4
In deze fase hebt u meerdere DTS-taken ( *Data Transformation Service* ) gemaakt en uitgevoerd in uw *StorSimple Data Manager*. Deze taken hebben uw bestanden en mappen gemigreerd naar Azure-bestands shares. Daarnaast hebt u Azure File Sync geïmplementeerd of uw netwerk-en opslag accounts voor direct share-toegang voor bereid.

## <a name="phase-5-user-cut-over"></a>Fase 5: gebruikers knippen
In deze fase wordt de migratie afronden:
* Plan uw downtime.
* Verwerk met eventuele wijzigingen die uw gebruikers en apps op de StorSimple hebben gemaakt terwijl de gegevens transformatie taken in fase 3 actief zijn. 
* Laat uw gebruikers niet overbeschikken over de nieuwe Windows-Server met Azure File Sync of de Azure-bestands shares via direct share-toegang.

### <a name="plan-your-downtime"></a>Uw downtime plannen
Deze migratie aanpak vereist enige downtime voor uw gebruikers en apps. Het doel is om de uitval tijd tot een minimum te beperken en de volgende overwegingen kunnen helpen:

* Zorg ervoor dat uw StorSimple-volumes beschikbaar zijn tijdens het uitvoeren van uw gegevens transformatie taken.
* Wanneer u klaar bent met het uitvoeren van uw gegevens migratie taak (s) voor een share, is het tijd om gebruikers toegang te verwijderen (ten minste schrijf toegang) van de StorSimple-volumes/-shares. Met een eind-RoboCopy kunt u uw Azure-bestands share opvangen en vervolgens uw gebruikers meeknippen. Waar u RoboCopy uitvoert, is afhankelijk van of u ervoor hebt gekozen om Azure File Sync of direct share-Access te gebruiken. In de komende sectie op RoboCopy wordt beschreven.
* Zodra u de RoboCopy-up hebt voltooid, bent u klaar om de nieuwe locatie beschikbaar te maken voor uw gebruikers. De Azure-bestands share is rechtstreeks of op een SMB-share op een Windows-Server met AFS. Vaak wordt een DFS-N-implementatie gebruikt om snel en efficiënt een knippen uit te voeren. De bestaande share-adressen blijven consistent en verwijzen naar een nieuwe locatie die uw gemigreerde bestanden en mappen bevat.

### <a name="determine-when-your-namespace-has-fully-synced-to-your-server"></a>Bepalen wanneer uw naam ruimte volledig is gesynchroniseerd met uw server

Wanneer u Azure File Sync gebruikt voor een Azure-bestands share, is het belang rijk dat u hebt vastgesteld dat de volledige naam ruimte is gedownload naar de server voordat u een lokale RoboCopy start. De tijd die nodig is om uw naam ruimte te downloaden is afhankelijk van het aantal items in de Azure-bestands share. Er zijn twee methoden om te bepalen of uw naam ruimte volledig is aangekomen op de server:

#### <a name="azure-portal"></a>Azure Portal
U kunt de Azure Portal gebruiken om te zien wanneer uw naam ruimte volledig is aangekomen.
* Meld u aan bij de Azure Portal, navigeer naar de synchronisatie groep en controleer de synchronisatie status van uw synchronisatie groep en server eindpunt. 
* De interessante richting wordt gedownload: als het server eindpunt nieuw is ingericht, wordt de **eerste synchronisatie** weer gegeven waarin staat dat de naam ruimte nog steeds niet beschikbaar is.
Zodra de wijzigingen in een van de *eerste synchronisaties*worden doorgevoerd, wordt de naam ruimte volledig ingevuld op de server en bent u goed om door te gaan met een lokale Robocopy.

#### <a name="windows-server-event-viewer"></a>Windows Server-Logboeken
OE kan ook de Logboeken op uw Windows-Server gebruiken om te bepalen wanneer de naam ruimte volledig is aangekomen.

1. Open de **Logboeken** en navigeer naar **toepassingen en services**.
1. Navigeer en open **Microsoft\FileSync\Agent\Telemetry**.
1. Zoek naar de meest recente **gebeurtenis 9102**, die overeenkomt met een voltooide synchronisatie sessie.
1. Selecteer **Details** en bevestig dat u naar een gebeurtenis zoekt waarbij de waarde voor **SyncDirection** is **gedownload**.
1. Voor het moment waarop uw naam ruimte het downloaden van de server heeft voltooid, is er één gebeurtenis met **scenario**, waarde **FullGhostedSync** en **HResult**  =  **0**.
1. Als u deze gebeurtenis mist, kunt u ook andere 9102- **gebeurtenissen** zoeken met **SyncDirection**  =  **down load** en **scenario**  =  **' RegularSync '**. Als u een van deze gebeurtenissen zoekt, geeft u ook aan dat de naam ruimte is gedownload en dat de synchronisatie is voltooid naar reguliere synchronisatie sessies, op dit moment.

### <a name="a-final-robocopy"></a>Een laatste RoboCopy
Op dit moment zijn er verschillen tussen uw on-premises Windows-Server en het StorSimple 8100-of 8600-apparaat:

1. U moet de wijzigingen die gebruikers/apps op de StorSimple hebben geproduceerd tijdens de migratie door lopen.
1. Voor gevallen waarin u Azure File Sync gebruikt: het StorSimple-apparaat heeft een gevulde cache vs. de Windows-Server heeft alleen een naam ruimte zonder bestands inhoud lokaal opgeslagen. De uiteindelijke RoboCopy kan u helpen uw lokale AFS-cache snel te starten door over te halen van de lokaal in cache opgeslagen bestands inhoud zoveel als beschikbaar is en op de AFS-server past.
1. Sommige bestanden zijn mogelijk achtergelaten door de gegevens transformatie taak vanwege ongeldige tekens. Als dit het geval is, kopieert u deze naar de Azure File Sync ingeschakelde Windows-Server. Later kunt u ze aanpassen zodat ze worden gesynchroniseerd. Als u Azure File Sync niet gebruikt voor een bepaalde share, is het beter om de namen van de bestanden te wijzigen met ongeldige tekens op het StorSimple-volume en vervolgens de RoboCopy rechtstreeks op de Azure-bestands share uit te voeren. 

> [!WARNING]
> U moet de RoboCopy niet starten voordat de server de naam ruimte heeft voor een Azure-bestands share die volledig is gedownload.
> Uitchecken: "[bepalen wanneer uw naam ruimte volledig is gedownload naar uw server](#determine-when-your-namespace-has-fully-synced-to-your-server)"

 U wilt alleen bestanden kopiëren die zijn gewijzigd nadat de migratie taak voor het laatst werd uitgevoerd en bestanden die nog niet via bedoelde taken zijn verplaatst. Nadat de migratie is voltooid, kunt u de problemen oplossen waarom ze later niet op de server zijn verplaatst. Zie [Azure file sync Troubleshooting](storage-sync-files-troubleshoot.md#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)(Engelstalig).

RoboCopy heeft verschillende para meters. De volgende opdracht wordt een voltooid en een lijst met redenen voor het kiezen van deze para meters:

```console
Robocopy /MT:16 /UNILOG:<file name> /TEE /B /MIR /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

Achtergrondbitmap

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      Hiermee kan RoboCopy meerdere threads uitvoeren. De standaard waarde is 8, maximum is 128.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG:<file name>
   :::column-end:::
   :::column span="1":::
      Hiermee wordt de status van de uitvoer naar een logboek bestand opgeslagen als UNICODE (het bestaande logboek wordt overschreven).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /TEE
   :::column-end:::
   :::column span="1":::
      Uitvoer naar console venster. Wordt gebruikt in combi natie met uitvoer naar een logboek bestand.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /B
   :::column-end:::
   :::column span="1":::
      Voert RoboCopy uit in dezelfde modus als een back-uptoepassing zou gebruiken. Hiermee kan RoboCopy bestanden verplaatsen waarnaar de huidige gebruiker geen machtigingen heeft.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /MIR
   :::column-end:::
   :::column span="1":::
      Hiermee kan voor RoboCopy alleen verschillen worden overwogen tussen de bron (StorSimple-apparaat) en het doel (Windows Server-map).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY: copyflag [s]
   :::column-end:::
   :::column span="1":::
      betrouw baarheid van het bestand kopiëren (standaard is/COPY: DAT), kopieer vlaggen: D = gegevens, A = kenmerken, T = tijds tempels, S = Security = NTFS Acl's, O = eigenaargegevens, U = controle-informatie
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      / COPYALL
   :::column-end:::
   :::column span="1":::
      ALLE bestands gegevens kopiëren (gelijk aan/COPY: DATSOU)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY: copyflag [s]
   :::column-end:::
   :::column span="1":::
      betrouw baarheid voor het kopiëren van mappen (standaard is/DCOPY: DA), kopieer vlaggen: D = gegevens, A = kenmerken, T = tijds tempels
   :::column-end:::
:::row-end:::

Wanneer u bron-en doel locaties van de RoboCopy-opdracht configureert, moet u de structuur van de bron en het doel controleren om ervoor te zorgen dat ze overeenkomen. Als u de functie voor adreslijst toewijzing van de migratie taak hebt gebruikt, kan de structuur van de hoofdmap afwijken van de structuur van uw StorSimple-volume. Als dat het geval is, hebt u mogelijk meerdere RoboCopy-taken nodig, één voor elke submap. Omdat deze RoboCopy-opdracht gebruikmaakt van/MIR, worden er geen bestanden verplaatst die hetzelfde zijn (gelaagde bestanden voor instantie), maar als u de bron-en doelpad onjuist haalt, worden in/MIR ook de mapstructuren op uw Windows Server/Azure-bestands share gewist die niet aanwezig zijn op het bron pad van de StorSimple. Daarom moeten ze exact overeenkomen voor de RoboCopy-taak om het beoogde doel te bereiken van het bijwerken van uw gemigreerde inhoud met de laatste wijzigingen die zijn aangebracht tijdens de migratie. 

Raadpleeg het RoboCopy-logboek bestand om te zien of er bestanden achterblijven. Als er problemen zijn, corrigeert u deze en voert u de RoboCopy-opdracht opnieuw uit. Zorg ervoor dat u geen StorSimple-resources vrijmaakt voordat u uitstaande problemen voor bestanden of mappen die u bezorgt, verhelpt.

Als u Azure File Sync niet gebruikt om de specifieke Azure-bestands share in de cache op te slaan, maar voor direct delen hebt gekozen:
1. [Koppel uw Azure-bestands share](storage-how-to-use-files-windows.md#mount-the-azure-file-share) als een netwerk station aan een lokale Windows-computer.
1. Voer de RoboCopy uit tussen uw StorSimple en de gekoppelde Azure-bestands share. Als bestanden niet worden gekopieerd, corrigeert u hun namen aan de StorSimple-zijde om ongeldige tekens te verwijderen en probeert u RoboCopy opnieuw uit te voeren. De eerder vermelde RoboCopy-opdracht kan meerdere keren worden uitgevoerd zonder dat de StorSimple worden teruggehaald.

### <a name="user-cut-over"></a>Gebruiker knippen

Als u Azure File Sync gebruikt, moet u waarschijnlijk de SMB-shares op die Windows Server met AFS-functionaliteit maken die overeenkomen met de shares van de StorSimple-volumes. U kunt deze stap vooraan laden en eerder geen tijd kwijt raken, maar u moet er wel voor zorgen dat niemand toegang heeft tot wijzigingen in de Windows-Server.

Als u een DFS-N-implementatie hebt, kunt u de DFN-Namespaces naar de nieuwe locatie van de servermap wijzen. Als u geen DFS-N-implementatie hebt en u uw 8100/8600-apparaat lokaal hebt voor een Windows-Server, kunt u die server uit het domein halen. Vervolgens voegt u een domein toe aan uw nieuwe Azure File Sync, Windows Server ingeschakeld. Geef tijdens dat proces de server dezelfde server naam en share namen als de oude server, zodat uw gebruikers, groeps beleid en scripts transparant blijven.

[Meer informatie over DFS-N](https://aka.ms/AzureFiles/Namespaces)

## <a name="deprovision"></a>Inrichting
Wanneer u de inrichting van een bron ongedaan maakt, verliest u de toegang tot de configuratie van die bron en de bijbehorende gegevens. Het opheffen van de inrichting kan niet ongedaan worden gemaakt, dus pas pas door nadat u hebt bevestigd dat de migratie is voltooid en er geen afhankelijkheden zijn voor de StorSimple bestanden, mappen of volume back-ups die u gaat opheffen.

Voordat u begint, is het een best practice om uw nieuwe Azure File Sync implementatie in productie te observeren, voor een beetje. Hiermee kunt u eventuele problemen oplossen die kunnen optreden.
Nadat u uw Azure File Sync-implementatie ten minste enkele dagen hebt gezien, kunt u de inrichting van resources in deze volg orde ongedaan maken:

1. De inrichting van uw StorSimple Data Manager-bron via de Azure Portal ongedaan maken.
   Al uw DTS-taken worden hiermee verwijderd. U kunt de Kopieer logboeken niet eenvoudig ophalen. Als ze belang rijk zijn voor uw records, haalt u deze op voordat u de inrichting ongedaan maakt.
1. Zorg ervoor dat uw fysieke StorSimple-apparaten zijn gemigreerd en hef de registratie ervan op.
   Als u niet volledig zeker weet dat ze zijn gemigreerd, moet u niet door gaan. Als u de inrichting van deze resources ongedaan maakt terwijl ze nog steeds nodig zijn, kunt u de gegevens of de configuratie ervan niet herstellen.
1. Als er nog geen geregistreerde apparaten meer zijn in een StorSimple-Apparaatbeheer, kunt u door gaan met het verwijderen van die Apparaatbeheer resource zelf.
1. Het is nu tijd om het StorSimple-opslag account te verwijderen in Azure. Het opnieuw, stoppen en bevestigen van de migratie is voltooid en niets en niemand is afhankelijk van deze gegevens voordat u verdergaat.
1. Ontkoppel het fysieke StorSimple-apparaat uit uw Data Center.
1. Als u de eigenaar van het StorSimple-apparaat bent, kunt u het recyclen. 
   Als uw apparaat wordt geleasd, wordt de lessor geïnformeerd en wordt het apparaat zo nodig geretourneerd.

De migratie is voltooid.

> [!NOTE]
> Hebt u nog vragen of hebt u problemen ondervonden?</br>
> We helpen u om: AzureFilesMigration@microsoft . com


## <a name="next-steps"></a>Volgende stappen

* Krijg meer vertrouwd met [Azure File Sync: aka.MS/AFS](https://aka.ms/AFS).
* Meer informatie over de flexibiliteit van beleids regels voor [Cloud lagen](storage-sync-cloud-tiering.md) .
* [Schakel Azure backup](../../backup/backup-afs.md#configure-backup-from-the-file-share-pane) in voor uw Azure-bestands shares om moment opnamen te plannen en schema's voor het bewaren van back-ups te definiëren.
* Als u in de Azure Portal ziet dat sommige bestanden permanent niet worden gesynchroniseerd, raadpleegt u de [hand leiding](storage-sync-files-troubleshoot.md) voor het oplossen van problemen om deze problemen op te lossen.
