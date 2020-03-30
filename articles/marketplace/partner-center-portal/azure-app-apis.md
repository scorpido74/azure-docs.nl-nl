---
title: Onboarding API voor Azure-apps in de commerciële marketplace
description: API-vereisten voor Azure-apps in commerciële marktplaats op Microsoft Partner Center.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/10/2019
ms.openlocfilehash: cc4d56058ce3985ec3a1d9124ef4ec73ff6be1a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80279756"
---
# <a name="api-for-onboarding-azure-apps-in-partner-center"></a>API voor onboarding Azure-apps in Partnercenter

Gebruik de *API voor het indienen van partnercentrumom* programmatisch inzendingen te maken voor Azure-aanbiedingen en deze te publiceren.  Deze API is handig als uw account veel aanbiedingen beheert en u het indieningsproces voor deze aanbiedingen wilt automatiseren en optimaliseren.

## <a name="api-prerequisites"></a>API-vereisten

Er zijn een paar programmatische elementen die u nodig hebt om de Partner Center API voor Azure-producten te gebruiken: 

- een Azure Active Directory-toepassing.
- een Azure Active Directory -toegangstoken (Azure Active Directory).

### <a name="step-1-complete-prerequisites-for-using-the-partner-center-submission-api"></a>Stap 1: Vereisten voltooien voor het gebruik van de API voor het indienen van het Partnercenter

Voordat u code gaat schrijven om de API voor indiening van het Partnercenter aan te roepen, moet u ervoor zorgen dat u de volgende vereisten hebt voltooid.

- U (of uw organisatie) moet een Azure AD-map hebben en u moet algemene [beheerdersmachtiging](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) voor de map hebben. Als u al Office 365 of andere zakelijke services van Microsoft gebruikt, beschikt u al over de Azure AD-map. Anders u zonder extra kosten [een nieuw Azure AD maken in partnercentrum.](https://docs.microsoft.com/windows/uwp/publish/associate-azure-ad-with-partner-center#create-a-brand-new-azure-ad-to-associate-with-your-partner-center-account)

- U moet [een Azure AD-toepassing koppelen aan uw Partner Center-account](https://docs.microsoft.com/windows/uwp/monetize/create-and-manage-submissions-using-windows-store-services#associate-an-azure-ad-application-with-your-windows-partner-center-account) en uw tenant-id, client-id en sleutel verkrijgen. U hebt deze waarden nodig om een Azure AD-toegangstoken te verkrijgen, dat u gebruikt bij aanroepen naar de Microsoft Store-indienings-API.

#### <a name="how-to-associate-an-azure-ad-application-with-your-partner-center-account"></a>Een Azure AD-toepassing koppelen aan uw Partner Center-account

Als u de Api voor het indienen van de Microsoft Store wilt gebruiken, moet u een Azure AD-toepassing koppelen aan uw Partner Center-account, de tenant-id en de client-id voor de toepassing ophalen en een sleutel genereren. De Azure AD-toepassing vertegenwoordigt de app of service van waaruit u de API voor het indienen van partnercentrumen wilt aanroepen. U hebt de tenant-id, client-id en sleutel nodig om een Azure AD-toegangstoken te verkrijgen dat u doorgeeft aan de API.

>[!Note]
>U hoeft deze taak slechts één keer uit te voeren. Nadat u de tenant-id, client-id en sleutel hebt, u ze opnieuw gebruiken wanneer u een nieuw Azure AD-toegangstoken wilt maken.

1. In Partnercentrum [koppelt u het PartnerCenter-account van uw organisatie aan de Azure AD-map van uw organisatie.](https://docs.microsoft.com/windows/uwp/publish/associate-azure-ad-with-partner-center)
1. Voeg vervolgens op de pagina **Gebruikers** in het gedeelte **Accountinstellingen** van Partnercentrum [de Azure AD-toepassing toe](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#add-azure-ad-applications-to-your-partner-center-account) die de app of service vertegenwoordigt die u gebruikt om toegang te krijgen tot inzendingen voor uw Partner Center-account. Zorg ervoor dat u deze toepassing de rol **Manager** toewijst. Als de toepassing nog niet bestaat in uw Azure AD-map, u [een nieuwe Azure AD-toepassing maken in partnercentrum](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#create-a-new-azure-ad-application-account-in-your-organizations-directory-and-add-it-to-your-partner-center-account).
1. Ga terug naar de pagina **Gebruikers,** klik op de naam van uw Azure AD-toepassing om naar de toepassingsinstellingen te gaan en kopieer de waarden **tenant-id** en **client-id.**
1. Klik **op Nieuwe toets toevoegen**. Kopieer op het volgende scherm de waarde **Sleutel.** Je hebt geen toegang meer tot deze informatie nadat je deze pagina hebt verlaten. Zie [Sleutels beheren voor een Azure AD-toepassing voor](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#manage-keys)meer informatie.

### <a name="step-2-obtain-an-azure-ad-access-token"></a>Stap 2: Een Azure AD-toegangstoken verkrijgen

Voordat u een van de methoden in de API voor indiening van het Partnercentrum aanroept, moet u eerst een Azure AD-toegangstoken verkrijgen dat u doorgeeft aan de **autorisatiekop** van elke methode in de API. Nadat u een toegangstoken hebt verkregen, hebt u 60 minuten om het te gebruiken voordat het verloopt. Nadat het token is verlopen, u het token vernieuwen, zodat u het blijven gebruiken in toekomstige oproepen naar de API.

Als u het toegangstoken wilt verkrijgen, volgt u de instructies in [Service to Service Calls met behulp van clientreferenties](https://azure.microsoft.com/documentation/articles/active-directory-protocols-oauth-service-to-service/) om een `HTTP POST` naar het `https://login.microsoftonline.com/<tenant_id>/oauth2/token` eindpunt te verzenden. Hier is een voorbeeld verzoek:

JSONCopy
```Json
POST https://login.microsoftonline.com/<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded; charset=utf-8

grant_type=client_credentials
&client_id=<your_client_id>
&client_secret=<your_client_secret>
&resource= https://api.partner.microsoft.com
```

Voor de *tenant_id* `POST URI` waarde in de *parameters en client_id* en *client_secret,* geeft u de tenant-id, de client-id en de sleutel voor uw toepassing op die u in de vorige sectie hebt opgehaald uit partnercentrum. Voor de *resourceparameter* moet `https://api.partner.microsoft.com`u opgeven .

### <a name="step-3-use-the-microsoft-store-submission-api"></a>Stap 3: De API voor het indienen van de Microsoft Store gebruiken

Nadat u een Azure AD-toegangstoken hebt, u methoden aanroepen in de API voor indiening van het Partnercentrum. Als u inzendingen wilt maken of bijwerken, roept u doorgaans meerdere methoden aan in de API voor het indienen van het Partnercentrum in een specifieke volgorde. Zie de swagger van de inname-API voor informatie over elk scenario en de syntaxis van elke methode.

https://apidocs.microsoft.com/services/partneringestion/
