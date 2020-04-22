---
title: Toegang tot Azure Media Services API met Azure Active Directory-verificatie | Microsoft Documenten
description: Meer informatie over concepten en stappen die u moet ondernemen om Azure Active Directory (Azure AD) te gebruiken om toegang tot de Azure Media Services API te verifiëren.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: 8e1aeaf105ce371e965b433ac78e2b257f4bc18b
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682051"
---
# <a name="access-the-azure-media-services-api-with-azure-ad-authentication"></a>Toegang tot de API van Azure Media Services met Azure Active Directory-verificatie  

> [!NOTE]
> Er worden geen nieuwe functies of functionaliteit meer aan Media Services v2. toegevoegd. <br/>Bekijk de nieuwste versie, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Zie ook [migratierichtlijnen van v2 naar v3](../latest/migrate-from-v2-to-v3.md)

De Azure Media Services API is een RESTful API. U het gebruiken om bewerkingen uit te voeren op mediabronnen met behulp van een REST API of met behulp van beschikbare client-SDK's. Azure Media Services biedt een Media Services-client SDK voor Microsoft .NET. Als u toestemming wilt krijgen om toegang te krijgen tot Media Services-bronnen en de API voor Media Services, moet u eerst worden geverifieerd. 

Media Services ondersteunt [Azure Active Directory (Azure AD)-gebaseerde verificatie](../../active-directory/fundamentals/active-directory-whatis.md). De Azure Media REST-service vereist dat de gebruiker of toepassing die de REST API-aanvragen maakt, de rol **Inzender** of **eigenaar** heeft om toegang te krijgen tot de resources. Zie [Aan de slag met op rollen gebaseerd toegangsbeheer in de Azure-portal](../../role-based-access-control/overview.md)voor meer informatie.  

Dit document geeft een overzicht van hoe u toegang krijgt tot de Media Services API met behulp van REST of .NET API's.

> [!NOTE]
> Toegangscontroleautorisatie is op 1 juni 2018 afgeschaft.

## <a name="access-control"></a>Toegangsbeheer

Als het Azure Media REST-verzoek wil slagen, moet de aanroepende gebruiker een rol inzender of eigenaar hebben voor het Media Services-account dat hij probeert te openen.  
Alleen een gebruiker met de rol Eigenaar kan mediabron (account) toegang geven tot nieuwe gebruikers of apps. De rol Inzender heeft alleen toegang tot de mediabron.
Ongeautoriseerde aanvragen mislukken, met statuscode van 401. Als u deze foutcode ziet, controleert u of de rol Inzender of Eigenaar is toegewezen voor het Media Services-account van de gebruiker. U dit controleren in de Azure-portal. Zoek naar uw mediaaccount en klik op het tabblad **Toegangsbeheer.** 

![Tabblad Toegangsbeheer](./media/media-services-use-aad-auth-to-access-ams-api/media-services-access-control.png)

## <a name="types-of-authentication"></a>Typen verificatie 
 
Wanneer u Azure AD-verificatie gebruikt met Azure Media Services, hebt u twee verificatieopties:

- **Gebruikersverificatie**. Verifieer een persoon die de app gebruikt om te communiceren met Media Services-bronnen. De interactieve toepassing moet eerst de gebruiker om de referenties van de gebruiker vragen. Een voorbeeld is een beheerconsole-app die door geautoriseerde gebruikers wordt gebruikt om coderingstaken of live streaming te controleren. 
- **Serviceprincipal-verificatie**. Een service verifiëren. Toepassingen die deze verificatiemethode vaak gebruiken, zijn apps die daemonservices, services op het middenniveau of geplande taken uitvoeren. Voorbeelden zijn web-apps, functie-apps, logische apps, API en microservices.

### <a name="user-authentication"></a>Gebruikersverificatie 

Toepassingen die de gebruikersverificatiemethode moeten gebruiken, zijn het beheer of de bewaking van native apps: mobiele apps, Windows-apps en Console-toepassingen. Dit type oplossing is handig wanneer u menselijke interactie met de service wilt in een van de volgende scenario's:

- Dashboard controleren voor uw coderingstaken.
- Monitoring dashboard voor uw live streams.
- Beheertoepassing voor desktop- of mobiele gebruikers om resources in een Media Services-account te beheren.

> [!NOTE]
> Deze verificatiemethode mag niet worden gebruikt voor toepassingen die gericht zijn op consumenten. 

Een native toepassing moet eerst een toegangstoken van Azure AD aanschaffen en het vervolgens gebruiken wanneer u HTTP-aanvragen indient voor de MEDIA Services REST API. Voeg het toegangstoken toe aan de aanknopingspunt. 

In het volgende diagram ziet u een typische interactieve verificatiestroom voor toepassingen: 

![Diagram voor native apps](./media/media-services-use-aad-auth-to-access-ams-api/media-services-native-aad-app1.png)

In het voorgaande diagram geven de getallen de stroom van de aanvragen in chronologische volgorde weer.

> [!NOTE]
> Wanneer u de gebruikersverificatiemethode gebruikt, delen alle apps dezelfde (standaard) native toepassingsclient-id en worden uri omgeleid door de native application. 

1. Vraag een gebruiker om referenties.
2. Vraag een Azure AD-toegangstoken aan met de volgende parameters:  

   * Eindpunt azure AD-tenant.

       De tenantgegevens kunnen worden opgehaald uit de Azure-portal. Plaats de cursor op de naam van de aangemelde gebruiker in de rechterbovenhoek.
   * Media Services resource URI. 

       Deze URI is hetzelfde voor Media Services-accounts die zich in\/dezelfde Azure-omgeving bevinden (bijvoorbeeld https: /rest.media.azure.net).

   * Media Services (native) application client ID.
   * Media Services (native) applicatie redirect URI.
   * Resource URI voor REST Media Services.
        
       De URI vertegenwoordigt het REST API-eindpunt (bijvoorbeeld https://test03.restv2.westus.media.azure.net/api/).

     Zie [De Azure-portal gebruiken om toegang te krijgen tot Azure AD-verificatieinstellingen](media-services-portal-get-started-with-aad.md) met behulp van de optie gebruikersverificatie als u waarden voor deze parameters wilt opvragen.

3. Het Azure AD-toegangstoken wordt naar de client verzonden.
4. De client stuurt een aanvraag naar de Azure Media REST API met het Azure AD Access-token.
5. De klant krijgt de gegevens terug van Media Services.

Zie [Azure AD-verificatie gebruiken om toegang te krijgen tot de Media Services API met .NET](media-services-dotnet-get-started-with-aad.md). 

Als u de SDK van de Media Services .NET-client niet gebruikt, moet u handmatig een Azure AD-toegangstokenaanvraag maken met behulp van de parameters die in stap 2 zijn beschreven. Zie [De Azure AD-verificatiebibliotheek gebruiken om het Azure AD-token te krijgen voor](../../active-directory/azuread-dev/active-directory-authentication-libraries.md)meer informatie.

### <a name="service-principal-authentication"></a>Verificatie van service-principal

Toepassingen die deze verificatiemethode vaak gebruiken, zijn apps die services op het middenniveau en geplande taken uitvoeren: web-apps, functie-apps, logische apps, API's en microservices. Deze verificatiemethode is ook geschikt voor interactieve toepassingen waarin u mogelijk een serviceaccount wilt gebruiken om resources te beheren.

Wanneer u de serviceprincipal-verificatiemethode gebruikt om consumentenscenario's te maken, wordt verificatie meestal in de middelste laag (via een API) en niet rechtstreeks in een mobiele of desktoptoepassing verwerkt. 

Als u deze methode wilt gebruiken, maakt u een Azure AD-toepassing en serviceprincipal in de eigen tenant. Nadat u de toepassing hebt gemaakt, geeft u de rol inzender van de app inzender of eigenaar toegang tot het Media Services-account. U dit doen in de Azure-portal, met behulp van de Azure CLI of met een PowerShell-script. U ook een bestaande Azure AD-toepassing gebruiken. U uw Azure AD-app en serviceprincipal registreren en beheren [in de Azure-portal.](media-services-portal-get-started-with-aad.md) U dit ook doen met [Azure CLI](media-services-use-aad-auth-to-access-ams-api.md) of [PowerShell.](media-services-powershell-create-and-configure-aad-app.md) 

![Apps op het middelste niveau](./media/media-services-use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

Nadat u uw Azure AD-toepassing hebt gemaakt, krijgt u waarden voor de volgende instellingen. U hebt deze waarden nodig voor verificatie:

- Client-id 
- Clientgeheim 

In het voorgaande cijfer geven de getallen de stroom van de aanvragen in chronologische volgorde weer:
    
1. Een middelste-tier app (web-API of webtoepassing) vraagt een Azure AD access token met de volgende parameters:  

   * Eindpunt azure AD-tenant.

       De tenantgegevens kunnen worden opgehaald uit de Azure-portal. Plaats de cursor op de naam van de aangemelde gebruiker in de rechterbovenhoek.
   * Media Services resource URI. 

       Deze URI is hetzelfde voor Media Services-accounts die zich in\/dezelfde Azure-omgeving bevinden (bijvoorbeeld https: /rest.media.azure.net).

   * Resource URI voor REST Media Services.

       De URI vertegenwoordigt het REST API-eindpunt (bijvoorbeeld https://test03.restv2.westus.media.azure.net/api/).

   * Azure AD-toepassingswaarden: de client-id en het clientgeheim.
    
     Zie [De Azure-portal gebruiken om toegang te krijgen tot Azure AD-verificatieinstellingen](media-services-portal-get-started-with-aad.md) met behulp van de optie serviceprincipal-verificatie als u waarden voor deze parameters wilt krijgen.

2. Het Azure AD-toegangstoken wordt naar de middelste laag verzonden.
4. De middelste laag verzendt aanvraag naar de Azure Media REST API met het Azure AD-token.
5. De middelste laag krijgt de gegevens terug van Media Services.

Zie [Azure AD-verificatie gebruiken om toegang te krijgen tot Azure Media Services API met .NET](media-services-dotnet-get-started-with-aad.md). 

Als u de SDK van de Media Services .NET-client niet gebruikt, moet u handmatig een Azure AD-tokenaanvraag maken met behulp van parameters die in stap 1 zijn beschreven. Zie [De Azure AD-verificatiebibliotheek gebruiken om het Azure AD-token te krijgen voor](../../active-directory/azuread-dev/active-directory-authentication-libraries.md)meer informatie.

## <a name="troubleshooting"></a>Problemen oplossen

Uitzondering: "De externe server heeft een fout geretourneerd: (401) Ongeautoriseerd."

Oplossing: Als het REST-verzoek voor MediaServices succesvol is, moet de bellende gebruiker een rol van inzender of eigenaar zijn in het Media Services-account dat hij probeert te openen. Zie de sectie [Toegangsbeheer](media-services-use-aad-auth-to-access-ams-api.md#access-control) voor meer informatie.

## <a name="resources"></a>Resources

De volgende artikelen zijn overzichten van Azure AD-verificatieconcepten: 

- [Verificatiescenario's aangepakt door Azure AD](../../active-directory/develop/authentication-scenarios.md)
- [Een toepassing toevoegen, bijwerken of verwijderen in Azure AD](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md)
- [Toegangsbeheer op basis van rollen configureren en beheren met PowerShell](../../role-based-access-control/role-assignments-powershell.md)

## <a name="next-steps"></a>Volgende stappen

* Gebruik de Azure-portal om toegang te [krijgen tot Azure AD-verificatie om Azure Media Services API te gebruiken.](media-services-portal-get-started-with-aad.md)
* Gebruik Azure AD-verificatie om toegang te [krijgen tot de Azure Media Services API met .NET](media-services-dotnet-get-started-with-aad.md).

