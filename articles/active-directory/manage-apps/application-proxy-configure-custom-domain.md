---
title: Aangepaste domeinen in Azure AD-toepassingsproxy | Microsoft Documenten
description: Aangepaste domeinen configureren en beheren in Azure AD-toepassingsproxy.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f35658a75adb4d4c6c279e45087e741b8117e65
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481378"
---
# <a name="configure-custom-domains-with-azure-ad-application-proxy"></a>Aangepaste domeinen configureren met Azure AD-toepassingsproxy

Wanneer u een toepassing publiceert via Azure Active Directory Application Proxy, maakt u een externe URL voor uw gebruikers. Deze URL krijgt het standaarddomein *yourtenant.msappproxy.net.* Als u bijvoorbeeld een app met de naam *Onkosten* publiceert in uw tenant met de naam *Contoso,* is de externe URL *https:\//expenses-contoso.msappproxy.net*. Als u uw eigen domeinnaam wilt gebruiken in plaats van *msappproxy.net,* u een aangepast domein voor uw toepassing configureren. 

## <a name="benefits-of-custom-domains"></a>Voordelen van aangepaste domeinen

Het is een goed idee om waar mogelijk aangepaste domeinen voor uw apps in te stellen. Enkele redenen om aangepaste domeinen te gebruiken zijn:

- Links tussen apps werken zelfs buiten het bedrijfsnetwerk. Zonder een aangepast domein, als uw app heeft hard-gecodeerde interne links naar doelen buiten de Application Proxy, en de links zijn niet extern oplosbaar, zullen ze breken. Wanneer uw interne en externe URL's hetzelfde zijn, voorkomt u dit probleem. Zie [Hardcoded links omleiden met Azure AD Application Proxy](../application-proxy-link-translation.md) voor andere manieren om dit probleem op te lossen als u geen aangepaste domeinen gebruiken. 
  
- Uw gebruikers hebben een gemakkelijkere ervaring, omdat ze de app met dezelfde URL van binnen of buiten uw netwerk kunnen openen. Ze hoeven geen verschillende interne en externe URL's te leren of hun huidige locatie bij te houden. 

- U uw branding beheren en de gewenste URL's maken. Een aangepast domein kan helpen het vertrouwen van uw gebruikers op te bouwen, omdat gebruikers een vertrouwde naam zien en gebruiken in plaats van *msappproxy.net.*

- Sommige configuraties werken alleen met aangepaste domeinen. U hebt bijvoorbeeld aangepaste domeinen nodig voor apps die SAML (Security Assertion Markup Language) gebruiken, bijvoorbeeld wanneer u Active Directory Federation Services (AD FS) gebruikt, maar ws-federation niet gebruiken. Zie [Werken met apps die op claims zijn in Application Proxy](application-proxy-configure-for-claims-aware-applications.md)voor meer informatie. 

Als u de interne en externe URL's niet laten overeenkomen, is het niet zo belangrijk om aangepaste domeinen te gebruiken, maar u nog steeds profiteren van de andere voordelen. 

## <a name="dns-configuration-options"></a>DNS-configuratieopties

Er zijn verschillende opties voor het instellen van uw DNS-configuratie, afhankelijk van uw vereisten:

### <a name="same-internal-and-external-url-different-internal-and-external-behavior"></a>Dezelfde interne en externe URL, ander intern en extern gedrag 

Als u niet wilt dat uw interne gebruikers worden doorgestuurd via de application proxy, u een *split-brain DNS*instellen. Een gesplitste DNS-infrastructuur stuurt interne hosts naar een interne domeinnaamserver en externe hosts naar een externe domeinnaamserver voor naamomzetting. 

![Split-brain DNS](./media/application-proxy-configure-custom-domain/split-brain-dns.png)

### <a name="different-internal-and-external-urls"></a>Verschillende interne en externe URL's 

Als de interne en externe URL's verschillend zijn, hoeft u split-brain gedrag niet te configureren, omdat de gebruikersroutering wordt bepaald door de URL. In dit geval wijzigt u alleen de externe DNS en wordt de externe URL doorgestoofd naar het eindpunt van de toepassingsproxy. 

Wanneer u een aangepast domein selecteert voor een externe URL, wordt op een informatiebalk de CNAME-vermelding weergegeven die u aan de externe DNS-provider moet toevoegen. U deze informatie altijd zien door naar de **proxypagina van** de app te gaan.

## <a name="set-up-and-use-custom-domains"></a>Aangepaste domeinen instellen en gebruiken

Als u een on-premises app wilt configureren om een aangepast domein te gebruiken, hebt u een geverifieerd Aangepast Azure Directory-domein, een PFX-certificaat voor het aangepaste domein en een on-premises app nodig om te configureren. 

### <a name="create-and-verify-a-custom-domain"></a>Een aangepast domein maken en verifiëren

Ga als volgt te werk om een aangepast domein te maken en te verifiëren:

1. Selecteer in Azure Active Directory **Aangepaste domeinnamen** in de linkernavigatie en selecteer Vervolgens Aangepast **domein toevoegen.** 
1. Voer uw aangepaste domeinnaam in en selecteer **Domein toevoegen**. 
1. Kopieer op de domeinpagina de TXT-recordgegevens voor uw domein. 
1. Ga naar uw domeinregistrar en maak een nieuwe TXT-record voor uw domein op basis van uw gekopieerde DNS-gegevens.
1. Nadat u het domein hebt geregistreerd, selecteert u op de pagina van het domein in Azure Active Directory de optie **Verifiëren**. Zodra de domeinstatus is **geverifieerd,** u het domein gebruiken voor al uw Azure AD-configuraties, inclusief Application Proxy. 

Zie Uw aangepaste [domeinnaam toevoegen met de Azure Active Directory-portal](../fundamentals/add-custom-domain.md)voor meer gedetailleerde instructies.

### <a name="configure-an-app-to-use-a-custom-domain"></a>Een app configureren om een aangepast domein te gebruiken

Ga als lid van het werk om uw app te publiceren via Application Proxy met een aangepast domein:

1. Selecteer **Enterprise-toepassingen** in de linkernavigatie voor een nieuwe app in Azure Active Directory. Selecteer **Nieuwe toepassing**. Selecteer in de sectie **On-premises toepassingen** de optie **Een on-premises toepassing toevoegen**. 
   
   Selecteer deze optie in de lijst en selecteer **Vervolgens De proxy van toepassing** in de linkernavigatie voor een app die al in **Enterprise-toepassingen staat.** 

2. Voer op de pagina Proxy-instellingen voor toepassingen een **naam** in als u uw eigen on-premises toepassing toevoegt.

3.  Voer in het veld **Interne url** de interne URL voor uw app in.
   
4. Laat in het veld **Externe url** de lijst vallen en selecteer het aangepaste domein dat u wilt gebruiken.
   
5. Selecteer **Toevoegen**.
   
   ![Aangepast domein selecteren](./media/application-proxy-configure-custom-domain/application-proxy.png)
   
6. Als het domein al een certificaat heeft, worden in het veld **Certificaat** de certificaatgegevens weergegeven. Selecteer anders het veld **Certificaat.** 
   
   ![Klik hier om een certificaat te uploaden](./media/application-proxy-configure-custom-domain/certificate.png)
   
7. Blader op de pagina **SSL-certificaat** naar en selecteer uw PFX-certificaatbestand. Voer het wachtwoord voor het certificaat in en selecteer **Certificaat uploaden**. Zie de sectie Certificaten [voor aangepaste domeinen voor](#certificates-for-custom-domains) meer informatie over certificaten.
   
   ![Certificaat uploaden](./media/application-proxy-configure-custom-domain/ssl-certificate.png)
   
   > [!TIP] 
   > Een aangepast domein hoeft slechts één keer te worden geüpload. Daarna wordt het geüploade certificaat automatisch toegepast wanneer u het aangepaste domein voor andere apps gebruikt.
   
8. Als u een certificaat hebt toegevoegd, selecteert u op de pagina **Proxy van toepassing** de optie **Opslaan**. 
   
9. Noteer in de informatiebalk op de proxypagina **van toepassing** het CNAME-item dat u aan uw DNS-zone moet toevoegen. 
   
   ![CNAME DNS-vermelding toevoegen](./media/application-proxy-configure-custom-domain/dns-info.png)
   
10. Volg de instructies bij [DNS-records en recordsets beheren met behulp van de Azure-portal](../../dns/dns-operations-recordsets-portal.md) om een DNS-record toe te voegen die de nieuwe externe URL doorverwijst naar het *msappproxy.net-domein.*
   
11. Als u wilt controleren of de DNS-record correct is geconfigureerd, gebruikt u de opdracht [nslookup](https://social.technet.microsoft.com/wiki/contents/articles/29184.nslookup-for-beginners.aspx) om te controleren of uw externe URL bereikbaar is en het *msapproxy.net* domein als alias wordt weergegeven.

Uw toepassing is nu ingesteld om het aangepaste domein te gebruiken. Zorg ervoor dat u gebruikers aan uw toepassing toewijst voordat u deze test of vrijgeeft. 

Als u het domein voor een app wilt wijzigen, selecteert u een ander domein in de vervolgkeuzelijst in Externe URL op de proxypagina van de **app.To** change the domain for a app, select a different domain from the dropdown list in **External URL** on the app's Application proxy page. Upload indien nodig een certificaat voor het bijgewerkte domein en werk de DNS-record bij. Als u het gewenste aangepaste domein niet ziet in de vervolgkeuzelijst in **Externe URL,** wordt het mogelijk niet geverifieerd.

Zie [Zelfstudie: Een on-premises toepassing toevoegen voor externe toegang via Application Proxy in Azure Active Directory](application-proxy-add-on-premises-application.md)voor meer gedetailleerde instructies voor toepassingsproxy.

## <a name="certificates-for-custom-domains"></a>Certificaten voor aangepaste domeinen

Een certificaat maakt de beveiligde TLS-verbinding voor uw aangepaste domein. 

### <a name="certificate-formats"></a>Certificaatindelingen

U moet een PFX-certificaat gebruiken om ervoor te zorgen dat alle vereiste tussenliggende certificaten zijn opgenomen. Het certificaat moet de privésleutel bevatten.

Er is geen beperking op de methoden voor het handtekening van certificaten. Elliptic Curve Cryptography (ECC), Subject Alternative Name (SAN) en andere veelvoorkomende certificaattypen worden ondersteund. 

U wildcardcertificaten gebruiken zolang de joker overeenkomt met de externe URL. U moet wildcardcertificaten gebruiken voor [wildcardtoepassingen](application-proxy-wildcard.md). Als u het certificaat wilt gebruiken om ook toegang te krijgen tot subdomeinen, moet u de subdomeinwildcards toevoegen als alternatieve namen voor onderwerpen in hetzelfde certificaat. Een certificaat voor * \*.adventure-works.com* werkt bijvoorbeeld niet voor * \*.apps.adventure-works.com,* tenzij u * \*.apps.adventure-works.com* toevoegt als alternatieve naam voor een onderwerp. 

U certificaten gebruiken die zijn uitgegeven door uw eigen openbare sleutelinfrastructuur (PKI) als de certificaatketen is geïnstalleerd op uw clientapparaten. Intune kan deze certificaten implementeren op beheerde apparaten. Voor niet-beheerde apparaten moet u deze certificaten handmatig installeren. 

We raden het gebruik van een private root CA af, omdat de private root CA ook naar clientmachines moet worden geduwd, wat veel uitdagingen kan opleveren.

### <a name="certificate-management"></a>Certificaatbeheer

Alle certificaatbeheer vindt plaats via de afzonderlijke toepassingspagina's. Ga naar de proxypagina **van** de toepassing om toegang te krijgen tot het veld **Certificaat.**

U hetzelfde certificaat voor meerdere toepassingen gebruiken. Als een geüpload certificaat werkt met een andere toepassing, wordt het automatisch toegepast. U wordt niet gevraagd deze opnieuw te uploaden wanneer u de app toevoegt of configureert. 

Wanneer een certificaat verloopt, krijgt u een waarschuwing waarin staat dat u een ander certificaat moet uploaden. Als het certificaat wordt ingetrokken, kunnen uw gebruikers een beveiligingswaarschuwing te zien krijgen wanneer ze de app openen. Als u het certificaat voor een app wilt bijwerken, navigeert u naar de pagina **Toepassingsproxy** voor de app, selecteert u **Certificaat**en uploadt u een nieuw certificaat. Als het oude certificaat niet door andere apps wordt gebruikt, wordt het automatisch verwijderd. 

## <a name="next-steps"></a>Volgende stappen
* [Schakel één aanmelding in](application-proxy-configure-single-sign-on-with-kcd.md) voor uw gepubliceerde apps met Azure AD-verificatie.
* [Voorwaardelijke toegang tot](../conditional-access/overview.md) uw gepubliceerde apps inschakelen.

