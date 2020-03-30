---
title: Migreren naar Azure-bestandsshares
description: Meer informatie over migraties naar Azure-bestandsshares en vind uw migratiegids.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 3/18/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 903ce52120fce7c23c6a3754498b81fc6fc2430f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247313"
---
# <a name="migrate-to-azure-file-shares"></a>Migreren naar Azure-bestandsshares

Dit artikel behandelt de basisaspecten van een migratie naar Azure-bestandsshares.

Naast de basisprincipes voor migratie bevat dit artikel een lijst met bestaande, geïndividualiseerde migratiegidsen. Met deze migratiehandleidingen u uw bestanden verplaatsen naar Azure-bestandsshares en worden ze geordend op de plaats waar uw gegevens zich vandaag bevinden en naar welk implementatiemodel (alleen in de cloud of hybride) u van plan bent over te stappen.

## <a name="migration-basics"></a>Basisbeginselen van migratie

Er zijn meerdere verschillende typen cloudopslag beschikbaar in Azure. Een fundamenteel aspect van een migratie van bestanden naar Azure is om te bepalen welke Azure-opslagoptie geschikt is voor uw gegevens.

Azure-bestandsshares zijn ideaal voor bestandsgegevens voor algemene doeleinden. Echt alles wat je gebruikt een on-premises SMB of NFS aandeel voor. Met [Azure File Sync](storage-sync-files-planning.md)u optioneel de inhoud van verschillende Azure-bestandsshares op verschillende on-premises Windows Servers opslaan.

Als u een toepassing hebt die momenteel op een on-premises server wordt uitgevoerd, kan het opslaan van bestanden in Azure-bestandsshares geschikt zijn voor u, afhankelijk van de toepassing. U de toepassing optillen om in Azure uit te voeren en Azure-bestandsshares gebruiken als gedeelde opslag. U [Azure Disks](../../virtual-machines/windows/managed-disks-overview.md) ook voor dit scenario overwegen. Voor toepassingen in de cloud die niet afhankelijk zijn van de smb- of machinelokale toegang tot hun gegevens of gedeelde toegang, is objectopslag, zoals [Azure-blobs,](../blobs/storage-blobs-overview.md)vaak de beste keuze.

De sleutel in elke migratie is het vastleggen van alle toepasselijke bestandsgetrouwheid bij het migreren van uw bestanden van hun huidige opslaglocatie naar Azure. Een hulp bij het kiezen van de juiste Azure-opslag is ook het aspect van hoeveel getrouwheid wordt ondersteund door de Azure-opslagoptie en vereist is door uw scenario. Bestandsgegevens voor algemeen gebruik zijn traditioneel afhankelijk van bestandsmetagegevens. Toepassingsgegevens mogelijk niet. Er zijn twee basiscomponenten aan een bestand:

- **Gegevensstroom:** de gegevensstroom van een bestand slaat de bestandsinhoud op.
- **Bestandsmetameta:De**bestandsmetagegevens hebben verschillende subcomponenten:
   * Bestandskenmerken: alleen-lezen, bijvoorbeeld.
   * Bestandsmachtigingen: aangeduid als *NTFS-machtigingen* of *acl.n.m.v. bestands- en mapacl.File*permissions: Referred to as NTFS permissions or file and folder ACL's .
   * Tijdstempels: Met name de *create-* en *laatste gewijzigde* tijdstempels.
   * Alternatieve gegevensstroom: een ruimte om grotere hoeveelheden niet-standaardeigenschappen op te slaan.

Bestandstrouw, in een migratie, kan daarom worden gedefinieerd als de mogelijkheid om alle toepasselijke bestandsinformatie op de bron op te slaan, de mogelijkheid om deze over te dragen met het migratieprogramma en de mogelijkheid om ze op te slaan op de doelopslag van de migratie.

Om ervoor te zorgen dat uw migratie zo soepel mogelijk verloopt, identificeert u [de beste kopieertool voor uw behoeften](#migration-toolbox) en matcht u een opslagdoel met uw bron.

Rekening houdend met de vorige informatie wordt duidelijk wat de doelopslag voor bestanden voor algemene doeleinden in Azure is: [Azure-bestandsshares](storage-files-introduction.md). In vergelijking met objectopslag in Azure-blobs kunnen bestandsmetagegevens native worden opgeslagen in bestanden in een Azure-bestandsshare.

Azure-bestandsshares behouden ook de bestands- en maphiërarchie. En verder:
* NTFS-machtigingen kunnen worden opgeslagen in bestanden en mappen terwijl ze on-premises zijn.
* AD-gebruikers (of Azure AD DS-gebruikers) hebben native toegang tot een Azure-bestandsshare. 
    Ze gebruiken hun huidige identiteit en krijgen toegang op basis van machtigingen voor delen en acl.acl. Een gedrag dat niet anders is dan wanneer gebruikers verbinding maken met een on-premises bestandsshare.
*  De alternatieve gegevensstroom is het primaire aspect van bestandstrouw dat momenteel niet kan worden opgeslagen in een bestand in een Azure-bestandsshare.
   Het blijft on-premises behouden wanneer Azure File Sync betrokken is.

* [Meer informatie over AD-verificatie voor Azure-bestandsshares](storage-files-identity-auth-active-directory-enable.md)
* [Meer informatie over Aad DS-verificatie (Azure Active Directory Domain Services) voor Azure-bestandsshares](storage-files-identity-auth-active-directory-domain-service-enable.md)

## <a name="migration-guides"></a>Migratiehandleidingen

In de volgende tabel vindt u gedetailleerde migratiegidsen.

Navigeer er door:
1. Zoek de rij voor het bronsysteem waarop uw bestanden momenteel zijn opgeslagen.
2. Bepaal of u een hybride implementatie target waarbij u Azure File Sync gebruikt om de inhoud van een of meer Azure-bestandsshares on-premises in de cache te plaatsen of dat u Azure-bestandsshares rechtstreeks in de cloud wilt gebruiken. Selecteer de doelkolom die uw beslissing weerspiegelt.
3. Binnen het snijpunt van bron en doel geeft een tabelcel beschikbare migratiescenario's weer. Selecteer een van hen om rechtstreeks een koppeling te maken naar de gedetailleerde migratiegids.

Een scenario zonder koppeling heeft nog geen gepubliceerde migratiegids. Controleer deze tabel af en toe voor updates. Nieuwe gidsen zullen worden gepubliceerd indien beschikbaar.

| Bron | Doel: </br>Hybride implementatie | Doel: </br>Implementatie in de cloud alleen |
|:---|:--|:--|
| | Gereedschapscombinatie:| Gereedschapscombinatie: |
| Windows Server 2012 R2 en nieuwer | <ul><li>[Azure File Sync](storage-sync-files-deployment-guide.md)</li><li>[Azure File Sync + DataBox](storage-sync-offline-data-transfer.md)</li><li>Opslagmigratieservice + Synchronisatie van Azure-bestanden</li></ul> | <ul><li>Azure File Sync</li><li>Azure File Sync + DataBox</li><li>Opslagmigratieservice + Synchronisatie van Azure-bestanden</li><li>Robocopy</li></ul> |
| Windows Server 2012 en ouder | <ul><li>Azure File Sync + DataBox</li><li>Opslagmigratieservice + Synchronisatie van Azure-bestanden</li></ul> | <ul><li>Opslagmigratieservice + Synchronisatie van Azure-bestanden</li><li>Robocopy</li></ul> |
| Netwerkopslag (NAS) | <ul><li>[Azure File Sync + RoboCopy](storage-files-migration-nas-hybrid.md)</li></ul> | <ul><li>Robocopy</li></ul> |
| Linux / Samba | <ul><li>[RoboCopy + Azure File Sync](storage-files-migration-linux-hybrid.md)</li></ul> | <ul><li>Robocopy</li></ul> |
| StorSimple 8100 / 8600 | <ul><li>[Azure File Sync + 8020 Virtueel toestel](storage-files-migration-storsimple-8000.md)</li></ul> | |
| StorSimple 1200 | <ul><li>[Azure File Sync](storage-files-migration-storsimple-1200.md)</li></ul> | |
| | | |

## <a name="migration-toolbox"></a>Migratiegereedschapsset

### <a name="file-copy-tools"></a>Hulpmiddelen voor bestandskopie

Er zijn verschillende hulpprogramma's voor bestandskopie van Microsoft en niet-Microsoft beschikbaar. Als u het juiste hulpmiddel voor bestandskopie voor uw migratiescenario wilt selecteren, moet u rekening houden met drie fundamentele vragen:

* Ondersteunt de kopieertool de bron en de doellocatie voor een bepaalde bestandskopie? 
    * Ondersteunt het uw netwerkpad en/of beschikbare protocollen (bijvoorbeeld REST/SMB/NFS) van en naar de bron- en doelopslaglocaties?
* Behoudt het kopieergereedschap de benodigde bestandsgetrouwheid die wordt ondersteund door de bron-/doellocatie? In sommige gevallen ondersteunt uw doelopslag niet dezelfde getrouwheid als uw bron. U hebt al de beslissing genomen dat de doelopslag voldoende is voor uw behoeften, vandaar dat de kopieertool alleen hoeft te voldoen aan de mogelijkheden voor bestandsgetrouwheid van de doelen.
* Heeft de kopieertool functies die het passen in mijn migratiestrategie? 
    * Overweeg bijvoorbeeld of er opties zijn waarmee u uw downtime minimaliseren. Een goede vraag om te stellen is: Kan ik dit exemplaar meerdere keren op dezelfde, door gebruikers actief toegang tot de locatie? Als dat zo is, u de hoeveelheid downtime aanzienlijk verminderen. Vergelijk dat met een situatie waarin u de kopie alleen starten wanneer de bron niet meer verandert, om een volledige kopie te garanderen.

In de volgende tabel worden Microsoft-hulpprogramma's en hun huidige geschiktheid voor Azure-bestandsshares geklasseerd:

| Aanbevolen | Hulpprogramma | Ondersteunt Azure-bestandsshares | Behoudt bestandsgetrouwheid |
| :-: | :-- | :---- | :---- |
|![Ja, aanbevolen.](media/storage-files-migration-overview/circle-green-checkmark.png)| Robocopy | Ondersteund. Azure-bestandsshares kunnen worden gemonteerd als netwerkstations. | Volledige trouw* |
|![Ja, aanbevolen.](media/storage-files-migration-overview/circle-green-checkmark.png)| Azure File Sync | Native geïntegreerd in Azure-bestandsshares. | Volledige trouw* |
|![Ja, aanbevolen.](media/storage-files-migration-overview/circle-green-checkmark.png)| Opslagmigratieservice (SMS) | Indirect ondersteund. Azure-bestandsshares kunnen worden gemonteerd als netwerkstations op een sms-doelserver. | Volledige trouw* |
|![Niet volledig aanbevolen.](media/storage-files-migration-overview/triangle-yellow-exclamation.png)| Azure DataBox | Ondersteund. | Kopieert geen metagegevens. [Kan worden gebruikt in combinatie met Azure File Sync](storage-sync-offline-data-transfer.md). |
|![Niet aanbevolen.](media/storage-files-migration-overview/circle-red-x.png)| AzCopy | Ondersteund. | Kopieert geen metagegevens. |
|![Niet aanbevolen.](media/storage-files-migration-overview/circle-red-x.png)| Azure Opslagverkenner | Ondersteund. | Kopieert geen metagegevens. |
|![Niet aanbevolen.](media/storage-files-migration-overview/circle-red-x.png)| Azure Data Factory | Ondersteund. | Kopieert geen metagegevens. |
|||||

*\*Volledige getrouwheid: voldoet aan of overtreft azure-mogelijkheden voor bestandsshare.*

### <a name="migration-helper-tools"></a>Hulpprogramma's voor migratiehelper

In deze sectie worden hulpprogramma's weergegeven die helpen bij het plannen en uitvoeren van migraties.

* **RoboCopy, van Microsoft Corporation**

    Een van de meest toepasselijke kopieertools voor bestandsmigraties, wordt geleverd als onderdeel van Microsoft Windows. Vanwege de vele opties in deze tool, de belangrijkste [RoboCopy documentatie](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) is een nuttige bron.

* **TreeSize, van JAM Software GmbH**

    Azure File Sync schaalt voornamelijk met het aantal items (bestanden en mappen) en minder met het totale TiB-bedrag. Het hulpprogramma kan worden gebruikt om het aantal bestanden en mappen op uw Windows Server-volumes te bepalen. Bovendien kan het worden gebruikt om een perspectief te creëren voor een [Azure File Sync-implementatie](storage-sync-files-deployment-guide.md) - maar ook daarna, wanneer cloudtiering is ingeschakeld en u niet alleen het aantal items wilt zien, maar ook in welke mappen uw servercache het meest wordt gebruikt.
    Deze tool (geteste versie 4.4.1) is compatibel met cloudgelaagde bestanden. Het zal niet leiden tot het terugroepen van gelaagde bestanden tijdens de normale werking.


## <a name="next-steps"></a>Volgende stappen

1. Maak een plan voor welke implementatie van Azure-bestandsshares (alleen in de cloud of hybride) u nastreeft.
2. Bekijk de lijst met beschikbare migratiegidsen om de gedetailleerde handleiding te vinden die overeenkomt met uw bron en implementatie van Azure-bestandsshares.

Er is meer informatie beschikbaar over de Azure Files-technologieën die in dit artikel worden genoemd:

* [Overzicht van Azure-bestandsshare](storage-files-introduction.md)
* [Planning voor een Azure Files Sync-implementatie](storage-sync-files-planning.md)
* [Azure-bestandssynchronisatie: cloudlaag](storage-sync-cloud-tiering.md)
