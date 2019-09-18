---
title: Maxi maal beschik bare Azure Storage toepassingen bouwen op zone-redundante opslag (ZRS) | Microsoft Docs
description: Zone-redundante opslag (ZRS) biedt een eenvoudige manier om Maxi maal beschik bare toepassingen te bouwen. ZRS beschermt tegen hardwarestoringen in het Data Center en aan bepaalde regionale rampen.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/28/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: f7639eb2807654aab38a4e849c2e58d77f15bc31
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71036254"
---
# <a name="zone-redundant-storage-zrs-for-building-highly-available-azure-storage-applications"></a>Zone-redundante opslag (ZRS) voor het bouwen van Maxi maal beschik bare Azure Storage toepassingen

[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-zrs.md)]

## <a name="support-coverage-and-regional-availability"></a>Dekking en regionale Beschik baarheid

ZRS ondersteunt momenteel standaard typen voor algemeen gebruik v2 en FileStorage-opslag accounts. Zie [Overzicht van Azure-opslagaccounts](storage-account-overview.md) voor meer informatie over de typen opslagaccounts.

ZRS is beschikbaar voor blok-blobs, niet-schijf pagina-blobs, bestanden, tabellen en wacht rijen.

Voor algemeen gebruik v2-accounts is ZRS algemeen beschikbaar in de volgende regio's:

- Azië - zuidoost
- Europa - west
- Europa - noord
- Frankrijk - centraal
- Japan - oost
- Verenigd Koninkrijk Zuid
- US - centraal
- US - oost
- US - oost 2
- US - west 2

Voor FileStorage-accounts is ZRS algemeen beschikbaar in de volgende regio's:

- Europa - west

Micro soft blijft ZRS inschakelen in extra Azure-regio's. Controleer regel matig de pagina met [updates voor Azure-Services](https://azure.microsoft.com/updates/) voor informatie over nieuwe regio's.

**Bekende beperkingen**

- De Archive-laag wordt momenteel niet ondersteund voor ZRS-accounts. Zie [Azure Blob-opslag: dynamische, koele en archief toegangs lagen](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) voor meer informatie.
- Managed disks bieden geen ondersteuning voor ZRS. U kunt moment opnamen en installatie kopieën voor Standard-SSD-Managed Disks op Standard-HDD opslag opslaan en [kiezen tussen opties voor LRS en ZRS](https://azure.microsoft.com/pricing/details/managed-disks/).

## <a name="what-happens-when-a-zone-becomes-unavailable"></a>Wat gebeurt er wanneer een zone niet meer beschikbaar is?

Uw gegevens zijn nog steeds toegankelijk voor zowel lees-als schrijf bewerkingen, zelfs als een zone niet meer beschikbaar is. Micro soft raadt u aan om de procedures voor het afhandelen van tijdelijke fouten voort te zetten. Deze procedures omvatten het implementeren van beleid voor opnieuw proberen met exponentiële back-ups.

Wanneer een zone niet beschikbaar is, onderneemt Azure netwerk updates, zoals DNS-distributie. Deze updates kunnen van invloed zijn op uw toepassing als u toegang hebt tot uw gegevens voordat de updates zijn voltooid.

ZRS kan uw gegevens mogelijk niet beschermen tegen een regionale ramp waarbij meerdere zones permanent worden beïnvloed. In plaats daarvan biedt ZRS tolerantie voor uw gegevens als deze tijdelijk niet beschikbaar is. Voor beveiliging tegen regionale rampen raadt micro soft aan gebruik te maken van geo-redundante opslag (GRS). Zie [geo-redundante opslag (GRS) voor meer informatie over GRS: Kruis regionale replicatie voor Azure Storage](storage-redundancy-grs.md).

## <a name="converting-to-zrs-replication"></a>Converteren naar ZRS-replicatie

Migreren naar of van LRS, GRS en RA-GRS is eenvoudig. Gebruik de Azure Portal of de opslag Resource provider-API om het redundantie type van uw account te wijzigen. De gegevens worden vervolgens dienovereenkomstig door Azure gerepliceerd. 

Voor het migreren van gegevens naar ZRS is een andere strategie vereist. ZRS-migratie omvat de fysieke verplaatsing van gegevens van één opslag stempel naar meerdere stem pels binnen een regio.

Er zijn twee primaire opties voor migratie naar ZRS: 

- Gegevens hand matig kopiëren of verplaatsen naar een nieuw ZRS-account vanuit een bestaand account.
- Een Live migratie aanvragen.

> [!IMPORTANT]
> Livemigratie wordt momenteel niet ondersteund voor Premium-bestands shares. Op dit moment worden alleen hand matig gekopieerde of verplaatsen van gegevens ondersteund.

Als u de migratie op een bepaalde datum wilt volt ooien, kunt u een hand matige migratie uitvoeren. Een hand matige migratie biedt meer flexibiliteit dan een Livemigratie. Met een hand matige migratie hebt u de controle over de timing.

Als u een hand matige migratie wilt uitvoeren, hebt u de volgende opties:
- Gebruik bestaande hulp middelen, zoals AzCopy, een van de Azure Storage-client bibliotheken of betrouw bare hulpprogram ma's van derden.
- Als u bekend bent met Hadoop of HDInsight, koppelt u het bron-en doel account (ZRS) aan uw cluster. Parallelliseren vervolgens het proces voor het kopiëren van gegevens met een hulp programma zoals DistCp.
- Bouw uw eigen hulp programma met behulp van een van de Azure Storage-client bibliotheken.

Een hand matige migratie kan leiden tot uitval tijd van toepassingen. Als voor uw toepassing hoge Beschik baarheid is vereist, biedt micro soft ook een optie voor Livemigratie. Een Livemigratie is een interne migratie zonder uitval tijd. 

Tijdens een Livemigratie kunt u uw opslag account gebruiken terwijl uw gegevens worden gemigreerd tussen de bron-en doel opslag tempels. Tijdens het migratie proces hebt u hetzelfde niveau van de SLA-en beschik baarheid op de gebruikelijke manier.

Houd u aan de volgende beperkingen voor Livemigratie:

- Microsoft verwerkt uw aanvraag voor livemigratie onmiddellijk, maar er is geen garantie wanneer een livemigratie wordt voltooid. Als u wilt dat uw gegevens op een bepaalde datum worden gemigreerd naar ZRS, raadt micro soft u aan om in plaats daarvan een hand matige migratie uit te voeren. In het algemeen geldt dat hoe meer gegevens u voor uw account hebt, hoe langer het duurt om die gegevens te migreren. 
- Livemigratie wordt alleen ondersteund voor opslag accounts die gebruikmaken van de replicatie van LRS of GRS. Als uw account gebruikmaakt van RA-GRS, moet u eerst het replicatie type van uw account wijzigen in LRS of GRS voordat u doorgaat. Deze intermediair-stap verwijdert het secundaire alleen-lezen eindpunt dat door RA-GRS vóór de migratie wordt gegeven.
- Uw account moet gegevens bevatten.
- U kunt alleen gegevens binnen dezelfde regio migreren. Als u uw gegevens wilt migreren naar een ZRS-account dat zich in een andere regio dan het bron account bevindt, moet u een hand matige migratie uitvoeren.
- Alleen de standaard opslag typen bieden ondersteuning voor Livemigratie. Premium Storage-accounts moeten hand matig worden gemigreerd.
- Livemigratie van ZRS naar LRS, GRS of RA-GRS wordt niet ondersteund. U moet de gegevens hand matig verplaatsen naar een nieuw of een bestaand opslag account.
- Managed disks zijn alleen beschikbaar voor LRS en kunnen niet worden gemigreerd naar ZRS. U kunt moment opnamen en installatie kopieën voor Standard-SSD-Managed Disks op Standard-HDD opslag opslaan en [kiezen tussen opties voor LRS en ZRS](https://azure.microsoft.com/pricing/details/managed-disks/). Zie [Introduction to Azure Managed disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#integration-with-availability-sets)(Engelstalig) voor integratie met beschikbaarheids sets.
- LRS-of GRS-accounts met Archief gegevens kunnen niet worden gemigreerd naar ZRS.

U kunt Live migratie aanvragen via de [ondersteunings portal van Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). Selecteer in de portal het opslag account dat u wilt converteren naar ZRS.
1. **Nieuwe ondersteunings aanvraag** selecteren
2. Voltooi de basis **beginselen** op basis van uw account gegevens. Selecteer in de sectie **service** **opslag account beheer** en de resource die u wilt converteren naar ZRS. 
3. Selecteer **Volgende**. 
4. Geef de volgende waarden op voor het **probleem** gedeelte: 
    - **Ernst**: De standaard waarde ongewijzigd laten.
    - **Probleem type**: Selecteer **gegevens migratie**.
    - **Categorie**: Selecteer **migreren naar ZRS**.
    - **Title**: Typ een beschrijvende titel, bijvoorbeeld ZRS- **account migratie**.
    - **Details**: Typ meer details in het vak **Details** . Ik wil bijvoorbeeld migreren naar ZRS vanuit [LRS, GRS] in de \_ \_ regio. 
5. Selecteer **Volgende**.
6. Controleer of de contact gegevens juist zijn op de Blade **contact gegevens** .
7. Selecteer **Maken**.

Een ondersteunings medewerker neemt contact met u op en geeft u hulp die u nodig hebt.

## <a name="live-migration-to-zrs-faq"></a>Veelgestelde vragen over Livemigratie naar ZRS

**Moet ik een uitval tijd plannen tijdens de migratie?**

Er wordt geen downtime veroorzaakt door de migratie. Tijdens een Livemigratie kunt u uw opslag account blijven gebruiken terwijl uw gegevens worden gemigreerd tussen de bron-en doel opslag tempels. Tijdens het migratie proces hebt u hetzelfde niveau van de SLA-en beschik baarheid op de gebruikelijke manier.

**Is er gegevens verlies gekoppeld aan de migratie?**

Er is geen gegevens verlies gekoppeld aan de migratie. Tijdens het migratie proces hebt u hetzelfde niveau van de SLA-en beschik baarheid op de gebruikelijke manier.

**Zijn er updates vereist voor de toepassing (en) wanneer de migratie is voltooid?**

Zodra de migratie is voltooid, wordt het replicatie type van de account (s) gewijzigd in "zone-redundante opslag (ZRS)". Service-eind punten, toegangs sleutels, SA'S en andere account configuratie opties blijven ongewijzigd en intact.

**Kan ik een Live migratie van mijn General-Purpose v1-account (s) aanvragen bij ZRS?**

ZRS ondersteunt alleen algemeen v2-accounts, dus voordat u een aanvraag voor een Livemigratie verzendt naar ZRS, moet u uw account (s) upgraden naar de versie van het algemene doel v2. Zie [overzicht van het Azure-opslag account](https://docs.microsoft.com/azure/storage/common/storage-account-overview) en voer [een upgrade uit naar een v2-opslag account voor algemeen gebruik](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) voor meer informatie.

**Kan ik een Livemigratie aanvragen van mijn account (GRS) met geo-redundante opslag met lees toegang (en) naar ZRS?**

Voordat u een aanvraag voor een Livemigratie naar ZRS, moet u ervoor zorgen dat uw toepassing (en) of werk belasting (s) niet langer toegang hebben tot het secundaire alleen-lezen-eind punt en het replicatie type van uw opslag account (s) wijzigen in geo-redundante opslag (GRS). Zie [replicatie strategie wijzigen](https://docs.microsoft.com/azure/storage/common/storage-redundancy#changing-replication-strategy) voor meer informatie.

**Kan ik een Live migratie van mijn opslag account (s) aanvragen naar een andere regio?**

Als u uw gegevens wilt migreren naar een ZRS-account dat zich in een andere regio dan de regio van het bron account bevindt, moet u een hand matige migratie uitvoeren.

## <a name="zrs-classic-a-legacy-option-for-block-blobs-redundancy"></a>Klassieke ZRS: Een verouderde optie voor redundantie van blok-blobs
> [!NOTE]
> Micro soft zal ZRS Classic-accounts op 31 maart 2021 terugboeken en migreren. Meer informatie vindt u voor ZRS Classic-klanten vóór afschaffing. 
>
> Zodra ZRS [algemeen beschikbaar is](#support-coverage-and-regional-availability) in een regio, kunnen klanten geen ZRS klassieke accounts maken vanuit de portal in die regio. Het gebruik van micro soft power shell en Azure CLI voor het maken van ZRS klassieke accounts is een optie totdat ZRS Classic is afgeschaft.

ZRS Classic repliceert asynchroon gegevens tussen data centers binnen een tot twee regio's. Gerepliceerde gegevens zijn mogelijk niet beschikbaar tenzij micro soft failover naar de secundaire initieert. Een klassiek ZRS-account kan niet worden geconverteerd naar of van LRS, GRS of RA-GRS. Klassieke ZRS-accounts bieden ook geen ondersteuning voor metrische gegevens of logboek registratie.

ZRS Classic is alleen beschikbaar voor **blok-blobs** in opslag accounts voor algemeen gebruik (GPv1). Zie [Overzicht van Azure-opslagaccount](storage-account-overview.md) voor meer informatie over opslagaccounts.

Gebruik een van de volgende hulpprogram ma's om de ZRS-account gegevens hand matig te migreren naar of van een LRS-, ZRS klassieke, GRS-of RA-GRS-account: AzCopy, Azure Storage Explorer, Azure PowerShell of Azure CLI. U kunt ook uw eigen migratie oplossing bouwen met een van de Azure Storage-client bibliotheken.

U kunt ook uw klassieke ZRS-account (s) upgraden naar ZRS in de portal of gebruikmaken van Azure PowerShell of Azure CLI in de regio's waar ZRS beschikbaar is. Als u een upgrade wilt uitvoeren naar ZRS in de Azure Portal, gaat u naar de sectie **configuratie** van het account en kiest u **upgrade uitvoeren**:

![Upgrade ZRS Classic naar ZRS in de portal](media/storage-redundancy-zrs/portal-zrs-classic-upgrade.png)

Als u een upgrade wilt uitvoeren naar ZRS met Power shell roept u de volgende opdracht aan:
```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> -AccountName <storage_account> -UpgradeToStorageV2
```

Als u wilt upgraden naar ZRS met behulp van CLI, roept u de volgende opdracht aan:
```cli
az storage account update -g <resource_group> -n <storage_account> --set kind=StorageV2
```

## <a name="see-also"></a>Zie ook
- [Azure Storage-replicatie](storage-redundancy.md)
- [Lokaal redundante opslag (LRS): Lage kosten voor gegevens redundantie voor Azure Storage](storage-redundancy-lrs.md)
- [Geografisch redundante opslag (GRS): Kruis regionale replicatie voor Azure Storage](storage-redundancy-grs.md)
