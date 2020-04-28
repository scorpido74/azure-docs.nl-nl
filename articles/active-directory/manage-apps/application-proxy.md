---
title: Externe toegang tot on-premises apps-Azure AD-toepassingsproxy
description: De toepassings proxy van Azure Active Directory biedt veilige externe toegang tot on-premises webtoepassingen. Na een eenmalige aanmelding bij Azure AD, hebben gebruikers toegang tot zowel Cloud-als on-premises toepassingen via een externe URL of een interne toepassings Portal. Toepassings proxy kan bijvoorbeeld externe toegang en eenmalige aanmelding bieden voor Extern bureaublad, share point, teams, tableau, Qlik en LOB-toepassingen (line-of-Business).
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4780786f0caea2c211b6b93fb0736feaade8de80
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74274837"
---
# <a name="remote-access-to-on-premises-applications-through-azure-active-directorys-application-proxy"></a>Externe toegang tot on-premises toepassingen via de toepassings proxy van Azure Active Directory 

De toepassings proxy van Azure Active Directory biedt veilige externe toegang tot on-premises webtoepassingen. Na een eenmalige aanmelding bij Azure AD, hebben gebruikers toegang tot zowel Cloud-als on-premises toepassingen via een externe URL of een interne toepassings Portal. Toepassings proxy kan bijvoorbeeld externe toegang en eenmalige aanmelding bieden voor Extern bureaublad, share point, teams, tableau, Qlik en LOB-toepassingen (line-of-Business).

Azure AD-toepassingsproxy is:

- **Eenvoudig te gebruiken**. Gebruikers hebben toegang tot uw on-premises toepassingen op dezelfde manier als de toegang tot O365 en andere SaaS-apps die zijn geïntegreerd met Azure AD. U hoeft uw toepassing niet te wijzigen of bij te werken om te kunnen werken met een toepassingsproxy. 

- **Beveiligd**. On-premises toepassingen kunnen gebruikmaken van de autorisatie-instellingen en beveiligings analyses van Azure. On-premises toepassingen kunnen bijvoorbeeld gebruikmaken van voorwaardelijke toegang en verificatie in twee stappen. Voor toepassings proxy is het niet nodig om binnenkomende verbindingen via uw firewall te openen.
 
- **Rendabel**. Voor on-premises oplossingen moet u doorgaans gedemilitariseerde zones (Dmz's), edge-servers of andere complexe infra structuren instellen en onderhouden. Toepassings proxy wordt uitgevoerd in de Cloud, waardoor u deze eenvoudig kunt gebruiken. Als u toepassings proxy wilt gebruiken, hoeft u de netwerk infrastructuur niet te wijzigen of extra apparaten te installeren in uw on-premises omgeving.

## <a name="what-is-application-proxy"></a>Wat is toepassingsproxy?
Toepassings proxy is een functie van Azure AD waarmee gebruikers toegang kunnen krijgen tot on-premises webtoepassingen vanaf een externe client. Toepassings proxy bevat zowel de Application proxy-service die wordt uitgevoerd in de Cloud als de connector van de toepassings proxy die wordt uitgevoerd op een on-premises server. Azure AD, de service toepassings proxy en de Application proxy-connector werken samen om het aanmeldings token van de gebruiker van Azure AD aan de webtoepassing veilig door te geven.

Toepassings proxy werkt met:

* Webtoepassingen die gebruikmaken van [geïntegreerde Windows-authenticatie](application-proxy-configure-single-sign-on-with-kcd.md) voor verificatie  
* Webtoepassingen die gebruikmaken van op formulieren of [koptekst gebaseerde](application-proxy-configure-single-sign-on-with-ping-access.md) toegang  
* Web-Api's die u beschikbaar wilt maken voor uitgebreide toepassingen op verschillende apparaten  
* Toepassingen die worden gehost achter een [extern bureaublad-gateway](application-proxy-integrate-with-remote-desktop-services.md)  
* Uitgebreide client-apps die zijn geïntegreerd met de Active Directory Authentication Library (ADAL)

Toepassings proxy biedt ondersteuning voor eenmalige aanmelding. Zie voor meer informatie over ondersteunde methoden [één aanmeldings methode kiezen](what-is-single-sign-on.md#choosing-a-single-sign-on-method).

Toepassings proxy wordt aanbevolen om externe gebruikers toegang te geven tot interne bronnen. Toepassings proxy vervangt de nood zaak van een VPN-of reverse-proxy. Het is niet bedoeld voor interne gebruikers in het bedrijfs netwerk.  Deze gebruikers die onnodig toepassings proxy gebruiken, kunnen onverwachte en ongewenste prestatie problemen veroorzaken.

## <a name="how-application-proxy-works"></a>Hoe toepassings proxy werkt

In het volgende diagram ziet u hoe Azure AD en toepassings proxy samen werken om eenmalige aanmelding te bieden voor on-premises toepassingen.

![AzureAD toepassings proxy diagram](./media/application-proxy/azureappproxxy.png)

1. Nadat de gebruiker toegang tot de toepassing heeft verkregen via een eind punt, wordt de gebruiker omgeleid naar de aanmeldings pagina van Azure AD. 
2. Na een geslaagde aanmelding stuurt Azure AD een token naar het client apparaat van de gebruiker.
3. De client stuurt het token naar de Application proxy-service, waarmee de user principal name (UPN) en de SPN (Security Principal Name) van het token worden opgehaald. Toepassings proxy verzendt de aanvraag vervolgens naar de connector van de toepassings proxy.
4. Als u eenmalige aanmelding hebt geconfigureerd, voert de connector extra authenticatie uit namens de gebruiker.
5. De connector stuurt de aanvraag naar de on-premises toepassing.  
6. Het antwoord wordt via de connector en de Application proxy-service naar de gebruiker verzonden.

| Onderdeel | Beschrijving |
| --------- | ----------- |
| Eindpunt  | Het eind punt is een URL of [eind gebruikers Portal](end-user-experiences.md). Gebruikers kunnen toepassingen en buiten uw netwerk bereiken door toegang te krijgen tot een externe URL. Gebruikers in uw netwerk hebben toegang tot de toepassing via een URL of een portal voor eind gebruikers. Wanneer gebruikers naar een van deze eind punten gaan, verifiëren ze in azure AD en sturen ze vervolgens via de connector naar de on-premises toepassing.|
| Azure AD | Azure AD voert de verificatie uit met behulp van de Tenant Directory die is opgeslagen in de Cloud. |
| Application proxy-service | Deze toepassings proxy service wordt uitgevoerd in de Cloud als onderdeel van Azure AD. Hiermee wordt het aanmeldings token van de gebruiker aan de Application proxy-connector door gegeven. Met de toepassings proxy worden alle toegankelijke headers voor de aanvraag doorgestuurd en worden de headers volgens het bijbehorende protocol ingesteld op het IP-adres van de client. Als de inkomende aanvraag voor de proxy al die header heeft, wordt het client-IP-adres toegevoegd aan het einde van de door komma's gescheiden lijst die de waarde van de koptekst is.|
| Application proxy-connector | De connector is een licht gewicht agent die wordt uitgevoerd op een Windows-Server in uw netwerk. De connector beheert de communicatie tussen de service toepassings proxy in de Cloud en de on-premises toepassing. De connector gebruikt alleen uitgaande verbindingen. u hoeft geen binnenkomende poorten te openen of niets in de DMZ te plaatsen. De connectors zijn stateless en halen indien nodig gegevens uit de Cloud. Zie [Azure AD-toepassingsproxy-connectors begrijpen](application-proxy-connectors.md)voor meer informatie over connectors, zoals hoe ze worden geladen en geverifieerd.|
| Active Directory (AD) | Active Directory on-premises wordt uitgevoerd om verificatie voor domein accounts uit te voeren. Wanneer eenmalige aanmelding is geconfigureerd, communiceert de connector met AD voor het uitvoeren van aanvullende verificatie vereist.
| On-premises toepassing | Ten slotte heeft de gebruiker toegang tot een on-premises toepassing. 

## <a name="next-steps"></a>Volgende stappen
Zie [zelf studie: een on-premises toepassing toevoegen voor externe toegang via toepassings proxy](application-proxy-add-on-premises-application.md)om toepassings proxy te gaan gebruiken. 

Zie het [blog toepassings proxy](https://blogs.technet.com/b/applicationproxyblog/) voor het laatste nieuws en de meest recente updates


