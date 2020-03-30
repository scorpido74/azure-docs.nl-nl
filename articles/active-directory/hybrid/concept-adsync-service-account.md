---
title: 'Azure AD Connect: ADSync-serviceaccount | Microsoft Documenten'
description: In dit onderwerp wordt het ADSync-serviceaccount beschreven en worden aanbevolen procedures met betrekking tot het account.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 06/27/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f228da5afc5998d8fa59ce2d720cec4c9f955b67
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67478717"
---
# <a name="adsync-service-account"></a>ADSync-serviceaccount
Azure AD Connect installeert een on-premises service die synchronisatie tussen Active Directory en Azure Active Directory orkestreert.  De ADSync-synchronisatieservice (Microsoft Azure AD Sync) wordt uitgevoerd op een server in uw on-premises omgeving.  De referenties voor de service worden standaard ingesteld in de Express-installaties, maar kunnen worden aangepast om te voldoen aan uw beveiligingsvereisten voor de organisatie.  Deze referenties worden niet gebruikt om verbinding te maken met uw on-premises forests of Azure Active Directory.

Het kiezen van het ADSync-serviceaccount is een belangrijke planningsbeslissing voordat u Azure AD Connect installeert.  Elke poging om de referenties na de installatie te wijzigen, leidt ertoe dat de service niet wordt gestart, de toegang tot de synchronisatiedatabase verliest en niet wordt geverifieerd met uw verbonden mappen (Azure en AD DS).  Er vindt geen synchronisatie plaats totdat de oorspronkelijke referenties zijn hersteld.

## <a name="the-default-adsync-service-account"></a>Het standaard ADSync-serviceaccount

Wanneer u op een lidserver wordt uitgevoerd, wordt de AdSync-service uitgevoerd in de context van een VSA (Virtual Service Account).  Vanwege een productbeperking wordt een aangepast serviceaccount gemaakt wanneer het is geïnstalleerd op een domeincontroller.  Als het serviceaccount Express-instellingen niet voldoet aan de beveiligingsvereisten voor uw organisatie, implementeert u Azure AD Connect door de optie Aanpassen te kiezen.  Kies vervolgens de serviceaccountoptie die voldoet aan de vereisten van uw organisatie.

>[!NOTE]
>Het standaardserviceaccount dat is geïnstalleerd op een domeincontroller is van het formulier Domain\AAD_InstallationIdentifier.  Het wachtwoord voor dit account wordt willekeurig gegenereerd en biedt aanzienlijke uitdagingen voor herstel en wachtwoordrotatie.  Microsoft raadt aan het serviceaccount aan te maken tijdens de eerste installatie op een domeincontroller om een zelfstandig of groepsManaged Service-account (sMSA / gMSA) te gebruiken

|Azure AD Connect-locatie|Serviceaccount gemaakt|
|-----|-----|
|Lidserver|NT-SERVICE\ADSync|
|Domeincontroller|Domein\AAD_74dc30c01e80 (zie notitie)|

## <a name="custom-adsync-service-accounts"></a>Aangepaste ADSync-serviceaccounts
Microsoft raadt aan om de ADSync-service uit te voeren in de context van een Virtual Service-account of een zelfstandig of groepsmanaged service-account.  Uw domeinbeheerder kan er ook voor kiezen om een serviceaccount te maken dat is ingericht om te voldoen aan uw specifieke beveiligingsvereisten voor de organisatie.   Als u het serviceaccount wilt aanpassen dat tijdens de installatie wordt gebruikt, kiest u de optie Aanpassen op de pagina Express-instellingen hieronder.   De volgende opties zijn beschikbaar:

- standaardaccount : Azure AD Connect voorziet in het serviceaccount zoals hierboven beschreven
- managed service account – gebruik een zelfstandige of groep MSA ingericht door uw beheerder
- domeinaccount – gebruik een domeinserviceaccount dat is ingericht door uw beheerder

![](media/concept-adsync-service-account/adsync1.png)

![](media/concept-adsync-service-account/adsync2.png)

## <a name="diagnosing-adsync-service-account-changes"></a>AdSync-serviceaccountwijzigingen diagnosticeren
Als u de referenties voor de ADSync-service na de installatie wijzigt, wordt de service niet gestart, wordt de toegang tot de synchronisatiedatabase verloren en wordt deze niet geverifieerd met uw verbonden mappen (Azure en AD DS).  Het verlenen van databasetoegang tot het nieuwe ADSync-serviceaccount is onvoldoende om dit probleem te herstellen. Er vindt geen synchronisatie plaats totdat de oorspronkelijke referenties zijn hersteld.

De ADSync-service geeft een bericht op foutniveau uit aan het gebeurtenislogboek wanneer deze niet kan worden gestart.  De inhoud van het bericht is afhankelijk van of de ingebouwde database (localdb) of volledige SQL in gebruik is.  Hieronder volgen voorbeelden van de gebeurtenislogboekvermeldingen die mogelijk aanwezig zijn.

### <a name="example-1"></a>Voorbeeld 1

De AdSync-serviceversleutelingssleutels konden niet worden gevonden en zijn opnieuw gemaakt.  Synchronisatie vindt pas plaats als dit probleem is verholpen.

Probleem oplossen De versleutelingssleutels voor Microsoft Azure AD Sync worden niet toegankelijk als de adsync-service aanmeldingsreferenties worden gewijzigd.  Als de referenties zijn gewijzigd, gebruikt u de toepassing Services om het account Aanmelding terug te wijzigen in de oorspronkelijk geconfigureerde waarde (bijvoorbeeld. NT SERVICE\AdSync) en start de service opnieuw.  Hierdoor wordt de correcte werking van de AdSync-service onmiddellijk hersteld.

Zie het volgende [artikel](https://go.microsoft.com/fwlink/?linkid=2086764) voor meer informatie.

### <a name="example-2"></a>Voorbeeld 2

De service kon niet worden gestart omdat er geen verbinding met de lokale database (localdb) kon worden gemaakt.

Problemen oplossen De Microsoft Azure AD Sync-service verliest de toegang tot de lokale databaseprovider als de adsync-service aanmeldingsreferenties worden gewijzigd.  Als de referenties zijn gewijzigd, gebruikt u de toepassing Services om het account Aanmelding terug te wijzigen in de oorspronkelijk geconfigureerde waarde (bijvoorbeeld. NT SERVICE\AdSync) en start de service opnieuw.  Hierdoor wordt de correcte werking van de AdSync-service onmiddellijk hersteld.

Zie het volgende [artikel](https://go.microsoft.com/fwlink/?linkid=2086764) voor meer informatie.

Aanvullende details De volgende foutgegevens zijn door de provider geretourneerd:
 

``` 
OriginalError=0x80004005 OLEDB Provider error(s): 
Description  = 'Login timeout expired'
Failure Code = 0x80004005
Minor Number = 0 
Description  = 'A network-related or instance-specific error has occurred while establishing a connection to SQL Server. Server is not found or not accessible. Check if instance name is correct and if SQL Server is configured to allow remote connections. For more information see SQL Server Books Online.'
```
## <a name="next-steps"></a>Volgende stappen
Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).