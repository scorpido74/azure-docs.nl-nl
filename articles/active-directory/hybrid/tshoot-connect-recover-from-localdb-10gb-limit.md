---
title: 'Azure AD Connect: het probleem met de limiet van LocalDB 10 GB herstellen | Microsoft Documenten'
description: In dit onderwerp wordt beschreven hoe u azure AD Connect-synchronisatieservice herstellen wanneer deze een probleem met de limietvan LocalDB 10 GB tegenkomt.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60386921"
---
# <a name="azure-ad-connect-how-to-recover-from-localdb-10-gb-limit"></a>Azure AD Connect: probleem met LocalDB met limiet van 10 GB oplossen
Azure AD Connect vereist een SQL Server-database voor het opslaan van identiteitsgegevens. U kunt de standaard SQL Server 2012 Express LocalDB gebruiken die samen met Azure AD Connect is geïnstalleerd, maar ook uw eigen volledige SQL. Voor SQL Server Express geldt een limiet van 10 GB. Wanneer u LocalDB gebruikt en deze limiet is bereikt, kan de Azure AD Connect-synchronisatieservice niet langer starten of goed synchroniseren. In dit artikel vindt u de herstelstappen.

## <a name="symptoms"></a>Symptomen
Er zijn twee veel voorkomende symptomen:

* Azure AD Connect Synchronization Service **wordt uitgevoerd,** maar kan niet worden gesynchroniseerd met de fout *'gestopt-database-schijfvol'.*

* Azure AD Connect-synchronisatieservice **kan niet worden gestart**. Wanneer u de service probeert te starten, mislukt deze met gebeurtenis 6323 en foutbericht *'De server heeft een fout ondervonden omdat SQL Server geen schijfruimte meer heeft'.*

## <a name="short-term-recovery-steps"></a>Herstelstappen op korte termijn
In deze sectie worden de stappen weergegeven om db-ruimte terug te winnen die nodig is voor Azure AD Connect Synchronization Service om de bewerking te hervatten. De stappen omvatten:
1. [De status van synchronisatieservice bepalen](#determine-the-synchronization-service-status)
2. [De database verkleinen](#shrink-the-database)
3. [Run-geschiedenisgegevens verwijderen](#delete-run-history-data)
4. [Bewaartermijn voor run history-gegevens verkorten](#shorten-retention-period-for-run-history-data)

### <a name="determine-the-synchronization-service-status"></a>De status van synchronisatieservice bepalen
Bepaal eerst of de synchronisatieservice nog steeds actief is of niet:

1. Meld u aan bij uw Azure AD Connect-server als beheerder.

2. Ga naar **Service Control Manager**.

3. Controleer de status van **Microsoft Azure AD Sync**.


4. Als deze wordt uitgevoerd, stopt u niet of start u de service niet opnieuw. De [databasestap](#shrink-the-database) verkleinen en ga naar De stap [Run History data](#delete-run-history-data) verwijderen.

5. Als deze niet wordt uitgevoerd, probeert u de service te starten. Als de service succesvol wordt gestart, slaat [u de databasestap verkleinen](#shrink-the-database) en gaat u naar De stap Run [History-gegevens](#delete-run-history-data) verwijderen. Ga anders verder met [De databasestap verkleinen.](#shrink-the-database)

### <a name="shrink-the-database"></a>De database verkleinen
Gebruik de bewerking Krimpen om voldoende DB-ruimte vrij te maken om de synchronisatieservice te starten. Het maakt DB-ruimte vrij door witruimten in de database te verwijderen. Deze stap is het beste, omdat het niet gegarandeerd is dat u altijd ruimte herstellen. Lees dit artikel Een database [verkleinen](https://msdn.microsoft.com/library/ms189035.aspx)voor meer informatie over de bewerking Krimpen.

> [!IMPORTANT]
> Sla deze stap over als u de synchronisatieservice uitvoeren. Het wordt afgeraden om de SQL DB te verkleinen, omdat deze kan leiden tot slechte prestaties als gevolg van verhoogde fragmentatie.

De naam van de database die is gemaakt voor Azure AD Connect is **ADSync**. Als u een Shrink-bewerking wilt uitvoeren, moet u zich aanmelden als de sysadmin of DBO van de database. Tijdens de installatie van Azure AD Connect krijgen de volgende accounts sysadmin-rechten:
* Lokale beheerders
* Het gebruikersaccount dat is gebruikt om de installatie van Azure AD Connect uit te voeren.
* Het Synchronisatieservice-account dat wordt gebruikt als de bedrijfscontext van Azure AD Connect Synchronization Service.
* De lokale groep ADSyncAdmins die is gemaakt tijdens de installatie.

1. Maak een back-up van de database door **ADSync.mdf-** en **ADSync_log.ldf-bestanden** die zich onder `%ProgramFiles%\Microsoft Azure AD Sync\Data` de veilige locatie bevinden, te kopiëren.

2. Start een nieuwe PowerShell-sessie.

3. Navigeer naar `%ProgramFiles%\Microsoft SQL Server\110\Tools\Binn`map .

4. Start **sqlcmd** utility door `./SQLCMD.EXE -S "(localdb)\.\ADSync" -U <Username> -P <Password>`de opdracht uit te voeren , met behulp van de referentie van een sysadmin of de database DBO.

5. Om de database te verkleinen, voer je bij `DBCC Shrinkdatabase(ADSync,1);`de `GO` sqlcmd-prompt (1>) in, gevolgd door in de volgende regel.

6. Als de bewerking is geslaagd, probeert u de synchronisatieservice opnieuw te starten. Als u de synchronisatieservice starten, gaat u naar De stap [Run-geschiedenisgegevens](#delete-run-history-data) verwijderen. Zo niet, neem dan contact op met de ondersteuning.

### <a name="delete-run-history-data"></a>Run-geschiedenisgegevens verwijderen
Azure AD Connect behoudt standaard maximaal zeven dagen aan run-geschiedenisgegevens. In deze stap verwijderen we de run-geschiedenisgegevens om DB-ruimte terug te winnen, zodat Azure AD Connect Synchronization Service opnieuw kan beginnen met synchroniseren.

1. Start **Synchronisatieservicebeheer** door naar STARTEN → Synchronisatieservice te gaan.

2. Ga naar het tabblad **Bewerkingen.**

3. Selecteer Onder **Acties**de optie **Uitvoeren wissen**...

4. U kiezen **voor Alle runs** wissen of Clear runs **vóór... datum \<>** optie. Het wordt aanbevolen om te beginnen met het wissen van run-geschiedenisgegevens die ouder zijn dan twee dagen. Als u nog steeds in db grootte probleem, kies dan de **optie Alle uitvoeringen wissen.**

### <a name="shorten-retention-period-for-run-history-data"></a>Bewaartermijn voor run history-gegevens verkorten
Deze stap is om de kans op het uitvoeren van de 10-GB limiet probleem na meerdere synchronisatiecycli te verminderen.

1. Open een nieuwe PowerShell-sessie.

2. Voer `Get-ADSyncScheduler` de eigenschap PurgeRunHistoryInterval uit en noteer deze, waarin de huidige bewaartermijn wordt opgegeven.

3. Voer `Set-ADSyncScheduler -PurgeRunHistoryInterval 2.00:00:00` de bewaartermijn in om de bewaartermijn in te stellen op twee dagen. Pas de bewaartermijn zo nodig aan.

## <a name="long-term-solution--migrate-to-full-sql"></a>Langetermijnoplossing – Migreren naar volledige SQL
In het algemeen is het probleem een indicatie dat de databasegrootte van 10 GB niet langer voldoende is voor Azure AD Connect om uw on-premises Active Directory te synchroniseren met Azure AD. Het wordt aanbevolen om over te schakelen naar het gebruik van de volledige versie van SQL-server. U kunt de LocalDB van een bestaande Azure AD Connect-implementatie niet rechtstreeks vervangen door de database van de volledige versie van SQL. In plaats daarvan implementeert u een nieuwe Azure AD Connect-server met de volledige SQL. U doet er verstandig aan een swingmigratie uit te voeren, waarbij de nieuwe Azure AD Connect-server (met SQL-database) wordt geïmplementeerd als testserver, naast de bestaande Azure AD Connect-server (met LocalDB). 
* Zie het artikel [Aangepaste installatie van Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-custom) voor instructies over het configureren van externe SQL met Azure AD Connect.
* Zie het artikel [Azure AD Connect: Upgrade from a previous version to the latest](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version#swing-migration) (Azure AD Connect: upgraden van een vorige naar de meest recente versie) voor instructies over swingmigratie voor een Azure AD Connect-upgrade.

## <a name="next-steps"></a>Volgende stappen
Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).
