---
title: Externe toegang tot on-premises apps - Azure AD-toepassingsproxy
description: De toepassingsproxy van Azure Active Directory biedt veilige externe toegang tot on-premises webtoepassingen. Na één aanmelding bij Azure AD hebben gebruikers toegang tot zowel cloud- als on-premises toepassingen via een externe URL of een interne toepassingsportal. Application Proxy kan bijvoorbeeld externe toegang en éénaanmelding bieden voor Remote Desktop, SharePoint, Teams, Tableau, Qlik en LOB-toepassingen (Line of Business).
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74274837"
---
# <a name="remote-access-to-on-premises-applications-through-azure-active-directorys-application-proxy"></a>Externe toegang tot on-premises toepassingen via de toepassingsproxy van Azure Active Directory 

De toepassingsproxy van Azure Active Directory biedt veilige externe toegang tot on-premises webtoepassingen. Na één aanmelding bij Azure AD hebben gebruikers toegang tot zowel cloud- als on-premises toepassingen via een externe URL of een interne toepassingsportal. Application Proxy kan bijvoorbeeld externe toegang en éénaanmelding bieden voor Remote Desktop, SharePoint, Teams, Tableau, Qlik en LOB-toepassingen (Line of Business).

Azure AD-toepassingsproxy is:

- **Eenvoudig te gebruiken.** Gebruikers hebben toegang tot uw on-premises toepassingen op dezelfde manier als ze toegang hebben tot O365 en andere SaaS-apps die zijn geïntegreerd met Azure AD. U hoeft uw toepassing niet te wijzigen of bij te werken om te kunnen werken met een toepassingsproxy. 

- **Veilig.** On-premises toepassingen kunnen de autorisatiebesturingselementen en beveiligingsanalyses van Azure gebruiken. On-premises toepassingen kunnen bijvoorbeeld voorwaardelijke toegang en verificatie in twee stappen gebruiken. Application Proxy vereist niet dat u binnenkomende verbindingen opent via uw firewall.
 
- **Kosteneffectief**. On-premises oplossingen vereisen doorgaans dat u gedemilitariseerde zones (DMZ's), edge-servers of andere complexe infrastructuren instelt en onderhoudt. Application Proxy draait in de cloud, waardoor het gemakkelijk te gebruiken is. Als u Application Proxy wilt gebruiken, hoeft u de netwerkinfrastructuur niet te wijzigen of extra apparaten te installeren in uw on-premises omgeving.

## <a name="what-is-application-proxy"></a>Wat is toepassingsproxy?
Application Proxy is een functie van Azure AD waarmee gebruikers toegang hebben tot on-premises webtoepassingen vanaf een externe client. Application Proxy omvat zowel de Application Proxy-service die in de cloud wordt uitgevoerd als de Application Proxy-connector die wordt uitgevoerd op een on-premises server. Azure AD, de application proxy-service en de Application Proxy-connector werken samen om het aanmeldingstoken van de gebruiker van Azure AD veilig door te geven aan de webtoepassing.

Application Proxy werkt met:

* Webtoepassingen die [geïntegreerde Windows-verificatie](application-proxy-configure-single-sign-on-with-kcd.md) gebruiken voor verificatie  
* Webtoepassingen die op formulieren gebaseerde of [op kopteksten gebaseerde](application-proxy-configure-single-sign-on-with-ping-access.md) toegang gebruiken  
* Web API's die u wilt blootstellen aan rijke toepassingen op verschillende apparaten  
* Toepassingen die worden gehost achter een [Extern bureaublad-gateway](application-proxy-integrate-with-remote-desktop-services.md)  
* Uitgebreide client-apps die zijn geïntegreerd met de Active Directory Authentication Library (ADAL)

Application Proxy ondersteunt eenmalige aanmelding. Zie [Een enkele aanmeldingsmethode kiezen](what-is-single-sign-on.md#choosing-a-single-sign-on-method)voor meer informatie over ondersteunde methoden.

Application Proxy wordt aanbevolen voor het geven van externe gebruikers toegang tot interne bronnen. Application Proxy vervangt de noodzaak van een VPN of reverse proxy. Het is niet bedoeld voor interne gebruikers op het bedrijfsnetwerk.  Deze gebruikers die application proxy onnodig gebruiken, kunnen onverwachte en ongewenste prestatieproblemen veroorzaken.

## <a name="how-application-proxy-works"></a>Hoe application proxy werkt

In het volgende diagram ziet u hoe Azure AD en Application Proxy samenwerken om afzonderlijke aanmeldingstoepassingen te bieden voor on-premises toepassingen.

![AzureAD-toepassingsproxydiagram](./media/application-proxy/azureappproxxy.png)

1. Nadat de gebruiker de toepassing via een eindpunt heeft geopend, wordt de gebruiker doorverwezen naar de aanmeldingspagina van Azure AD. 
2. Na een succesvolle aanmelding stuurt Azure AD een token naar het clientapparaat van de gebruiker.
3. De client stuurt het token naar de Application Proxy-service, die de gebruikersnaam (UPN) en de beveiligingsnaam (SPN) uit het token haalt. Application Proxy stuurt het verzoek vervolgens naar de Application Proxy-connector.
4. Als u eenmalige aanmelding hebt geconfigureerd, voert de connector aanvullende verificatie uit die namens de gebruiker vereist is.
5. De connector stuurt de aanvraag naar de on-premises toepassing.  
6. Het antwoord wordt verzonden via de connector en Application Proxy service naar de gebruiker.

| Onderdeel | Beschrijving |
| --------- | ----------- |
| Eindpunt  | Het eindpunt is een URL of een [portal voor eindgebruikers.](end-user-experiences.md) Gebruikers kunnen applicaties buiten uw netwerk bereiken door toegang te krijgen tot een externe URL. Gebruikers binnen uw netwerk hebben toegang tot de toepassing via een URL of een portal voor eindgebruikers. Wanneer gebruikers naar een van deze eindpunten gaan, verifiëren ze zich in Azure AD en worden ze vervolgens via de connector doorgestuurd naar de on-premises toepassing.|
| Azure AD | Azure AD voert de verificatie uit met de tenantmap die in de cloud is opgeslagen. |
| Application Proxy-service | Deze application proxy-service wordt uitgevoerd in de cloud als onderdeel van Azure AD. Het doorgeeft het aanmeldingstoken van de gebruiker aan de Application Proxy Connector. Application Proxy stuurt alle toegankelijke headers op de aanvraag door en stelt de headers in volgens het protocol, naar het IP-adres van de client. Als de binnenkomende aanvraag voor de proxy al die koptekst heeft, wordt het IP-adres van de client toegevoegd aan het einde van de door komma gescheiden lijst die de waarde van de koptekst is.|
| Proxyconnector voor toepassingen | De connector is een lichtgewicht agent die wordt uitgevoerd op een Windows Server in uw netwerk. De connector beheert de communicatie tussen de Application Proxy-service in de cloud en de on-premises toepassing. De connector maakt alleen gebruik van uitgaande verbindingen, zodat u geen binnenkomende poorten hoeft te openen of iets in de DMZ hoeft te plaatsen. De connectors zijn stateloos en halen informatie uit de cloud als dat nodig is. Zie [Azure AD Application Proxy connectors](application-proxy-connectors.md)begrijpen voor meer informatie over connectors, zoals hoe ze load-balance en authenticeren.|
| Active Directory (AD) | Active Directory wordt on-premises uitgevoerd om verificatie voor domeinaccounts uit te voeren. Wanneer eenmalige aanmelding is geconfigureerd, communiceert de connector met AD om eventuele extra verificatie uit te voeren die vereist is.
| On-premises toepassing | Ten slotte heeft de gebruiker toegang tot een on-premises toepassing. 

## <a name="next-steps"></a>Volgende stappen
Zie [Zelfstudie: Een on-premises toepassing toevoegen voor externe toegang via Application Proxy](application-proxy-add-on-premises-application.md)als u Application Proxy wilt gebruiken. 

Voor het laatste nieuws en updates, zie de [Application Proxy blog](https://blogs.technet.com/b/applicationproxyblog/)


