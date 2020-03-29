---
title: 'Azure AD Connect: selecteer uw installatietype | Microsoft Documenten'
description: In dit onderwerp u het installatietype selecteren dat moet worden gebruikt voor Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 90a624a6b3b4696899af0d8606f653df260cc201
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60348277"
---
# <a name="select-which-installation-type-to-use-for-azure-ad-connect"></a>Het installatietype voor Azure AD Connect selecteren
Azure AD Connect heeft twee installatietypen voor nieuwe installatie: Express en aangepast. Dit onderwerp helpt u om te beslissen welke optie te gebruiken tijdens de installatie.

## <a name="express"></a>Express
Express is de meest voorkomende optie en wordt gebruikt door ongeveer 90% van alle nieuwe installaties. Het is ontworpen om een configuratie te bieden die werkt voor de meest voorkomende klantscenario's.

Het veronderstelt:

- U hebt één Active Directory-forest on-premises.
- U hebt een bedrijfsbeheerdersaccount dat u voor de installatie gebruiken.
- U hebt minder dan 100.000 objecten in uw on-premises Active Directory.

Je krijgt:

- [Synchronisatie van wachtwoordhash](how-to-connect-password-hash-synchronization.md) van on-premises naar Azure AD voor eenmalige aanmelding.
- Een configuratie die [gebruikers, groepen, contactpersonen en Windows 10-computers](concept-azure-ad-connect-sync-default-configuration.md)synchroniseert.
- Synchronisatie van alle in aanmerking komende objecten in alle domeinen en alle OE's.
- [Automatische upgrade](how-to-connect-install-automatic-upgrade.md) is ingeschakeld om ervoor te zorgen dat u altijd de nieuwste beschikbare versie gebruikt.

Opties waar u Express nog steeds gebruiken:

- Als u niet alle Ok's wilt synchroniseren, u Express nog steeds gebruiken en op de laatste pagina u de selectie deselectie uitdeschakelt **Start het synchronisatieproces...***. Voer vervolgens de wizard Installatie opnieuw uit en wijzig de OK's in [configuratieopties](how-to-connect-installation-wizard.md#customize-synchronization-options) en schakel geplande synchronisatie in.
- U wilt een van de functies in Azure AD Premium inschakelen, zoals terugschrijven van wachtwoorden. Ga eerst door express om de eerste installatie voltooid te krijgen. Voer vervolgens de wizard installatie opnieuw uit en wijzig de [configuratieopties](how-to-connect-installation-wizard.md#customize-synchronization-options).

## <a name="custom"></a>Aangepast telefoonnummer
Het aangepaste pad biedt veel meer opties dan express. Het moet worden gebruikt in alle gevallen waarin de configuratie beschreven in de vorige sectie voor express is niet representatief voor uw organisatie.

Gebruiken wanneer:

- U hebt geen toegang tot een bedrijfsbeheerdersaccount in Active Directory.
- U hebt meer dan één forest of u bent van plan om in de toekomst meer dan één forest te synchroniseren.
- U hebt domeinen in uw forest die niet bereikbaar zijn vanaf de Connect-server.
- U bent van plan federatie- of doorreisverificatie te gebruiken voor aanmelding door gebruikers.
- U hebt meer dan 100.000 objecten en moet een volledige SQL Server gebruiken.
- U bent van plan om groepsfiltering te gebruiken en niet alleen domein- of OU-filtering.

## <a name="upgrade-from-dirsync"></a>Upgrade van DirSync
Als u momenteel DirSync gebruikt, voert u de stappen in [Upgrade van DirSync](how-to-dirsync-upgrade-get-started.md) uit om uw bestaande configuratie te upgraden. Er zijn twee verschillende upgradeopties:

- In-place upgrade om Connect op dezelfde server te installeren.
- Parallelle implementatie om Connect op een nieuwe server te installeren terwijl de bestaande DirSync-server nog operationeel is.

## <a name="upgrade-from-azure-ad-sync"></a>Upgraden van Azure AD Sync
Als u momenteel Azure AD Sync gebruikt, u [dezelfde stappen](how-to-upgrade-previous-version.md) volgen als wanneer u een upgrade uitvoert van de ene Connect-versie naar een nieuwere versie. Er zijn twee verschillende upgradeopties:

- In-place upgrade om Connect op dezelfde server te installeren.
- Swing-migratie om Connect op een nieuwe server te installeren terwijl de bestaande Azure AD Sync-server nog steeds operationeel is.

## <a name="migrate-from-fim2010-or-mim2016"></a>Migreren van FIM2010 of MIM2016
Als u momenteel Forefront Identity Manager 2010 of Microsoft Identity Manager 2016 gebruikt met de Azure AD Connector, is uw enige optie een migratie. Volg de stappen beschreven in [swing-migratie](how-to-upgrade-previous-version.md#swing-migration). Vervang in de stappen elke vermelding van Azure AD Sync door FIM2010/MIM2016.

## <a name="next-steps"></a>Volgende stappen
Afhankelijk van de optie die u hebt geselecteerd om te gebruiken, gebruikt u de inhoudstabel links om uw artikel met de gedetailleerde stappen te vinden.
