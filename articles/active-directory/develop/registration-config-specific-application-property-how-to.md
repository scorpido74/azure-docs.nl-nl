---
title: Azure-portalregistratievelden voor speciaal ontwikkelde apps
description: Richtlijnen voor het registreren van een op maat ontwikkelde toepassing met Azure AD
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: ryanwi
ms.openlocfilehash: 36d74b9926639bb4ec49821a3d73b5d615016394
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76702671"
---
# <a name="azure-portal-registration-fields-for-custom-developed-apps"></a>Azure-portalregistratievelden voor speciaal ontwikkelde apps

In dit artikel vindt u een korte beschrijving van alle beschikbare velden in het formulier voor toepassingsregistratie in de [Azure-portal.](https://portal.azure.com)

## <a name="register-a-new-application"></a>Een nieuwe toepassing registreren

-   Als u een nieuwe toepassing wilt registreren, navigeert u naar de [Azure-portal.](https://portal.azure.com)

-   Klik in het linkernavigatiedeelvenster op **Azure Active Directory.**

-   Kies **App-registraties** en klik op **Toevoegen**.

-   Hiermee wordt het aanmeldingsformulier geopend.

## <a name="fields-in-the-application-registration-form"></a>Velden in het aanmeldingsformulier

| Veld            | Beschrijving                                                                              |
|------------------|------------------------------------------------------------------------------------------|
| Name             | De naam van de toepassing. Het moet een minimum van vier tekens hebben.                |
| Ondersteunde accounttypen| Selecteer welke accounts u wilt dat uw toepassing ondersteunt: alleen accounts in deze organisatiemap, accounts in een organisatiemap of accounts in een organisatiemap en persoonlijke Microsoft-accounts.  |
| URI omleiden (optioneel) | Selecteer het type app dat u bouwt, **web-** of **openbare client (mobile & desktop)** en voer vervolgens de omleiding uri (of antwoord-URL) in voor uw toepassing. Geef voor webtoepassingen de basis-URL van de app op. http://localhost:31544 kan bijvoorbeeld de URL zijn van een web-app die op uw lokale machine wordt uitgevoerd. Gebruikers moeten deze URL gebruiken om zich bij een webclienttoepassing aan te melden. Geef voor openbare clienttoepassingen de URI op die in Azure Active Directory wordt gebruikt om tokenantwoorden te retourneren. Voer een waarde in die specifiek is voor uw toepassing, zoals myapp://auth. Bekijk onze quickstarts om specifieke voorbeelden voor webapplicaties of native applicaties te [bekijken.](https://docs.microsoft.com/azure/active-directory/develop)|

Zodra u de bovenstaande velden hebt ingevuld, wordt de toepassing geregistreerd in de Azure-portal en wordt u doorgestuurd naar de pagina met toepassingsoverzicht. De instellingenpagina's in het linkerdeelvenster onder **Beheren** hebben meer velden waarop u uw toepassing aanpassen. De onderstaande tabellen beschrijven alle velden. U ziet alleen een subset van deze velden, afhankelijk van of u een webtoepassing of een openbare clienttoepassing hebt gemaakt.

### <a name="overview"></a>Overzicht

| Veld           | Beschrijving        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Toepassings-id  | Wanneer u een toepassing registreert, wijst Azure AD uw toepassing een toepassings-id toe. De toepassings-id kan worden gebruikt om uw toepassing op unieke wijze te identificeren in verificatieaanvragen voor Azure AD en om toegang te krijgen tot bronnen zoals de Graph API.                                                          |
| App-id-URI      | Dit moet een unieke URI, meestal van het formulier **&lt;https:// tenant\_naam&gt;/&lt;naam\_aanvraag naam&gt;.** Dit wordt gebruikt tijdens de autorisatietoewijzingsstroom, als een unieke id om de resource op te geven waarvoor het token moet worden uitgegeven. Het wordt ook de 'aud' claim in het uitgegeven toegangstoken. |

### <a name="branding"></a>Huisstijl

| Veld           | Beschrijving        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nieuw logo uploaden | U dit gebruiken om een logo voor uw toepassing te uploaden. Het logo moet in de .bmp-, .jpg- of .png-indeling staan en de bestandsgrootte moet kleiner zijn dan 100 KB. De afmetingen voor de afbeelding moeten 215x215 pixels zijn, met centrale beeldafmetingen van 94x94 pixels.|
| URL van startpagina   | Dit is de aanmeldings-URL die is opgegeven tijdens de registratie van de aanvraag.|

### <a name="authentication"></a>Authentication

| Veld           | Beschrijving        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Afmeldings-URL      | Dit is de url van de uitloging. Azure AD stuurt een afmeldverzoek naar deze URL wanneer de gebruiker zijn sessie met Azure AD met een andere geregistreerde toepassing wist.|
| Ondersteunde accounttypen  | Deze schakelaar geeft aan of de toepassing door meerdere tenants kan worden gebruikt. Dit betekent meestal dat externe organisaties uw toepassing kunnen gebruiken door deze te registreren in hun tenant en toegang te verlenen tot de gegevens van hun organisatie.|
| Omleidings-URL's      | De omleidings- of antwoord-URL's zijn de eindpunten waarin Azure AD alle tokens retourneert die uw toepassing aanvraagt. Voor native toepassingen wordt de gebruiker hier verzonden na een succesvolle autorisatie. Azure AD controleert of de omleiding uri uw toepassingsbenodigdheden in de OAuth 2.0-aanvraag overeenkomt met een van de geregistreerde waarden in de portal.|

### <a name="certificates-and-secrets"></a>Certificaten en geheimen

| Veld           | Beschrijving        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Clientgeheimen            | U clientgeheimen of sleutels maken om programmatisch toegang te krijgen tot web-API's die zijn beveiligd door Azure AD zonder enige interactie van de gebruiker. Voer **op** de geheime pagina Nieuwe client een sleutelbeschrijving en de vervaldatum in en sla op om de sleutel te genereren. Zorg ervoor dat u het ergens veilig opslaat, omdat u er later geen toegang meer toe hebt.             |

## <a name="next-steps"></a>Volgende stappen

[Toepassingen beheren met Azure Active Directory](../manage-apps/what-is-application-management.md)
