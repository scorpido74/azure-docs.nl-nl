---
title: 'Azure AD Connect: Hybride Azure AD join post configuratie taken | Microsoft Documenten'
description: In dit document worden details beschreven voor de configuratietaken die nodig zijn om de hybride Azure AD-join te voltooien
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: billmath
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/10/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ffb8243041bb93ba8be6a65bb83df6f84affaee3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049669"
---
# <a name="post-configuration-tasks-for-hybrid-azure-ad-join"></a>Taken na configuratie voor hybride Azure AD-koppeling

Nadat u Azure AD Connect hebt uitgevoerd om uw organisatie te configureren voor Hybride Azure AD-lid, zijn er een paar aanvullende stappen die u moet voltooien om die installatie af te ronden.  Voer alleen de stappen uit die van toepassing zijn op uw apparaten.

## <a name="1-configure-controlled-rollout-optional"></a>1. Gecontroleerde implementatie configureren (optioneel)
Alle met een domein verbonden apparaten met Windows 10 en Windows Server 2016 registreren zich automatisch bij Azure AD zodra alle configuratiestappen zijn voltooid. Als u liever een gecontroleerde implementatie hebt dan deze automatische registratie, u groepsbeleid gebruiken om automatische implementatie selectief in te schakelen of uit te schakelen.  Dit groepsbeleid moet worden ingesteld voordat de andere configuratiestappen worden gestart:
* Maak een groepsbeleidsobject in uw Active Directory.
* Noem het (ex- Hybride Azure AD join).
* Bewerken en ga naar: >-beleid voor computerconfiguratie > beheersjablonen > Windows-componenten > apparaatregistratie.

>[!NOTE]
>Voor 2012R2 bevinden de beleidsinstellingen zich bij **Computer Configuration > Policies >-beheersjablonen > Windows Components > Workplace Join > Automatisch werken bij clientcomputers**

* Schakel deze instelling in: Computers met domeinverbonden computers registreren als apparaten.
* Toepassen en klik op OK.
* Koppel de GPO aan de locatie van uw keuze (organisatie-eenheid, beveiligingsgroep of aan het domein voor alle apparaten).

## <a name="2-configure-network-with-device-registration-endpoints"></a>2. Netwerk configureren met apparaatregistratieeindpunten
Controleer of de volgende URL's toegankelijk zijn vanaf computers in uw organisatienetwerk voor registratie naar Azure AD:

* `https://enterpriseregistration.windows.net`
* `https://login.microsoftonline.com`
* `https://device.login.microsoftonline.com` 

## <a name="3-implement-wpad-for-windows-10-devices"></a>3. WPAD implementeren voor Windows 10-apparaten
Als uw organisatie toegang heeft tot internet via een uitgaande proxy, implementeert u Web Proxy Auto-Discovery (WPAD) zodat Windows 10-computers zich kunnen registreren bij Azure AD.

## <a name="4-configure-the-scp-in-any-forests-that-were-not-configured-by-azure-ad-connect"></a>4. Configureer het SCP in forests die niet zijn geconfigureerd door Azure AD Connect 

Het serviceverbindingspunt (SCP) bevat uw Azure AD-tenantgegevens die door uw apparaten worden gebruikt voor automatische registratie.  Voer het PowerShell-script, ConfigureSCP.ps1, uit dat u hebt gedownload van Azure AD Connect.

## <a name="5-configure-any-federation-service-that-was-not-configured-by-azure-ad-connect"></a>5. Configureer elke federatieservice die niet is geconfigureerd door Azure AD Connect

Als uw organisatie een federatieservice gebruikt om zich aan te melden bij Azure AD, moeten de claimregels in uw azure AD-vertrouwensrelatie met basispartijen apparaatverificatie toestaan. Als u federatie met AD FS gebruikt, gaat u naar [AD FS Help](https://aka.ms/aadrptclaimrules) om de claimregels te genereren. Als u een niet-Microsoft-federatieoplossing gebruikt, neemt u contact op met die provider voor advies.  

>[!NOTE]
>Als u apparaten op windows-niveau hebt, moet de service ondersteuning bieden voor het uitgeven van de verificatiemethode en wiaormultiauthn-claims bij het ontvangen van aanvragen voor de Azure AD-vertrouwensrelatie. In AD FS moet u een regel voor uitgiftetransformatie toevoegen die door de verificatiemethode gaat.

## <a name="6-enable-azure-ad-seamless-sso-for-windows-down-level-devices"></a>6. Azure AD Seamless SSO inschakelen voor Windows-apparaten op downniveau

Als uw organisatie wachtwoordhashsynchronisatie of pass-through-verificatie gebruikt om zich aan te melden bij Azure AD, schakelt u https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-ssoAzure AD Seamless SSO in met die aanmeldingsmethode om apparaten op downniveau van Windows te verifiëren: . 

## <a name="7-set-azure-ad-policy-for-windows-down-level-devices"></a>7. Azure AD-beleid instellen voor apparaten op downniveau van Windows

Als u apparaten op downniveau van Windows wilt registreren, moet u ervoor zorgen dat gebruikers in het Azure AD-beleid apparaten kunnen registreren. 

* Log in bij uw account in de Azure-portal.
* Ga naar: Instellingen voor Azure Active Directory > devices >-apparaat
* Stel 'Gebruikers kunnen hun apparaten registreren bij Azure AD' in op ALL.
* Op Opslaan klikken

## <a name="8-add-azure-ad-endpoint-to-windows-down-level-devices"></a>8. Azure AD-eindpunt toevoegen aan apparaten op downniveau van Windows

Voeg het eindpunt azure AD-apparaatverificatie toe aan de lokale intranetzones op uw Windows-apparaten op downniveau om certificaatprompts te voorkomen bij het verifiëren van de apparaten:`https://device.login.microsoftonline.com` 

Als u [Seamless SSO](how-to-connect-sso.md)gebruikt, schakelt u ook 'Statusbalkupdates toestaan via script' in op die zone en voegt u het volgende eindpunt toe:`https://autologon.microsoftazuread-sso.com` 

## <a name="9-install-microsoft-workplace-join-on-windows-down-level-devices"></a>9. Microsoft Workplace Join installeren op apparaten op windows-niveau

Met dit installatieprogramma wordt een geplande taak gemaakt op het apparaatsysteem dat in de context van de gebruiker wordt uitgevoerd. De taak wordt geactiveerd wanneer de gebruiker zich aanmeldt bij Windows. De taak voegt het apparaat in stilte samen met Azure AD met de gebruikersreferenties na verificatie met geïntegreerde Windows-verificatie. Het downloadcentrum https://www.microsoft.com/download/details.aspx?id=53554bevindt zich op . 

## <a name="10-configure-group-policy-to-allow-device-registration"></a>10. Groepsbeleid configureren om apparaatregistratie toe te staan

* Maak een groepsbeleidsobject in uw Active Directory, als dit nog niet is gemaakt.
* Noem het (ex- Hybride Azure AD join).
* & naar: Computerconfiguratie >-beleid > beheersjablonen > Windows-onderdelen > apparaatregistratie te bewerken
* Inschakelen: computers met domeinverbonden computers registreren als apparaten
* Toepassen en klik op OK.
* Koppel de GPO aan de locatie van uw keuze (organisatie-eenheid, beveiligingsgroep of aan het domein voor alle apparaten).

>[!NOTE]
>Voor 2012R2 bevinden de beleidsinstellingen zich bij **Computer Configuration > Policies >-beheersjablonen > Windows Components > Workplace Join > Automatisch werken bij clientcomputers**

## <a name="next-steps"></a>Volgende stappen
[Apparaatterugschrijven configureren](how-to-connect-device-writeback.md)
