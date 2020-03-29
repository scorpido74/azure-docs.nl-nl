---
title: 'Azure AD Connect: automatische upgrade | Microsoft Documenten'
description: In dit onderwerp wordt de ingebouwde automatische upgradefunctie in Azure AD Connect-synchronisatie beschreven.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6b395e8f-fa3c-4e55-be54-392dd303c472
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/26/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: bfd61b78ca3027ade1f2f48dec33e0a8ed508d3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60349817"
---
# <a name="azure-ad-connect-automatic-upgrade"></a>Azure AD Connect: automatische upgrade
Deze functie werd geïntroduceerd met build [1.1.105.0 (uitgebracht in februari 2016).](reference-connect-version-history.md#111050)  Deze functie is bijgewerkt in [build 1.1.561](reference-connect-version-history.md#115610) en ondersteunt nu extra scenario's die voorheen niet werden ondersteund.

## <a name="overview"></a>Overzicht
Ervoor zorgen dat uw Azure AD Connect-installatie altijd up-to-date is, is nog nooit zo eenvoudig geweest met de **automatische upgradefunctie.** Deze functie is standaard ingeschakeld voor expresinstallaties en DirSync-upgrades. Wanneer een nieuwe versie wordt uitgebracht, wordt uw installatie automatisch bijgewerkt.
Automatische upgrade is standaard ingeschakeld voor het volgende:

* Installatie van express-instellingen en DirSync-upgrades.
* Met behulp van SQL Express LocalDB, dat is wat Express-instellingen altijd gebruiken. DirSync met SQL Express maakt ook gebruik van LocalDB.
* Het AD-account is de standaardMSOL_-account dat is gemaakt door Express-instellingen en DirSync.
* Hebben minder dan 100.000 objecten in de metaverse.

De huidige status van automatische upgrade kan worden `Get-ADSyncAutoUpgrade`bekeken met de PowerShell-cmdlet. Het heeft de volgende staten:

| Status | Opmerking |
| --- | --- |
| Ingeschakeld |Automatische upgrade is ingeschakeld. |
| Onderbroken |Alleen ingesteld door het systeem. Het systeem komt **momenteel niet** in aanmerking voor automatische upgrades. |
| Uitgeschakeld |Automatische upgrade is uitgeschakeld. |

U wijzigen tussen **Ingeschakeld** `Set-ADSyncAutoUpgrade`en **uitgeschakeld** met. Alleen het systeem moet de status **Opgeschort**instellen.  Voorafgaand aan 1.1.750.0 zou de cmdlet Set-ADSyncAutoUpgrade auto-upgrade blokkeren als de status van automatische upgrade is ingesteld op Opgeschort. Deze functionaliteit is nu veranderd, zodat het auto-upgrade niet blokkeert.

Automatische upgrade maakt gebruik van Azure AD Connect Health voor de upgrade-infrastructuur. Controleer voor automatische upgrade naar het werk of u de URL's in uw proxyserver voor **Azure AD Connect-status** hebt geopend zoals gedocumenteerd in [Office 365-URL's en IP-adresbereiken.](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)


Als de **gebruikersinterface van Synchronisatieservicebeheer** wordt uitgevoerd op de server, wordt de upgrade opgeschort totdat de gebruikersinterface is gesloten.

## <a name="troubleshooting"></a>Problemen oplossen
Als uw Connect-installatie zichzelf niet upgradet zoals verwacht, volgt u deze stappen om erachter te komen wat er mis kan zijn.

Ten eerste moet u niet verwachten dat de automatische upgrade wordt geprobeerd op de eerste dag dat een nieuwe versie wordt uitgebracht. Er is een opzettelijke willekeur voordat een upgrade wordt geprobeerd, dus wees niet gealarmeerd als uw installatie niet onmiddellijk wordt bijgewerkt.

Als u denkt dat er iets `Get-ADSyncAutoUpgrade` niet klopt, dan eerst lopen om ervoor te zorgen automatische upgrade is ingeschakeld.

Zorg er vervolgens voor dat u de vereiste URL's in uw proxy of firewall hebt geopend. Automatische update maakt gebruik van Azure AD Connect Health zoals beschreven in het [overzicht](#overview). Als u een proxy gebruikt, controleert u of Status is geconfigureerd om een [proxyserver](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy)te gebruiken. Test ook de [Health-connectiviteit met](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) Azure AD.

Nu de verbinding met Azure AD is geverifieerd, is het tijd om de gebeurtenislogboeken te bekijken. Start de gebeurtenisviewer en kijk in de **gebeurtenislog toepassing.** Voeg een eventlogfilter toe voor de bron **Azure AD Connect Upgrade** en het gebeurtenis-id-bereik **300-399**.  
![Eventlogfilter voor automatische upgrade](./media/how-to-connect-install-automatic-upgrade/eventlogfilter.png)  

U nu de eventlogs zien die zijn gekoppeld aan de status voor automatische upgrade.  
![Eventlogfilter voor automatische upgrade](./media/how-to-connect-install-automatic-upgrade/eventlogresult.png)  

De resultaatcode heeft een voorvoegsel met een overzicht van de status.

| Voorvoegsel van resultaatcode | Beschrijving |
| --- | --- |
| Geslaagd |De installatie is geüpgraded. |
| UpgradeAfgebroken |Een tijdelijke voorwaarde gestopt met de upgrade. Het zal opnieuw worden geprobeerd en de verwachting is dat het later slaagt. |
| UpgradeNotSupported |Het systeem heeft een configuratie die blokkeert het systeem automatisch wordt bijgewerkt. Het zal opnieuw worden geprobeerd om te zien of de status verandert, maar de verwachting is dat het systeem handmatig moet worden bijgewerkt. |

Hier is een lijst van de meest voorkomende berichten die u vindt. Het vermeldt niet alles, maar de resultaatboodschap moet duidelijk zijn met wat het probleem is.

| Resultaatbericht | Beschrijving |
| --- | --- |
| **UpgradeAfgebroken** | |
| UpgradeAbortedCouldNotSetUpgradeMarker |Kon niet schrijven naar het register. |
| GeünmeerdeOnvoldoendedatabasemachtigingen |De groep ingebouwde beheerders heeft geen machtigingen voor de database. Handmatig upgraden naar de nieuwste versie van Azure AD Connect om dit probleem op te lossen. |
| UpgradeAfgebrokedOnvoldoendeDiskSpace |Er is niet genoeg schijfruimte om een upgrade te ondersteunen. |
| Afgebroken beveiligingsgroepen niet aanwezig bijwerken |Kon niet vinden en oplossen van alle beveiligingsgroepen die worden gebruikt door de sync engine. |
| Geünmeerde mislukte serviceCanNotBeStarted |De NT-service **Microsoft Azure AD Sync** kan niet worden gestart. |
| UpgradeAfgebrokedServiceCanNotBeStopped |De NT-service **Microsoft Azure AD Sync** is niet gestopt. |
| Geünnoedserviceisnotactief bijwerken |De NT-service **Microsoft Azure AD Sync** wordt niet uitgevoerd. |
| UpgradeAfgebrokenSyncCycleDisabled |De optie SyncCycle in de [planner](how-to-connect-sync-feature-scheduler.md) is uitgeschakeld. |
| UpgradeAfgebrokedSyncExeInuse |De [gebruikersinterface van de synchronisatieservicemanager](how-to-connect-sync-service-manager-ui.md) is geopend op de server. |
| UpgradeAbortedSyncOrConfigurationInProgress |De wizard Installatie wordt uitgevoerd of er is een synchronisatie gepland buiten de planner. |
| **UpgradeNotSupported** | |
| UpgradeNotSupportedAdfsSignInMethod | U hebt Adfs geselecteerd als aanmeldingsmethode. |
| UpgradeNotSupportedCustomizedSyncRules |U hebt uw eigen aangepaste regels aan de configuratie toegevoegd. |
| UpgradeNotSupportedDeviceWritebackIngeschakeld |U hebt de [functie voor het terugschrijven van](how-to-connect-device-writeback.md) het apparaat ingeschakeld. |
| UpgradeNotSupportedGroupWritebackIngeschakeld |U hebt de [functie voor groepsafschrijving](how-to-connect-preview.md#group-writeback) ingeschakeld. |
| UpgradeNotSupportedInvalidPersistedState |De installatie is geen Express-instellingen of een DirSync-upgrade. |
| UpgradeNotSupportedMetaverseSizeExceeededed |Je hebt meer dan 100.000 objecten in de metaverse. |
| UpgradeNotSupportedMultiForestSetup |U maakt verbinding met meer dan één bos. Express-opstelling maakt slechts verbinding met één forest. |
| UpgradeNotSupportedNonLocalDbInstall |U gebruikt geen SQL Server Express LocalDB-database. |
| UpgradeNotSupportedNonMsolAccount |Het [AD DS Connector-account](reference-connect-accounts-permissions.md#ad-ds-connector-account) is niet meer de standaard MSOL_-account. |
| UpgradeNotSupportedNotConfiguredSignInMethod | Wanneer u AAD Connect instelt, kiest u *Niet configureren* bij het selecteren van de aanmeldingsmethode. |
| UpgradeNotSupportedPtaSignInMethod | U hebt Verificatie doorgeven als aanmeldingsmethode geselecteerd. |
| UpgradeNotSupportedStagingModeIngeschakeld |De server is ingesteld om in [de faseringsmodus](how-to-connect-sync-staging-server.md)te staan. |
| UpgradeNotSupportedUserWritebackIngeschakeld |U hebt de [terugschrijffunctie van](how-to-connect-preview.md#user-writeback) de gebruiker ingeschakeld. |

## <a name="next-steps"></a>Volgende stappen
Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).
