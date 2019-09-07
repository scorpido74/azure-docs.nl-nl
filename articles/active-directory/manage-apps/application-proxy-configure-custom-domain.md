---
title: Aangepaste domeinen in Azure AD-toepassingsproxy | Microsoft Docs
description: Aangepaste domeinen in Azure AD-toepassingsproxy beheren zodat de URL voor de app hetzelfde is, ongeacht waar uw gebruikers toegang toe.
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
ms.date: 01/31/2018
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a1914b7cf79287831e0e94c19c50107c2ac216d
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390780"
---
# <a name="working-with-custom-domains-in-azure-ad-application-proxy"></a>Werken met aangepaste domeinen in Azure AD-toepassingsproxy

Wanneer u een toepassing via Azure Active Directory Application Proxy publiceert, maakt u een externe URL voor uw gebruikers kunnen raadplegen wanneer ze extern werken. Deze URL wordt het standaarddomein *yourtenant.msappproxy.net*. Bijvoorbeeld, als u gepubliceerd een app met de naam van de kosten en uw tenant is met de naam Contoso, bedragen de externe URL `https://expenses-contoso.msappproxy.net`. Als u uw eigen domeinnaam gebruiken wilt, moet u een aangepast domein voor uw toepassing configureren. 

U wordt aangeraden dat instellen van aangepaste domeinen voor uw toepassingen zo veel mogelijk. De voordelen van aangepaste domeinen zijn onder andere:

- Uw gebruikers toegang krijgen tot de toepassing met dezelfde URL, of ze binnen of buiten uw netwerk werken.
- Als al uw toepassingen dezelfde interne en externe URL's, hebben koppelingen in een toepassing die naar een andere verwijzen er nog steeds werken, zelfs buiten het bedrijfsnetwerk. 
- U bepaalt uw huisstijl en de URL's die u wilt maken. 


## <a name="configure-a-custom-domain"></a>Een aangepast domein configureren

### <a name="prerequisites"></a>Vereisten

Voordat u een aangepast domein configureren, zorg ervoor dat u de volgende vereisten voorbereid hebt: 
- Een [geverifieerd domein is toegevoegd aan Azure Active Directory](../fundamentals/add-custom-domain.md).
- Een aangepaste certificaat voor het domein, in de vorm van een PFX-bestand.
- Een on-premises app [gepubliceerd via toepassingsproxy](application-proxy-add-on-premises-application.md).

### <a name="configure-your-custom-domain"></a>Uw aangepaste domein configureren

Wanneer u deze drie vereisten klaar hebt, volgt u deze stappen voor het instellen van uw aangepaste domein:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Navigeer naar **Azure Active Directory** > **bedrijfstoepassingen** > **alle toepassingen** en kies de app die u wilt beheren.
3. Selecteer **toepassingsproxy**. 
4. Gebruik de vervolgkeuzelijst om uw aangepaste domein in het veld externe URL. Als u uw domein in de lijst niet ziet, nog niet klikt u vervolgens deze is geverifieerd nog. 
5. Selecteer **opslaan**
5. De **certificaat** veld dat is uitgeschakeld wordt ingeschakeld. Dit veld selecteert. 

   ![Klik hier om een certificaat te uploaden](./media/application-proxy-configure-custom-domain/certificate.png)

   Als u al een certificaat voor dit domein hebt geüpload, geeft de certificaatinformatie weer in het veld van het certificaat. 

6. Het PFX-certificaat uploaden en voer het wachtwoord voor het certificaat. 
7. Selecteer **opslaan** uw wijzigingen op te slaan. 
8. Voeg een [DNS-record](../../dns/dns-operations-recordsets-portal.md) die de nieuwe externe URL wordt omgeleid naar het domein msappproxy.net.
9. Controleer of de DNS-record correct is geconfigureerd met behulp van de [nslookup](https://social.technet.microsoft.com/wiki/contents/articles/29184.nslookup-for-beginners.aspx) opdracht om te zien of uw externe URL bereikbaar is en of het domein msapproxy.net wordt weer gegeven als een alias.

>[!TIP] 
>U moet slechts één certificaat per aangepast domein uploaden. Wanneer u een certificaat hebt geüpload, kunt u het aangepaste domein kiezen wanneer u een nieuwe app publiceren en geen extra configuratie, met uitzondering van de DNS-record. 

## <a name="manage-certificates"></a>Certificaten beheren

### <a name="certificate-format"></a>De indeling van certificaat
Er is geen beperking voor het certificaat handtekening-methoden. Alle worden Elliptic Curve Cryptography (ECC), SAN Subject Alternative Name () en andere algemene typen certificaten ondersteund. 

U kunt een certificaat met jokertekens gebruiken, zolang het jokerteken komt overeen met de gewenste externe URL.

Het certificaat moet de persoonlijke sleutel bevatten.

Certificaten die zijn uitgegeven door uw eigen open bare-sleutel infrastructuur (PKI) kunnen worden gebruikt als de certificaat keten is geïnstalleerd op uw client apparaten. InTune kan worden gebruikt om deze certificaten te implementeren op beheerde apparaten. Voor niet-beheerde apparaten moeten deze certificaten hand matig worden geïnstalleerd.

### <a name="changing-the-domain"></a>Wijzigen van het domein
Alle geverifieerde domeinen worden weergegeven in de vervolgkeuzelijst voor de externe URL voor uw toepassing. Als u wilt wijzigen van het domein, moet u alleen dat veld voor de toepassing bijwerken. Als het domein dat u wilt dat zich niet in de lijst [toe te voegen als een geverifieerd domein](../fundamentals/add-custom-domain.md). Als u een domein dat niet beschikken over een bijbehorende certificaat nog, volgt u stap 5-7 om toe te voegen van het certificaat selecteren. Controleer vervolgens of dat u de DNS-record om te leiden van de nieuwe externe URL bijwerken. 

### <a name="certificate-management"></a>Certificaatbeheer
U kunt hetzelfde certificaat gebruiken voor meerdere toepassingen, tenzij de toepassingen die een externe host delen. 

U ontvangt een waarschuwing wanneer een certificaat is verlopen, waarin u een ander certificaat via de portal uploaden. Als het certificaat is ingetrokken, kunnen uw gebruikers een beveiligingswaarschuwing zien bij het openen van de toepassing. We uitvoeren geen intrekkingscontroles voor certificaten.  Voor het bijwerken van het certificaat voor een bepaalde toepassing, gaat u naar de toepassing en stappen 5 tot en met 7 om aangepaste domeinen voor gepubliceerde toepassingen voor het uploaden van een nieuw certificaat te configureren. Als het oude certificaat niet wordt gebruikt door andere toepassingen, wordt deze automatisch verwijderd. 

Alle Certificaatbeheer is momenteel door afzonderlijke toepassing's daarom voor het beheren van certificaten in de context van de desbetreffende toepassingen. 

## <a name="next-steps"></a>Volgende stappen
* [Eenmalige aanmelding inschakelen](application-proxy-configure-single-sign-on-with-kcd.md) naar uw gepubliceerde apps met Azure AD-verificatie.
* [Schakel voorwaardelijke toegang](https://docs.microsoft.com/en-us/azure/active-directory/conditional-access/technical-reference#cloud-apps-assignments) tot uw gepubliceerde apps in.
* [Uw aangepaste domeinnaam toevoegen aan Azure AD](../fundamentals/add-custom-domain.md)


