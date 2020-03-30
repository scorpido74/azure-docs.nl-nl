---
title: Azure AD-verificatie gebruiken om toegang te krijgen tot azure mediaservices-API met REST | Microsoft Documenten
description: Meer informatie over het openen van Azure Media Services API met Azure Active Directory-verificatie met REST.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.reviewer: willzhan; johndeu
ms.openlocfilehash: a693eb374365670da3fe8c4b2bb8ce664a024217
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295446"
---
# <a name="use-azure-ad-authentication-to-access-the-media-services-api-with-rest"></a>Azure Active Directory-verificatie gebruiken voor toegang tot de Media Services-API met REST

> [!NOTE]
> Er worden geen nieuwe functies of functionaliteit meer aan Media Services v2. toegevoegd. <br/>Bekijk de nieuwste versie, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Zie ook [migratierichtlijnen van v2 naar v3](../latest/migrate-from-v2-to-v3.md)

Wanneer u Azure AD-verificatie gebruikt met Azure Media Services, u op twee manieren verifiëren:

- **Gebruikersverificatie** verifieert een persoon die de app gebruikt om te communiceren met Azure Media Services-bronnen. De interactieve toepassing moet eerst vragen aan de gebruiker om referenties. Een voorbeeld is een beheerconsole-app die wordt gebruikt door geautoriseerde gebruikers om coderingstaken of live streaming te controleren. 
- **Serviceprincipal-verificatie** verifieert een service. Toepassingen die deze verificatiemethode vaak gebruiken, zijn apps die daemonservices, services op het middensegment of geplande taken uitvoeren, zoals web-apps, functie-apps, logische apps, API's of microservices.

    In deze zelfstudie ziet u hoe u de **hoofdverificatie van** azure AD-service gebruiken om toegang te krijgen tot de AMS-API met REST. 

    > [!NOTE]
    > **Service principal** is the recommended best practice for most applications connecting to Azure Media Services. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * De verificatiegegevens ophalen via de Azure-portal
> * Het toegangstoken krijgen met Postman
> * De **ELEMENTEN-API** testen met behulp van het toegangstoken


> [!IMPORTANT]
> Momenteel ondersteunt Media Services het verificatiemodel azure Access Control-services. Toegangsbeheerverificatie wordt echter afgeschaft op 1 juni 2018. We raden u aan om zo snel mogelijk naar het Azure Active Directory-verificatiemodel te migreren.

## <a name="prerequisites"></a>Vereisten

- Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) voordat u begint.
- [Maak een Azure Media Services-account met behulp van de Azure-portal.](media-services-portal-create-account.md)
- Bekijk het [overzichtsartikel Accessing Azure Media Services API met Azure AD-verificatieoverzicht.](media-services-use-aad-auth-to-access-ams-api.md)
- Installeer de [Postman](https://www.getpostman.com/) REST-client om de REST API's uit te voeren die in dit artikel worden weergegeven. 

    In deze tutorial gebruiken we **Postman,** maar elke REST tool zou geschikt zijn. Andere alternatieven zijn: **Visual Studio Code** met de REST plugin of **Telerik Fiddler**. 

## <a name="get-the-authentication-information-from-the-azure-portal"></a>De verificatiegegevens ophalen via de Azure-portal

### <a name="overview"></a>Overzicht

Als u toegang wilt krijgen tot de API van Media Services, moet u de volgende gegevenspunten verzamelen.

|Instelling|Voorbeeld|Beschrijving|
|---|-------|-----|
|Azure Active Directory-tenantdomein|microsoft.onmicrosoft.com|Azure AD als STS-eindpunt (Secure Token Service) wordt <https://login.microsoftonline.com/{your-ad-tenant-name.onmicrosoft.com}/oauth2/token>gemaakt met de volgende indeling: . Azure AD geeft een JWT uit om toegang te krijgen tot bronnen (een toegangstoken).|
|Eindpunt van de REST-API|<https://amshelloworld.restv2.westus.media.azure.net/api/>|Dit is het eindpunt waartegen alle Media Services REST API-aanroepen in uw toepassing worden uitgevoerd.|
|Client-id (toepassings-id)|f7fbbb29-a02d-4d91-bbc6-59a2579259d2|Azure AD-toepassings-id (client). De client-ID is vereist om het toegangstoken te krijgen. |
|Clientgeheim|+mUERiNzVMoJGggD6aV1etzFGa1n6KeSlLjIq+Dbim0=|Azure AD-toepassingssleutels (clientgeheim). Het clientgeheim is vereist om het toegangstoken te krijgen.|

### <a name="get-aad-auth-info-from-the-azure-portal"></a>Ontvang AAD auth-informatie van de Azure-portal

Voer de volgende stappen uit om de informatie te krijgen:

1. Log in bij de [Azure-portal](https://portal.azure.com).
2. Navigeer naar uw AMS-exemplaar.
3. Selecteer **API-toegang**.
4. Klik op **Verbinding maken met Azure Media Services API met serviceprincipal**.

    ![API-toegang](./media/connect-with-rest/connect-with-rest01.png)

5. Selecteer een bestaande **Azure AD-toepassing** of maak een nieuwe (zie hieronder).

    > [!NOTE]
    > Als het Azure Media REST-verzoek wil slagen, moet de aanroepende gebruiker een **rol inzender** of **eigenaar** hebben voor het Media Services-account dat hij probeert te openen. Als u een uitzondering krijgt met de tekst 'De externe server heeft een fout geretourneerd: (401) Onbevoegd", [raadpleegt Access control](media-services-use-aad-auth-to-access-ams-api.md#access-control).

    Als u een nieuwe AD-app wilt maken, voert u de volgende stappen uit:
    
   1. Druk **op Nieuw maken**.
   2. Voer een naam in.
   3. Druk nogmaals **op Nieuw maken.**
   4. Klik op **Opslaan**.

      ![API-toegang](./media/connect-with-rest/new-app.png)

      De nieuwe app verschijnt op de pagina.

6. Haal de **client-id** (toepassings-id) op.
    
   1. Selecteer de toepassing.
   2. Haal de **client-id** uit het venster aan de rechterkant. 

      ![API-toegang](./media/connect-with-rest/existing-client-id.png)

7. Download de **sleutel** van de toepassing (clientgeheim). 

   1. Klik **op** de knop Toepassing beheren (de client-id-gegevens staat onder **Toepassings-id).** 
   2. Druk op **Toetsen**.
    
       ![API-toegang](./media/connect-with-rest/manage-app.png)
   3. Genereer de app-toets (clientgeheim) door **BESCHRIJVING** en **VERLOPEN** in te vullen en op **Opslaan te drukken.**
    
       Zodra de knop **Opslaan** is ingedrukt, wordt de sleutelwaarde weergegeven. Kopieer de sleutelwaarde voordat u het blad verlaat.

   ![API-toegang](./media/connect-with-rest/connect-with-rest03.png)

U waarden voor AD-verbindingsparameters toevoegen aan uw web.config- of app.config-bestand om later in uw code te worden gebruikt.

> [!IMPORTANT]
> De **clientsleutel** is een belangrijk geheim en moet goed worden beveiligd in een sleutelkluis of versleuteld in productie.

## <a name="get-the-access-token-using-postman"></a>Het toegangstoken krijgen met Postman

In deze sectie ziet u hoe **u Postman gebruikt** om een REST-API uit te voeren die een JWT-dragertoken (toegangstoken) retourneert. Als u een MEDIA Services REST API wilt aanroepen, moet u de koptekst 'Autorisatie' toevoegen aan de aanroepen en de waarde van *'Your_access_token*drager' toevoegen aan elke aanroep (zoals in het volgende gedeelte van deze zelfstudie wordt weergegeven). 

1. Open **postbode**.
2. Selecteer **POST**.
3. Voer de URL in die uw tenantnaam bevat met de volgende indeling: de tenantnaam moet eindigen met **.onmicrosoft.com** en de URL moet eindigen met **oauth2/token:** 

    `https://login.microsoftonline.com/{your-aad-tenant-name.onmicrosoft.com}/oauth2/token`

4. Selecteer het tabblad **Kopteksten.**
5. Voer de gegevens **over kopteksten** in met het gegevensraster 'Sleutel/waarde'. 

    ![Gegevensraster](./media/connect-with-rest/headers-data-grid.png)

    U ook klikken op de koppeling **Bulkbewerken** rechts van het venster Postbode en de volgende code plakken.

        Content-Type:application/x-www-form-urlencoded
        Keep-Alive:true

6. Druk op het tabblad **Hoofd.**
7. Voer de hoofdgegevens in met behulp van het gegevensraster 'Sleutel/waarde' (vervang de client-id en geheime waarden). 

    ![Gegevensraster](./media/connect-with-rest/data-grid.png)

    U ook rechts van het venster Postman **op Bulk bewerken** en de volgende hoofdtekst plakken (de client-id en geheime waarden vervangen):

        grant_type:client_credentials
        client_id:{Your Client ID that you got from your Azure AD Application}
        client_secret:{Your client secret that you got from your Azure AD Application's Keys}
        resource:https://rest.media.azure.net

8. Druk op **Verzenden**.

    ![token krijgen](./media/connect-with-rest/connect-with-rest04.png)

Het geretourneerde antwoord bevat het **toegangstoken** dat u moet gebruiken om toegang te krijgen tot ams-API's.

## <a name="test-the-assets-api-using-the-access-token"></a>De **ELEMENTEN-API** testen met behulp van het toegangstoken

In deze sectie ziet u hoe u toegang krijgt tot de **API voor assets** met **Postman**.

1. Open **postbode**.
2. Selecteer **GET**.
3. Het REST API-eindpunt plakken (bijvoorbeeldhttps://amshelloworld.restv2.westus.media.azure.net/api/Assets)
4. Selecteer het tabblad **Autorisatie.** 
5. Selecteer **Token aan toonder**.
6. Plak het token dat in de vorige sectie is gemaakt.

    ![token krijgen](./media/connect-with-rest/connect-with-rest05.png)

    > [!NOTE]
    > De Postman UX kan verschillen tussen een Mac en pc. Als de Mac-versie niet de optie 'Dragertoken' heeft in de vervolgkeuzelijst **verificatie,** moet u de **koptekst Autorisatie** handmatig toevoegen aan de Mac-client.

   ![Auth-koptekst](./media/connect-with-rest/auth-header.png)

7. Selecteer **Kopteksten**.
5. Klik op de koppeling **Bulk bewerken** rechts het venster Postbode.
6. Plak de volgende kopteksten:

        x-ms-version:2.19
        Accept:application/json
        Content-Type:application/json
        DataServiceVersion:3.0
        MaxDataServiceVersion:3.0

7. Druk op **Verzenden**.

Het geretourneerde antwoord bevat de activa die in uw account staan.

## <a name="next-steps"></a>Volgende stappen

* Probeer deze voorbeeldcode in [Azure AD-verificatie voor Azure Media Services Access: beide via REST API](https://github.com/willzhan/WAMSRESTSoln)
* [Bestanden uploaden met .NET](media-services-dotnet-upload-files.md)
