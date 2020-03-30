---
title: Directe federatie instellen met een AD FS voor B2B - Azure AD
description: Meer informatie over het instellen van AD FS als identiteitsprovider voor directe federatie, zodat gasten zich kunnen aanmelden bij uw Azure AD-apps
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e350d6338b6ca589ab18d068ef6a314363fe205c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74272830"
---
# <a name="example-direct-federation-with-active-directory-federation-services-ad-fs-preview"></a>Voorbeeld: Directe federatie met Active Directory Federation Services (AD FS) (voorbeeld)
|     |
| --- |
| Directe federatie is een openbare preview-functie van Azure Active Directory. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie over voorvertoningen.|
|     |

In dit artikel wordt beschreven hoe [u directe federatie](direct-federation.md) instelt met Active Directory Federation Services (AD FS) als een SAML 2.0- of WS-Fed-identiteitsprovider. Om directe federatie te ondersteunen, moeten bepaalde kenmerken en claims worden geconfigureerd bij de identiteitsprovider. Als u wilt illustreren hoe u een identiteitsprovider voor directe federatie configureert, gebruiken we Bijvoorbeeld Active Directory Federation Services (AD FS). We laten zien hoe je AD FS instelt, zowel als SAML-identiteitsprovider als als ws-fed-identiteitsprovider.

> [!NOTE]
> In dit artikel wordt beschreven hoe u AD FS instelt voor zowel SAML als WS-Fed voor illustratiedoeleinden. Voor directe federatie-integraties waarbij de identiteitsprovider AD FS is, raden we aan WS-Fed als protocol te gebruiken. 

## <a name="configure-ad-fs-for-saml-20-direct-federation"></a>AD FS configureren voor SAML 2.0 directe federatie
Azure AD B2B kan worden geconfigureerd om te reageren op identiteitsproviders die het SAML-protocol gebruiken met specifieke vereisten die hieronder worden vermeld. Om de SAML-configuratiestappen te illustreren, ziet u in deze sectie hoe u AD FS instelt voor SAML 2.0. 

Als u een directe federatie wilt instellen, moeten de volgende kenmerken worden ontvangen in het SAML 2.0-antwoord van de identiteitsprovider. Deze kenmerken kunnen worden geconfigureerd door te linken naar het XML-bestand voor online beveiligingstokenservices of door ze handmatig in te voeren. Stap 12 in [Een test AD FS-exemplaar maken](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed) beschrijft bijvoorbeeld `https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml`hoe u de AD FS-eindpunten vinden of hoe u de URL van uw metagegevens genereren. 

|Kenmerk  |Waarde  |
|---------|---------|
|BeweringConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|Doelgroep     |`urn:federation:MicrosoftOnline`         |
|Verlener     |De uitgever URI van de partner IdP, bijvoorbeeld`http://www.example.com/exk10l6w90DHM0yi...`         |

De volgende claims moeten worden geconfigureerd in het SAML 2.0-token dat is uitgegeven door de identiteitsprovider:


|Kenmerk  |Waarde  |
|---------|---------|
|NameID-indeling     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |


In de volgende sectie wordt uitgelegd hoe u de vereiste kenmerken en claims configureert met AD FS als voorbeeld van een SAML 2.0-identiteitsprovider.

### <a name="before-you-begin"></a>Voordat u begint

Een AD FS-server moet al zijn ingesteld en functioneren voordat u met deze procedure begint. Zie Een voorbeeld van een test [AD FS 3.0 maken op een virtuele Azure-server voor](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed)hulp bij het instellen van een AD FS-server.

### <a name="add-the-claim-description"></a>De claimbeschrijving toevoegen

1. Selecteer op uw AD FS-server Het**AD FS-beheer**van **gereedschappen** > .
2. **Selecteer** > **serviceclaimbeschrijvingen**in het navigatiedeelvenster .
3. Selecteer **onder Acties**de optie **Claimbeschrijving toevoegen**.
4. Geef in het venster **Een claimbeschrijving toevoegen** de volgende waarden op:

   - **Weergavenaam:** permanente id
   - **Claim-id:**`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent` 
   - Schakel het selectievakje in voor **Deze claimbeschrijving publiceren in federatiemetagegevens in als claimtype dat deze federatieservice kan accepteren.**
   - Schakel het selectievakje in voor **Deze claimbeschrijving publiceren in federatiemetagegevens in als claimtype dat deze federatieservice kan verzenden.**

5. Klik op **Ok**.

### <a name="add-the-relying-party-trust-and-claim-rules"></a>De vertrouwens- en claimregels van de relying party toevoegen

1. Ga op de AD FS-server naar **Ad** > **FS-beheer .**
2. Selecteer **vertrouwensrelaties vertrouwen** > **in de vertrouwensrelaties**in het navigatiedeelvenster .
3. Selecteer **Onder Acties**de optie **Vertrouwensrelatie relying party toevoegen**. 
4. Gebruik in de wizard Vertrouwensrelatie toevoegen voor **Gegevensbron selecteren**de optie **Gegevens importeren over de relying party die online of op een lokaal netwerk is gepubliceerd.** Geef deze url https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xmlvan de federatie-metagegevens op. Laat andere standaardselecties achter. Selecteer **Sluiten**.
5. De wizard **Claimregels bewerken** wordt geopend.
6. Selecteer **Regel toevoegen**in de wizard **Claimregels bewerken** . Selecteer **in Regeltype kiezen**de optie **LDAP-kenmerken verzenden als claims**. Selecteer **Volgende**.
7. Geef in **Claimregel configureren**de volgende waarden op: 

   - **Naam claimregel**: Regel e-mailclaim 
   - **Kenmerkarchief:** Active Directory 
   - **LDAP-kenmerk**: E-mailadressen 
   - **Type uitgaande claim**: E-mailadres

8. Selecteer **Finish**.
9. In het venster **Claimregels bewerken** wordt de nieuwe regel weergegeven. Klik op **Toepassen**. 
10. Klik op **Ok**.  

### <a name="create-an-email-transform-rule"></a>Een regel voor e-mailtransformatie maken
1. Ga naar **Claimregels bewerken** en klik op **Regel toevoegen**. Selecteer **in Regeltype kiezen**de optie Een **binnenkomende claim transformeren** en klik op **Volgende**. 
2. Geef in **Claimregel configureren**de volgende waarden op: 

   - **Naam van claimregel:** regel voor e-mailtransformatie 
   - **Binnenkomend claimtype**: E-mailadres 
   - **Type uitgaande claim**: naam-id 
   - **Indeling uitgaande naam-id:** permanente id 
   - Selecteer **Alle claimwaarden doorgeven**.

3. Klik op **Voltooien**. 
4. In het venster **Claimregels bewerken** worden de nieuwe regels weergegeven. Klik op **Toepassen**. 
5. Klik op **OK**. De AD FS-server is nu geconfigureerd voor directe federatie met behulp van het SAML 2.0-protocol.

## <a name="configure-ad-fs-for-ws-fed-direct-federation"></a>AD FS configureren voor directe federatie van WS-Fed 
Azure AD B2B kan worden geconfigureerd om te reageren op identiteitsproviders die het WS-Fed-protocol gebruiken met de specifieke vereisten die hieronder worden vermeld. Momenteel zijn de twee WS-Fed-providers getest op compatibiliteit met Azure AD, waaronder AD FS en Shibboleth. Hier gebruiken we Active Directory Federation Services (AD FS) als voorbeeld van de WS-Fed-identiteitsprovider. Download de compatibiliteitsdocumenten van de Azure AD-identiteitsprovider voor meer informatie over het instellen van een vertrouwensrelatie tussen een WS-Fed-compatibele provider met Azure AD.

Als u een directe federatie wilt instellen, moeten de volgende kenmerken worden ontvangen in het WS-Fed-bericht van de identiteitsprovider. Deze kenmerken kunnen worden geconfigureerd door te linken naar het XML-bestand voor online beveiligingstokenservices of door ze handmatig in te voeren. Stap 12 in [Een test AD FS-exemplaar maken](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed) beschrijft bijvoorbeeld `https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml`hoe u de AD FS-eindpunten vinden of hoe u de URL van uw metagegevens genereren.
 
|Kenmerk  |Waarde  |
|---------|---------|
|PassiefRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|Doelgroep     |`urn:federation:MicrosoftOnline`         |
|Verlener     |De uitgever URI van de partner IdP, bijvoorbeeld`http://www.example.com/exk10l6w90DHM0yi...`         |

Vereiste claims voor het WS-Fed-token uitgegeven door de IdP:

|Kenmerk  |Waarde  |
|---------|---------|
|Onveranderlijke ID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

In de volgende sectie wordt uitgelegd hoe u de vereiste kenmerken en claims configureert met AD FS als voorbeeld van een WS-Fed-identiteitsprovider.

### <a name="before-you-begin"></a>Voordat u begint
Een AD FS-server moet al zijn ingesteld en functioneren voordat u met deze procedure begint. Zie Een voorbeeld van een test [AD FS 3.0 maken op een virtuele Azure-server voor](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed)hulp bij het instellen van een AD FS-server.


### <a name="add-the-relying-party-trust-and-claim-rules"></a>De vertrouwens- en claimregels van de relying party toevoegen 
1. Ga op de AD FS-server naar **Ad** > **FS-beheer .** 
1. Selecteer **vertrouwensrelaties vertrouwen** > **in de vertrouwensrelaties**in het navigatiedeelvenster . 
1. Selecteer **Onder Acties**de optie **Vertrouwensrelatie relying party toevoegen**.  
1. Gebruik in de wizard Vertrouwensrelatie toevoegen van relying party voor **Gegevensbron selecteren**de optie **Gegevens importeren over de relying party die online of op een lokaal netwerk is gepubliceerd.** Geef deze URL `https://nexus.microsoftonline-p.com/federationmetadata/2007-06/federationmetadata.xml`van federatiemetagegevens op: .  Laat andere standaardselecties achter. Selecteer **Sluiten**.
1. De wizard **Claimregels bewerken** wordt geopend. 
1. Selecteer **Regel toevoegen**in de wizard **Claimregels bewerken** . Selecteer **In Regeltype kiezen**de optie Claims verzenden met een aangepaste **regel**. Selecteer *Volgende*. 
1. Geef in **Claimregel configureren**de volgende waarden op:

   - **Naam claimregel**: Onveranderlijke id afgeven  
   - **Aangepaste regel:**`c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"] => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"), query = "samAccountName={0};objectGUID;{1}", param = regexreplace(c.Value, "(?<domain>[^\\]+)\\(?<user>.+)", "${user}"), param = c.Value);`

1. Selecteer **Finish**. 
1. In het venster **Claimregels bewerken** wordt de nieuwe regel weergegeven. Klik op **Toepassen**.  
1. Selecteer **Regel toevoegen**in dezelfde wizard **Claimregels bewerken** . Selecteer **LDAP-kenmerken verzenden als claims**in **Cohose Rule Type**. Selecteer **Volgende**.
1. Geef in **Claimregel configureren**de volgende waarden op: 

   - **Naam claimregel**: Regel e-mailclaim  
   - **Kenmerkarchief:** Active Directory  
   - **LDAP-kenmerk**: E-mailadressen  
   - **Type uitgaande claim**: E-mailadres 

1.  Selecteer **Finish**. 
1.  In het venster **Claimregels bewerken** wordt de nieuwe regel weergegeven. Klik op **Toepassen**.  
1.  Klik op **OK**. De AD FS-server is nu geconfigureerd voor directe federatie met Behulp van WS-Fed.

## <a name="next-steps"></a>Volgende stappen
Vervolgens [configureert](direct-federation.md#step-2-configure-direct-federation-in-azure-ad) u directe federatie in Azure AD in de Azure AD-portal of met PowerShell. 
