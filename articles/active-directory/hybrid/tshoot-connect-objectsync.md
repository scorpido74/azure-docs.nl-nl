---
title: 'Azure AD Connect: problemen met objectsynchronisatie oplossen | Microsoft Documenten'
description: In dit onderwerp worden stappen gegeven voor het oplossen van problemen met objectsynchronisatie met behulp van de probleemoplossingstaak.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e10d00ed90248319801974c7c1e7fadf835024b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81407023"
---
# <a name="troubleshoot-object-synchronization-with-azure-ad-connect-sync"></a>Problemen met objectsynchronisatie oplossen met Azure AD Connect-synchronisatie
In dit artikel worden stappen gezet voor het oplossen van problemen met objectsynchronisatie door de taak voor het oplossen van problemen te gebruiken. Bekijk [deze korte video](https://aka.ms/AADCTSVideo)om te zien hoe probleemoplossing werkt in Azure Active Directory (Azure AD) Connect.

## <a name="troubleshooting-task"></a>Probleemoplossingvoor taak
Voor implementatie van Azure AD Connect met versie 1.1.749.0 of hoger, gebruikt u de probleemoplossingstaak in de wizard  om problemen met objectsynchronisatie op te lossen. Voor eerdere versies u problemen handmatig oplossen zoals [hier](tshoot-connect-object-not-syncing.md)beschreven.

### <a name="run-the-troubleshooting-task-in-the-wizard"></a>Taak voor probleemoplossing uitvoeren in de wizard
Voer de volgende stappen uit om de taak voor het oplossen van problemen in de wizard uit te voeren:

1.  Open een nieuwe Windows PowerShell-sessie op uw Azure AD Connect-server met de optie Uitvoeren als administrator.
2.  Uitvoeren `Set-ExecutionPolicy RemoteSigned` `Set-ExecutionPolicy Unrestricted`of .
3.  Start de wizard Azure AD Connect.
4.  Navigeer naar de pagina Extra taken, selecteer Problemen oplossen en klik op Volgende.
5.  Klik op de pagina Probleemoplossing op Starten om het menu probleemoplossing in PowerShell te starten.
6.  Selecteer Objectsynchronisatie oplossen in het hoofdmenu.
![Problemen met objectsynchronisatie oplossen](media/tshoot-connect-objectsync/objsynch11.png)

### <a name="troubleshooting-input-parameters"></a>Problemen met invoerparameters oplossen
De volgende invoerparameters zijn nodig voor de taak voor het oplossen van problemen:
1.  **Objectdistinguished Name** : dit is de voorname naam van het object dat probleemoplossing nodig heeft
2.  **NAAM AD-connector** – Dit is de naam van het AD-forest waar het bovenstaande object zich bevindt.
3.  Globale beheerdersreferenties ![voor Azure AD-tenant](media/tshoot-connect-objectsync/objsynch1.png)

### <a name="understand-the-results-of-the-troubleshooting-task"></a>De resultaten van de probleemoplossingstaak begrijpen
De taak voor het oplossen van problemen voert de volgende controles uit:

1.  UPN-mismatch detecteren als het object is gesynchroniseerd met Azure Active Directory
2.  Controleren of object is gefilterd vanwege domeinfiltering
3.  Controleren of object is gefilterd vanwege het filteren van de o.a.-problemen
4.  Controleren of objectsynchronisatie is geblokkeerd vanwege een gekoppeld postvak
5. Controleren of object een dynamische distributiegroep is die niet moet worden gesynchroniseerd

De rest van deze sectie beschrijft specifieke resultaten die door de taak worden geretourneerd. In elk geval biedt de taak een analyse, gevolgd door aanbevolen acties om het probleem op te lossen.

## <a name="detect-upn-mismatch-if-object-is-synced-to-azure-active-directory"></a>UPN-mismatch detecteren als object wordt gesynchroniseerd met Azure Active Directory
### <a name="upn-suffix-is-not-verified-with-azure-ad-tenant"></a>UPN-achtervoegsel is NIET geverifieerd met Azure AD-tenant
Wanneer het upn-achtervoegsel (UserPrincipalName)/Alternate Login ID niet is geverifieerd met de Azure AD-tenant, vervangt Azure Active Directory de UPN-achtervoegsels door de standaarddomeinnaam 'onmicrosoft.com'.

![Azure AD vervangt UPN](media/tshoot-connect-objectsync/objsynch2.png)

### <a name="azure-ad-tenant-dirsync-feature-synchronizeupnformanagedusers-is-disabled"></a>Azure AD-tenant DirSync-functie 'SynchronizeUpnForManagedUsers' is uitgeschakeld
Wanneer de Azure AD-tenant DirSync-functie 'SynchronizeUpnForManagedUsers' is uitgeschakeld, staat Azure Active Directory synchronisatie-updates voor UserPrincipalName/Alternate Login ID voor gelicentieerde gebruikersaccounts met beheerde verificatie niet toe.

![SynchronizeUpnForManagedUsers](media/tshoot-connect-objectsync/objsynch4.png)

## <a name="object-is-filtered-due-to-domain-filtering"></a>Object wordt gefilterd vanwege domeinfiltering
### <a name="domain-is-not-configured-to-sync"></a>Domein is niet geconfigureerd om te synchroniseren
Object is buiten bereik omdat het domein niet is geconfigureerd. In het onderstaande voorbeeld is het object buiten het synchronisatiebereik omdat het domein waartoe het behoort, wordt gefilterd op synchronisatie.

![Domein is niet geconfigureerd om te synchroniseren](media/tshoot-connect-objectsync/objsynch5.png)

### <a name="domain-is-configured-to-sync-but-is-missing-run-profilesrun-steps"></a>Domein is geconfigureerd om te synchroniseren, maar ontbreekt run profielen / uitvoeren stappen
Object is buiten bereik omdat het domein ontbreekt, voer profielen/uitvoerenstappen uit. In het onderstaande voorbeeld is het object buiten het bereik van de synchronisatie omdat het domein waartoe het behoort, uitvoerenstappen mist voor het runprofiel Volledig importeren.
![ontbrekende runprofielen](media/tshoot-connect-objectsync/objsynch6.png)

## <a name="object-is-filtered-due-to-ou-filtering"></a>Object wordt gefilterd vanwege het filteren van de organisatie van de organisatie van de organisatie van de
Het object is buiten het synchronisatiebereik als gevolg van de configuratie van de ORGANISATIE-filtering. In het onderstaande voorbeeld behoort het object tot OU=NoSync,DC=bvtadwbackdc,DC=com.  Deze organisatie-eenheid is niet opgenomen in het synchronisatiebereik.</br>

![ORGANISATIE-EENHEID](./media/tshoot-connect-objectsync/objsynch7.png)

## <a name="linked-mailbox-issue"></a>Probleem met gekoppeld postvak
Een gekoppeld postvak wordt verondersteld te zijn gekoppeld aan een extern hoofdaccount in een ander vertrouwd accountforest. Als er geen extern hoofdaccount is, synchroniseert Azure AD Connect het gebruikersaccount niet met het gekoppelde postvak in het Exchange-forest met de Azure AD-tenant.</br>
![Gekoppeld postvak](./media/tshoot-connect-objectsync/objsynch12.png)

## <a name="dynamic-distribution-group-issue"></a>Probleem met dynamische distributiegroep
Vanwege verschillende verschillen tussen on-premises Active Directory en Azure Active Directory synchroniseert Azure AD Connect geen dynamische distributiegroepen met de Azure AD-tenant.

![Dynamische distributiegroep](./media/tshoot-connect-objectsync/objsynch13.png)

## <a name="html-report"></a>HTML-rapport
Naast het analyseren van het object, genereert de probleemoplossingstaak ook een HTML-rapport met alles over het object. Dit HTML-rapport kan worden gedeeld met het ondersteuningsteam om indien nodig verdere probleemoplossing te kunnen doen.

![HTML-rapport](media/tshoot-connect-objectsync/objsynch8.png)

## <a name="next-steps"></a>Volgende stappen
Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).
