---
title: 'Toepassingsbeheer: Beste praktijken en aanbevelingen | Microsoft Documenten'
description: Lees aanbevolen procedures en aanbevelingen voor het beheren van toepassingen in Azure Active Directory. Meer informatie over het gebruik van automatische inrichting en publicatie van on-premises apps met Application Proxy.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/13/2019
ms.subservice: app-mgmt
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6534efb6fcd07ee3b9f3979cabf2feb77496a8b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74085289"
---
# <a name="application-management-best-practices"></a>Aanbevolen procedures voor toepassingsbeheer
Dit artikel bevat aanbevelingen en aanbevolen procedures voor het beheren van toepassingen in Azure Active Directory (Azure AD), het gebruik van automatische inrichting en het publiceren van on-premises apps met Application Proxy.

## <a name="cloud-app-and-single-sign-on-recommendations"></a>Cloud-app- en aanmeldingsaanbevelingen
| Aanbeveling | Opmerkingen |
| --- | --- |
| De azure AD-toepassingsgalerie voor apps controleren  | Azure AD heeft een galerie die duizenden vooraf geïntegreerde toepassingen bevat die zijn ingeschakeld met Enterprise single sign-on (SSO). Zie de [lijst met zelfstudies voor saas-app-richtlijnen](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)voor app-specifieke installatierichtlijnen.  | 
| Gebruik federatieve SAML-gebaseerde SSO  | Wanneer een toepassing dit ondersteunt, gebruikt u Federated, SAML-gebaseerde SSO met Azure AD in plaats van op een wachtwoord gebaseerde SSO en ADFS.  | 
| SHA-256 gebruiken voor certificaatondertekening  | Azure AD gebruikt standaard het SHA-256-algoritme om de SAML-respons te ondertekenen. SHA-256 gebruiken, tenzij de toepassing SHA-1 vereist (zie [opties voor certificaatondertekening](certificate-signing-options.md) en [aanmeldingsprobleem voor toepassingen](application-sign-in-problem-application-error.md).)  | 
| Gebruikerstoewijzing vereisen  | Gebruikers hebben standaard toegang tot uw bedrijfstoepassingen zonder dat ze eraan zijn toegewezen. Als de toepassing echter rollen blootlegt of als u wilt dat de toepassing wordt weergegeven in het toegangspaneel van een gebruiker, moet u gebruikerstoewijzing uitvoeren. (Zie [Richtlijnen voor ontwikkelaars voor het integreren van toepassingen](developer-guidance-for-integrating-applications.md).)  | 
| Het toegangspaneel van Mijn apps implementeren voor uw gebruikers | Het [toegangspaneel](end-user-experiences.md) op `https://myapps.microsoft.com` is een web-based portal die gebruikers voorziet van een enkel punt van binnenkomst voor hun toegewezen cloud-gebaseerde toepassingen. Als er extra mogelijkheden worden toegevoegd, zoals groepsbeheer en selfservicewachtwoordreset, kunnen gebruikers ze vinden in het toegangspaneel. Zie [Een implementatie van een toegangspaneel plannen](access-panel-deployment-plan.md).
| Groepstoewijzing gebruiken  | Als u in uw abonnement wordt opgenomen, wijst u groepen toe aan een toepassing, zodat u doorlopend toegangsbeheer delegeren aan de groepseigenaar. (Zie [Richtlijnen voor ontwikkelaars voor het integreren van toepassingen](developer-guidance-for-integrating-applications.md).)   | 
| Een proces voor het beheren van certificaten instellen | De maximale looptijd van een ondertekeningscertificaat is drie jaar. Gebruik rollen en e-maildistributielijsten om uitval als gevolg van het verlopen van een certificaat te voorkomen of te minimaliseren om ervoor te zorgen dat certificaatgerelateerde wijzigingsmeldingen nauwlettend worden gevolgd. |

## <a name="provisioning-recommendations"></a>Inrichten van aanbevelingen
| Aanbeveling | Opmerkingen |
| --- | --- |
| Zelfstudies gebruiken om inrichten in te stellen met cloud-apps | Raadpleeg de zelfstudies van de [SaaS-app](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) voor stapsgewijze richtlijnen voor het configureren van de inrichting voor de galerij-app die u wilt toevoegen. |
| Inrichten van logboeken (voorbeeld) gebruiken om de status te controleren | De [inrichtingslogboeken](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) geven details over alle acties die door de inrichtingsservice worden uitgevoerd, inclusief de status voor individuele gebruikers. |
| Een distributiegroep toewijzen aan de e-mail met de inrichtingsmelding | Als u de zichtbaarheid wilt vergroten van kritieke waarschuwingen die door de inrichtingsservice worden verzonden, wijst u een distributiegroep toe aan de instelling Meldingse-e-mails. |


## <a name="application-proxy-recommendations"></a>Aanbevelingen voor toepassingsproxy
| Aanbeveling | Opmerkingen |
| --- | --- |
| Toepassingsproxy gebruiken voor externe toegang tot interne bronnen | Application Proxy wordt aanbevolen voor het geven van externe gebruikers toegang tot interne bronnen, ter vervanging van de noodzaak van een VPN of reverse proxy. Het is niet bedoeld voor toegang tot bronnen vanuit het bedrijfsnetwerk, omdat het latentie kan toevoegen.
| Aangepaste domeinen gebruiken | Stel aangepaste domeinen in voor uw toepassingen (zie [Aangepaste domeinen configureren)](application-proxy-configure-custom-domain.md)zodat URL's voor gebruikers en tussen toepassingen binnen of buiten uw netwerk werken. U ook uw merknaam beheren en uw URL's aanpassen.  Wanneer u aangepaste domeinnamen gebruikt, bent u van plan een openbaar certificaat te verkrijgen van een niet-microsoft vertrouwde certificaatautoriteit. Azure Application Proxy ondersteunt standaard- ([wildcard)](application-proxy-wildcard.md)of SAN-gebaseerde certificaten. (Zie [Application Proxy planning](application-proxy-deployment-plan.md).) |
| Gebruikers synchroniseren voordat u Application Proxy implementeert | Voordat u de proxy van de toepassing implementeert, synchroniseert u gebruikersidentiteiten vanuit een on-premises map of maakt u ze rechtstreeks in Azure AD. Met identiteitssynchronisatie kan Azure AD gebruikers vooraf verifiëren voordat ze toegang krijgen tot door app-proxy gepubliceerde toepassingen. Het biedt ook de nodige gebruikersidentificatie-informatie om eenmalige aanmelding (SSO) uit te voeren. (Zie [Application Proxy planning](application-proxy-deployment-plan.md).) |
| Volg onze tips voor hoge beschikbaarheid en load balancing | Zie [Hoge beschikbaarheid en taakverdeling van uw toepassingsproxyconnectors en -toepassingen](application-proxy-high-availability-load-balancing.md)voor meer informatie over hoe het verkeer onder gebruikers, toepassingsproxyconnectors en back-end-app-servers stroomt en om tips te krijgen voor het optimaliseren van prestaties en taakverdeling. |
| Meerdere connectors gebruiken | Gebruik twee of meer Application Proxy-connectors voor meer tolerantie, beschikbaarheid en schaal (zie [Application Proxy connectors).](application-proxy-connectors.md) Maak verbindingsgroepen en zorg ervoor dat elke connectorgroep ten minste twee connectoren heeft (drie connectoren is optimaal). |
| Zoek connectorservers in de buurt van toepassingsservers en zorg ervoor dat ze zich in hetzelfde domein bevinden | Als u de prestaties wilt optimaliseren, zoekt u de connectorserver fysiek in de buurt van de toepassingsservers (zie [Overwegingen netwerktopologie).](application-proxy-network-topology.md) De connectorserver en webservers moeten ook tot hetzelfde Active Directory-domein behoren of vertrouwende domeinen omvatten. Deze configuratie is vereist voor SSO met Geïntegreerde Windows Authentication (IWA) en Kerberos Constrained Delegation (KCD). Als de servers zich in verschillende domeinen bevinden, moet u op resources gebaseerde delegatie voor SSO gebruiken (zie [KCD voor eenmalige aanmelding met Application Proxy).](application-proxy-configure-single-sign-on-with-kcd.md) |
| Automatische updates inschakelen voor connectors | Schakel automatische updates in voor uw connectors voor de nieuwste functies en bugfixes. Microsoft biedt directe ondersteuning voor de nieuwste connectorversie en één versie. (Zie [Versiegeschiedenis van de releasevan application proxy](application-proxy-release-version-history.md).) |
| Uw on-premises proxy omzeilen | Configureer de connector voor eenvoudiger onderhoud om uw on-premises proxy te omzeilen, zodat deze rechtstreeks verbinding maakt met de Azure-services. (Zie [Application Proxy connectors en proxy servers](application-proxy-configure-connectors-with-proxy-servers.md).) |
| Azure AD-toepassingsproxy gebruiken via proxy van webtoepassing | Gebruik Azure AD-toepassingsproxy voor de meeste on-premises scenario's. Webtoepassingsproxy heeft alleen de voorkeur in scenario's waarvoor een proxyserver voor AD FS vereist is en waar u geen aangepaste domeinen gebruiken in Azure Active Directory. (Zie [migratie van toepassingsproxy](application-proxy-migration.md).) |
