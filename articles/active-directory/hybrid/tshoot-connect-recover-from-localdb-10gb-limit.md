---
title: 'Azure AD Connect: herstellen van LocalDB 10GB-limiet probleem | Microsoft Docs'
description: In dit onderwerp wordt beschreven hoe u Azure AD Connect-synchronisatie service herstelt wanneer er LocalDB 10GB-limiet problemen optreden.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 41d081af-ed89-4e17-be34-14f7e80ae358
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d420c64c5834f7d3cb11d2f5f59e3ed85a54891
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/26/2020
ms.locfileid: "60386921"
---
# <a name="azure-ad-connect-how-to-recover-from-localdb-10-gb-limit"></a>Azure AD Connect: probleem met LocalDB met limiet van 10 GB oplossen
Azure AD Connect vereist een SQL Server-database voor het opslaan van identiteitsgegevens. U kunt de standaard SQL Server 2012 Express LocalDB gebruiken die samen met Azure AD Connect is geïnstalleerd, maar ook uw eigen volledige SQL. Voor SQL Server Express geldt een limiet van 10 GB. Wanneer u LocalDB gebruikt en deze limiet is bereikt, kan de Azure AD Connect-synchronisatieservice niet langer starten of goed synchroniseren. In dit artikel worden de herstel stappen beschreven.

## <a name="symptoms"></a>Symptomen
Er zijn twee veelvoorkomende symptomen:

* Azure AD Connect-synchronisatie service **wordt uitgevoerd,** maar kan niet worden gesynchroniseerd met de fout *' Stopped-data base-disk-full '* .

* Azure AD Connect synchronisatie service **kan niet worden gestart**. Wanneer u de service probeert te starten, mislukt de gebeurtenis 6323 en het fout bericht *' er is een fout opgetreden op de server omdat SQL Server geen schijf ruimte meer heeft. '*

## <a name="short-term-recovery-steps"></a>Herstel stappen voor de korte termijn
In deze sectie worden de stappen beschreven voor het vrijmaken van de database ruimte die is vereist voor de Azure AD Connect-synchronisatie service om de bewerking te hervatten. De stappen zijn onder andere:
1. [De status van de synchronisatie service bepalen](#determine-the-synchronization-service-status)
2. [De data base verkleinen](#shrink-the-database)
3. [Gegevens uit de uitvoerings geschiedenis verwijderen](#delete-run-history-data)
4. [Bewaar periode voor de gegevens van de uitvoerings geschiedenis verkorten](#shorten-retention-period-for-run-history-data)

### <a name="determine-the-synchronization-service-status"></a>De status van de synchronisatie service bepalen
Bepaal eerst of de synchronisatie service nog steeds actief is:

1. Meld u als beheerder aan bij uw Azure AD Connect-server.

2. Ga naar **service besturings beheer**.

3. Controleer de status van **Microsoft Azure AD synchronisatie**.


4. Als deze wordt uitgevoerd, moet u de service niet stoppen of opnieuw starten. Sla [de stap database verkleinen](#shrink-the-database) en ga naar de stap [uitvoerings geschiedenis verwijderen](#delete-run-history-data) .

5. Als deze niet actief is, probeert u de service te starten. Als de service met succes wordt gestart, slaat u [de database stap kleiner](#shrink-the-database) en gaat u naar de stap [Run Data History-gegevens verwijderen](#delete-run-history-data) . Ga anders verder met [de stap data base verkleinen](#shrink-the-database) .

### <a name="shrink-the-database"></a>De data base verkleinen
Gebruik de verkleinings bewerking om voldoende DB-ruimte vrij te maken om de synchronisatie service te starten. Hiermee wordt DB-ruimte vrijgemaakt door spaties in de data base te verwijderen. Deze stap is het beste omdat u niet zeker weet dat u altijd ruimte kunt herstellen. Lees dit artikel [een Data Base verkleinen](https://msdn.microsoft.com/library/ms189035.aspx)voor meer informatie over de verkleinings bewerking.

> [!IMPORTANT]
> Sla deze stap over als u de synchronisatie service kunt uitvoeren. Het wordt afgeraden de SQL-Data Base te verkleinen omdat deze kan leiden tot slechte prestaties vanwege een verhoogde fragmentatie.

De naam van de data base die is gemaakt voor Azure AD Connect is **ADSync**. Als u een verkleinings bewerking wilt uitvoeren, moet u zich aanmelden als sysadmin of DBO van de data base. Tijdens de Azure AD Connect-installatie worden de sysadmin-rechten verleend aan de volgende accounts:
* Lokale beheerders
* Het gebruikers account dat is gebruikt om de Azure AD Connect-installatie uit te voeren.
* Het synchronisatie service account dat wordt gebruikt als de context van de Azure AD Connect-synchronisatie service.
* De ADSyncAdmins van de lokale groep die tijdens de installatie is gemaakt.

1. Maak een back-up van de data base door **ADSync. MDF** en **ADSync_log. ldf** -bestanden `%ProgramFiles%\Microsoft Azure AD Sync\Data` te kopiëren die zich bevinden op een veilige locatie.

2. Een nieuwe Power shell-sessie starten.

3. Navigeer naar map `%ProgramFiles%\Microsoft SQL Server\110\Tools\Binn`.

4. Start **Sqlcmd** Utility door de opdracht `./SQLCMD.EXE -S "(localdb)\.\ADSync" -U <Username> -P <Password>`uit te voeren met de referentie van een sysadmin of de data base dbo.

5. Als u de Data Base wilt verkleinen, typt `DBCC Shrinkdatabase(ADSync,1);`u bij het Sqlcmd-prompt (1> `GO` ), gevolgd door in de volgende regel.

6. Als de bewerking is geslaagd, probeert u de synchronisatie service opnieuw te starten. Als u de synchronisatie service kunt starten, gaat u naar de stap [Run History-gegevens verwijderen](#delete-run-history-data) . Zo niet, neem dan contact op met de ondersteuning.

### <a name="delete-run-history-data"></a>Gegevens uit de uitvoerings geschiedenis verwijderen
Azure AD Connect behouden standaard Maxi maal zeven dagen aan uitvoerings geschiedenis gegevens. In deze stap verwijderen we de gegevens uit de uitvoerings geschiedenis om DB-ruimte vrij te maken, zodat de synchronisatie service van Azure AD Connect opnieuw kan worden gestart.

1. Start **Synchronization Service Manager** door naar Start → synchronisatie service te gaan.

2. Ga naar het tabblad **bewerkingen** .

3. Onder **acties**, selecteert u **uitvoeringen wissen**...

4. U kunt kiezen voor **alle uitvoeringen wissen** of **uitvoeringen wissen voor... datum \<>** optie. Het wordt aanbevolen dat u begint met het wissen van uitvoerings geschiedenis gegevens die ouder zijn dan twee dagen. Als u doorgaat met het probleem met de omvang van de data base, kiest u de optie **alle uitvoeringen wissen** .

### <a name="shorten-retention-period-for-run-history-data"></a>Bewaar periode voor de gegevens van de uitvoerings geschiedenis verkorten
Deze stap is het verminderen van de kans op problemen met de limiet van 10 GB na meerdere synchronisatie cycli.

1. Open een nieuwe Power shell-sessie.

2. Voer `Get-ADSyncScheduler` uit en noteer de eigenschap PurgeRunHistoryInterval, waarmee de huidige Bewaar periode wordt opgegeven.

3. Voer `Set-ADSyncScheduler -PurgeRunHistoryInterval 2.00:00:00` uit om de Bewaar periode in te stellen op twee dagen. Pas de Bewaar periode zo nodig aan.

## <a name="long-term-solution--migrate-to-full-sql"></a>Lange termijn oplossing: migreren naar volledige SQL
In het algemeen is het probleem indicatief dat de grootte van 10 GB-data base niet langer voldoende is voor Azure AD Connect om uw on-premises Active Directory te synchroniseren met Azure AD. Het is raadzaam om over te scha kelen naar het gebruik van de volledige versie van SQL Server. U kunt de LocalDB van een bestaande Azure AD Connect-implementatie niet rechtstreeks vervangen door de database van de volledige versie van SQL. In plaats daarvan implementeert u een nieuwe Azure AD Connect-server met de volledige SQL. U doet er verstandig aan een swingmigratie uit te voeren, waarbij de nieuwe Azure AD Connect-server (met SQL-database) wordt geïmplementeerd als testserver, naast de bestaande Azure AD Connect-server (met LocalDB). 
* Zie het artikel [Aangepaste installatie van Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-custom) voor instructies over het configureren van externe SQL met Azure AD Connect.
* Zie het artikel [Azure AD Connect: Upgrade from a previous version to the latest](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version#swing-migration) (Azure AD Connect: upgraden van een vorige naar de meest recente versie) voor instructies over swingmigratie voor een Azure AD Connect-upgrade.

## <a name="next-steps"></a>Volgende stappen
Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).
