---
title: Vereisten voor Azure Active Directory reporting API | Microsoft Documenten
description: Meer informatie over de vereisten voor toegang tot de Azure AD-rapportage-API
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 03/04/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7fd3580ca03fa49d428904c6da78fdf9cda202c7
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991259"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>Vereisten voor toegang tot de Azure Active Directory-rapportage-API

De [Azure Active Directory (Azure AD) rapportage-API's](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-reporting-api) bieden toegang tot de gegevens op programmeerniveau via een set op REST-gebaseerde API's. U deze API's bellen vanuit programmeertalen en -tools.

De rapportage-API maakt gebruik van [OAuth](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad) om de toegang tot de web-API's te autoriseren.

Als u uw toegang tot de rapportage-API wilt voorbereiden, moet u:

1. [Rollen toewijzen](#assign-roles)
2. [Licentievereisten](#license-requirements)
3. [Een toepassing registreren](#register-an-application)
4. [Machtigingen verlenen](#grant-permissions)
5. [Configuratie-instellingen verzamelen](#gather-configuration-settings)

## <a name="assign-roles"></a>Rollen toewijzen

Als u toegang wilt krijgen tot de rapportagegegevens via de API, moet u een van de volgende rollen toegewezen hebben:

- Beveiligingslezer

- Beveiligingsbeheer

- Globale beheerder

## <a name="license-requirements"></a>Licentievereisten

Als u toegang wilt krijgen tot de aanmeldingsrapporten voor een tenant, moet een Azure AD-tenant een Azure AD Premium-licentie hebben gekoppeld. Azure AD Premium P1 -licentie (of hoger) is vereist voor toegang tot aanmeldingsrapporten voor elke Azure AD-tenant. Als het maptype Azure AD B2C is, zijn de aanmeldingsrapporten ook toegankelijk via de API zonder extra licentievereiste. 


## <a name="register-an-application"></a>Een toepassing registreren

Registratie is nodig, zelfs als u de rapportage-API gebruikt met behulp van een script. De registratie geeft u een **applicatie-id,** die nodig is voor de autorisatie oproepen en stelt uw code in staat om tokens te ontvangen.

Als u uw directory wilt configureren om toegang te krijgen tot de Azure AD-rapportage-API, moet u zich aanmelden bij de [Azure-portal](https://portal.azure.com) met een Azure-beheerdersaccount dat ook lid is van de **maprol Globale beheerder** in uw Azure AD-tenant.

> [!IMPORTANT]
> Toepassingen die worden uitgevoerd onder referenties met beheerdersbevoegdheden kunnen zeer krachtig zijn, dus zorg ervoor dat de id en geheime referenties van de toepassing op een veilige locatie worden bewaard.
> 

**Ga als een Azure AD-toepassing te registreren:**

1. Selecteer Azure Active Directory in de [Azure-portal](https://portal.azure.com)in het linkernavigatiedeelvenster. **Azure Active Directory**
   
    ![Toepassing registreren](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Selecteer **app-registraties**op de pagina **Azure Active Directory** .

    ![Toepassing registreren](./media/howto-configure-prerequisites-for-reporting-api/02.png) 

3. Selecteer op de pagina **App-registraties** de optie **Nieuwe registratie**.

    ![Toepassing registreren](./media/howto-configure-prerequisites-for-reporting-api/03.png)

4. De pagina **Registratie een aanvraag:**

    ![Toepassing registreren](./media/howto-configure-prerequisites-for-reporting-api/04.png)

    a. Typ in het tekstvak **Naam** . `Reporting API application`

    b. Selecteer **Accounts in deze organisatie alleen**voor het type Ondersteunde **accounts**.

    c. Typ in het **tekstvak Omleiden url** **Webtekst** . `https://localhost`

    d. Selecteer **Registreren**. 


## <a name="grant-permissions"></a>Machtigingen verlenen 

Afhankelijk van de API die u wilt openen, moet u uw app de volgende machtigingen verlenen:  

| API | Machtiging |
| --- | --- |
| Windows Azure Active Directory | Mapgegevens lezen |
| Microsoft Graph | Alle controlelogboekgegevens lezen |


![Toepassing registreren](./media/howto-configure-prerequisites-for-reporting-api/36.png)

In de volgende sectie worden de stappen voor beide API's weergegeven. Als u geen toegang wilt tot een van de API's, u de gerelateerde stappen overslaan.

**Ga als u uw aanvraagmachtigingen verlenen voor het gebruik van de API's:**


1. Selecteer **API-machtigingen** **en voeg een machtiging toe.** 

    ![Toepassing registreren](./media/howto-configure-prerequisites-for-reporting-api/05.png)

2. Zoek op de **pagina API-machtigingen aanvragen**naar **Ondersteuning verouderde API** Azure Active **Directory Graph**. 

    ![Toepassing registreren](./media/howto-configure-prerequisites-for-reporting-api/06.png)

3. Schakel op de pagina **Vereiste machtigingen** **toepassingsmachtigingen**in, vouw **mapuit** **mapmap.ReadAll**uit .  Selecteer **Machtigingen toevoegen**.

    ![Toepassing registreren](./media/howto-configure-prerequisites-for-reporting-api/07.png)

4. Selecteer op de pagina **Rapportage API-toepassing - API-machtigingen** de **optie Toestemming voor beheerders verlenen**. 

    ![Toepassing registreren](./media/howto-configure-prerequisites-for-reporting-api/08.png)

5. Opmerking: **Microsoft Graph** wordt standaard toegevoegd tijdens API-registratie.

    ![Toepassing registreren](./media/howto-configure-prerequisites-for-reporting-api/15.png)

## <a name="gather-configuration-settings"></a>Configuratie-instellingen verzamelen 

In deze sectie ziet u hoe u de volgende instellingen uit uw map halen:

- Domeinnaam
- Client-id
- Clientgeheim

U hebt deze waarden nodig bij het configureren van aanroepen naar de rapportage-API. 

### <a name="get-your-domain-name"></a>Uw domeinnaam oppakken

**Ga als uw domeinnaam op de eerste plaats:**

1. Selecteer **Azure Active Directory**in de [Azure-portal](https://portal.azure.com)in het linkernavigatiedeelvenster .
   
    ![Toepassing registreren](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Selecteer **aangepaste domeinnamen**op de pagina Azure **Active Directory** .

    ![Toepassing registreren](./media/howto-configure-prerequisites-for-reporting-api/09.png) 

3. Kopieer uw domeinnaam uit de lijst met domeinen.


### <a name="get-your-applications-client-id"></a>De client-id van uw toepassing ophalen

**Ga als eerste op de klant-id van uw toepassing:**

1. Klik in de [Azure-portal](https://portal.azure.com)in het linkernavigatiedeelvenster op **Azure Active Directory**.
   
    ![Toepassing registreren](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Selecteer uw toepassing op de pagina **App-registraties.**

3. Navigeer op de toepassingspagina naar **toepassings-id** en selecteer **Klik om te kopiëren**.

    ![Toepassing registreren](./media/howto-configure-prerequisites-for-reporting-api/11.png) 


### <a name="get-your-applications-client-secret"></a>Krijg het klantgeheim van uw toepassing
 Voorkom fouten bij het openen van controlelogboeken of aanmelding met behulp van de API.

**Ga als eerste voor meer het klantgeheim van uw toepassing:**

1. Klik in de [Azure-portal](https://portal.azure.com)in het linkernavigatiedeelvenster op **Azure Active Directory**.
   
    ![Toepassing registreren](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2.  Selecteer uw toepassing op de pagina **App-registraties.**

3.  Selecteer **Certificaten en geheimen op** de pagina **API-toepassing,** klik in de sectie **Clientgeheimen** op **+ Nieuw clientgeheim**. 

    ![Toepassing registreren](./media/howto-configure-prerequisites-for-reporting-api/12.png)

5. Voeg op de geheime pagina **Een client toevoegen** het als:

    a. Typ in het tekstvak **Beschrijving** . `Reporting API`

    b. Als **verloopt,** selecteert **u In 2 jaar**.

    c. Klik op **Opslaan**.

    d. Kopieer de sleutelwaarde.

## <a name="troubleshoot-errors-in-the-reporting-api"></a>Fouten in de rapportage-API oplossen

In deze sectie worden de veelvoorkomende foutberichten weergegeven die u tegenkomen terwijl u toegang hebt tot activiteitsrapporten met behulp van de Microsoft Graph-API en stappen voor de oplossing ervan.

### <a name="error-failed-to-get-user-roles-from-microsoft-graph"></a>Fout: kan geen gebruikersrollen krijgen uit Microsoft Graph

 Meld u aan bij uw account met beide aanmeldingsknoppen in de gebruikersinterface van Graph Explorer om te voorkomen dat u een foutmelding krijgt wanneer u zich wilt aanmelden met Graph Explorer. 

![Graph Explorer](./media/troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-microsoft-graph"></a>Fout: kan geen premium licentiecontrole uitvoeren vanuit Microsoft Graph 

Als u dit foutbericht tegenkomt terwijl u aanmeldingen probeert te openen met Grafiek Verkenner, kiest u **Machtigingen wijzigen** onder uw account aan de linkerkant en selecteert u **Taken.ReadWrite** en **Directory.Read.All**. 

![Gebruikersinterface voor machtigingen wijzigen](./media/troubleshoot-graph-api/modify-permissions.png)

### <a name="error-tenant-is-not-b2c-or-tenant-doesnt-have-premium-license"></a>Fout: Huurder is geen B2C of huurder heeft geen premium licentie

Voor het openen van aanmeldingsrapporten is een Azure Active Directory premium 1 -licentie (P1) vereist. Als u dit foutbericht ziet terwijl u aanmeldingen hebt geopend, controleert u of uw tenant een licentie heeft met een Azure AD P1-licentie.

### <a name="error-the-allowed-roles-does-not-include-user"></a>Fout: de toegestane rollen bevatten geen gebruiker. 

 Voorkom fouten bij het openen van controlelogboeken of aanmelding met behulp van de API. Controleer of uw account deel uitmaakt van de rol **Beveiligingslezer** of **Rapportlezer** in uw Azure Active Directory-tenant.

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Fout: Toepassing ontbreekt AAD 'Lees directory gegevens' toestemming 

### <a name="error-application-missing-microsoft-graph-api-read-all-audit-log-data-permission"></a>Fout: Toepassing ontbreekt Microsoft Graph API 'Lees alle audit log gegevens' toestemming

Volg de stappen in de [vereisten om toegang te krijgen tot de Azure Active Directory-rapportage-API](howto-configure-prerequisites-for-reporting-api.md) om ervoor te zorgen dat uw toepassing wordt uitgevoerd met de juiste set machtigingen. 

## <a name="next-steps"></a>Volgende stappen

* [Gegevens ophalen met de Azure Active Directory rapportage-API met certificaten](tutorial-access-api-with-certificates.md)
* [Api-verwijzing voor controle](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [API-verwijzing naar voor aanmeldingsactiviteitsrapport](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
