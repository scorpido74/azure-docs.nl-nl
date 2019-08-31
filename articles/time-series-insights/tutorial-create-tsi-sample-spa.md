---
title: 'Zelfstudie: Een web-app van Azure Time Series Insights met één pagina maken | Microsoft Docs'
description: Meer informatie over het maken van een webtoepassing met één pagina waarmee gegevens uit een Azure Time Series Insights omgeving worden opgevraagd en weer gegeven.
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: dpalled
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: 031e8074585426584d7ef63a103c9c2b4d90e6c3
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70194218"
---
# <a name="tutorial-create-an-azure-time-series-insights-single-page-web-app"></a>Zelfstudie: Een web-app van Azure Time Series Insights met één pagina maken

Deze zelf studie leidt u door het proces van het maken van uw eigen webtoepassing met één pagina (SPA) voor toegang tot Azure Time Series Insights gegevens.

In deze zelfstudie wordt aandacht besteed aan:

> [!div class="checklist"]
> * Het ontwerp van de toepassing
> * Uw toepassing registreren bij Azure Active Directory (Azure AD)
> * Maken, publiceren en testen van de webtoepassing

> [!NOTE]
> * De bron code voor deze zelf studie vindt u op [github](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial).
> * De Time Series Insights [client](https://insights.timeseries.azure.com/clientsample) -voor beeld-app wordt gehost om de voltooide app weer te geven die in deze zelf studie wordt gebruikt.

Meld u aan voor een [gratis Azure-abonnement](https://azure.microsoft.com/free/) als u er nog geen hebt.

## <a name="prerequisites"></a>Vereisten

* Een gratis exemplaar van Visual Studio. Down load de versies van de [2017-of 2019-Community](https://www.visualstudio.com/downloads/) om aan de slag te gaan.

* De onderdelen van de IIS Express, Web Deploy en Azure Cloud Services Core-hulpprogram ma's voor Visual Studio. Voeg de onderdelen toe door de installatie van Visual Studio te wijzigen.

## <a name="understand-application-design"></a>Toepassings ontwerp begrijpen

De Time Series Insights voor beeld-beveiligd-wachtwoord verificatie is de basis voor het ontwerp en de code die in deze zelf studie wordt gebruikt. De code maakt gebruik van de Time Series Insights java script-client bibliotheek. De Time Series Insights-client bibliotheek biedt een abstractie voor twee hoofd-API-Categorieën:

- **Wrapper-methoden voor het aanroepen van de time series Insights query-api's**: REST-Api's die u kunt gebruiken om Time Series Insights gegevens op te vragen met behulp van JSON-expressies. De methoden zijn ingedeeld onder de TsiClient. server-naam ruimte van de bibliotheek.

- **Methoden voor het maken en vullen van verschillende typen grafiekbesturingselementen**: Methoden die u kunt gebruiken voor het visualiseren van Time Series Insights gegevens op een webpagina. De methoden zijn ingedeeld onder de naam ruimte TsiClient. UX van de-bibliotheek.

In deze zelf studie wordt ook gebruikgemaakt van gegevens uit de Time Series Insights omgeving van de voorbeeld toepassing. Raadpleeg de zelf studie [de Azure time series Insights java script-client bibliotheek](tutorial-explore-js-client-lib.md)voor meer informatie over de structuur van de voorbeeld toepassing time series Insights en het gebruik van de time series Insights-client bibliotheek.

## <a name="register-with-azure-ad"></a>Registreren bij Azure AD

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

## <a name="build-and-publish"></a>Bouwen en publiceren

1. Maak een map voor het opslaan van de projectbestanden van uw toepassing. Ga vervolgens naar de volgende Url's. Klik met de rechter muisknop op de onbewerkte koppeling in de rechter bovenhoek van de pagina en selecteer vervolgens **Opslaan als** om de bestanden in de projectmap op te slaan.

   - [*index. html*](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html): de HTML en Java script voor de pagina
   - [*sampleStyles. CSS*](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/sampleStyles.css): het CSS-opmaak model

   > [!NOTE]
   > Afhankelijk van de browser moet u de bestands extensies mogelijk wijzigen in. html of. CSS voordat u het bestand opslaat.

1. Controleer of de vereiste onderdelen zijn geïnstalleerd in Visual Studio. De onderdelen van de IIS Express, Web Deploy en Azure Cloud Services Core-hulpprogram ma's voor Visual Studio moeten zijn geïnstalleerd.

    [![Visual Studio: de geïnstalleerde onderdelen aanpassen](media/tutorial-create-tsi-sample-spa/vs-installation.png)](media/tutorial-create-tsi-sample-spa/vs-installation.png#lightbox)

    > [!NOTE]
    > Uw ervaring met Visual Studio kan enigszins afwijken van de voor beelden, afhankelijk van uw versie en configuratie-instellingen.

1. Open Visual Studio en meld u aan. Als u een project voor de webtoepassing wilt maken, selecteert u in het menu **bestand** de optie**website** **openen** > .

    [![Visual Studio-een nieuwe oplossing maken](media/tutorial-create-tsi-sample-spa/vs-solution-create.png)](media/tutorial-create-tsi-sample-spa/vs-solution-create.png#lightbox)

1. Selecteer in het deel venster **website openen** de werkmap waarin u de HTML-en CSS-bestanden hebt opgeslagen en selecteer vervolgens **openen**.

   [![Visual Studio: het menu bestand, met de opties openen en website](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png)](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png#lightbox)

1. Selecteer in het menu **beeld** van Visual Studio **Solution Explorer**. De nieuwe oplossing wordt geopend. Het bevat een website project (Globe icon), dat de HTML-en CSS-bestanden bevat.

   [![Visual Studio: de nieuwe oplossing in Solution Explorer](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png)](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png#lightbox)

1. Voordat u uw app publiceert, moet u de configuratie-instellingen wijzigen in *index. html*.

   1. Verwijder de opmerking bij de drie regels onder `"PROD RESOURCE LINKS"` de opmerking om de afhankelijkheden van ontwikkeling naar productie over te scha kelen. Commentaar bij de drie regels onder de opmerking `"DEV RESOURCE LINKS"`.

      [!code-html[head-sample](~/samples-javascript/pages/tutorial/index.html?range=2-20&highlight=10-13,15-18)]

      Uw afhankelijkheden moeten worden gemarkeerd als in het volgende voor beeld:

      ```HTML
      <!-- PROD RESOURCE LINKS -->
      <link rel="stylesheet" type="text/css" href="./sampleStyles.css">
      <script src="https://unpkg.com/tsiclient@1.1.4/tsiclient.js"></script>
      <link rel="stylesheet" type="text/css" href="https://unpkg.com/tsiclient@1.1.4/tsiclient.css">

      <!-- DEV RESOURCE LINKS -->
      <!-- <link rel="stylesheet" type="text/css" href="./sampleStyles.css">
      <script src="../../dist/tsiclient.js"></script>
      <link rel="stylesheet" type="text/css" href="../../dist/tsiclient.css"> -->
      ```

   1. Als u de app wilt configureren voor het gebruik van de registratie-id van `clientID` uw Azure AD-app, wijzigt u de waarde voor het gebruik van de **toepassings-id** die u in **stap 3** hebt gekopieerd, wanneer u [de toepassing hebt geregistreerd voor gebruik van Azure AD](#register-with-azure-ad). Als u een afmeldings- **URL** hebt gemaakt in azure AD, stelt u `postLogoutRedirectUri` deze waarde in als de waarde.

      [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-153&highlight=4-5)]

      Bijvoorbeeld:

      ```javascript
      clientId: '8884d4ca-b9e7-403a-bd8a-366d0ce0d460',
      postLogoutRedirectUri: 'https://tsispaapp.azurewebsites.net',
      ```

   1. Sla *index. html*op wanneer u klaar bent met het maken van wijzigingen.

1. Publiceer de webtoepassing in uw Azure-abonnement als een Azure App Service.  

   > [!NOTE]
   > Verschillende opties in de scherm afbeeldingen die in de volgende stappen worden weer gegeven, worden automatisch ingevuld met gegevens uit uw Azure-abonnement. Het kan een paar seconden duren voordat elk deel venster volledig wordt geladen.  

   1. Klik in Solution Explorer met de rechter muisknop op het knoop punt website project en selecteer vervolgens **Web-app publiceren**.  

      [![Visual Studio: Selecteer de Solution Explorer optie Web-app publiceren](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png)](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png#lightbox)

   1. Selecteer **Start** om te beginnen met het publiceren van uw app.

      [![Visual Studio: het deel venster voor het publicatie profiel](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png#lightbox)

   1. Selecteer deze optie om een nieuw exemplaar van Azure App Service te publiceren of een bestaande te gebruiken.

      [![Een Azure App Service exemplaar selecteren](media/tutorial-create-tsi-sample-spa/vs-publish-select-target.png)](media/tutorial-create-tsi-sample-spa/vs-publish-select-target.png#lightbox)

   1. Selecteer het abonnement dat u wilt gebruiken om de toepassing te publiceren. Selecteer het **TsiSpaApp** -project. Selecteer vervolgens **OK**.

      [![Visual Studio: het App Service venster voor het publicatie profiel](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png#lightbox)

   1. Selecteer **publiceren** om de webtoepassing te implementeren.

      [![Visual Studio: de optie publiceren en de uitvoer van het publicatie logboek](media/tutorial-create-tsi-sample-spa/vs-publish-profile-output.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-output.png#lightbox)

   1. Er wordt een geslaagd publicatie logboek weer gegeven in het deel venster Visual Studio- **uitvoer** . Wanneer de implementatie is voltooid, opent Visual Studio de webtoepassing in een browser tabblad en wordt u gevraagd zich aan te melden. Nadat het aanmelden is geslaagd, worden de Time Series Insights besturings elementen gevuld met gegevens.

   1. Navigeer naar uw web-app en meld u aan om de gerenderde Time Series Insights visuele gegevens weer te geven.

      [![De gehoste web-app controleren](media/tutorial-create-tsi-sample-spa/vs-publish-hosted-app.png)](media/tutorial-create-tsi-sample-spa/vs-publish-hosted-app.png#lightbox)

## <a name="troubleshoot"></a>Problemen oplossen  

Foutcode/-conditie | Description
---------------------| -----------
*AADSTS50011: No reply address is registered for the application.* | Er ontbreekt een omleidings- **URI** -eigenschap in de Azure AD-registratie. Ga naar de omleidings-**uri's** voor **verificatie** > voor uw Azure AD-toepassings registratie. Controleer of de omleidings- **URI** die u in **stap 2** of **stap 4** hebt opgegeven bij het registreren van [de toepassing voor gebruik van Azure AD](#register-with-azure-ad) aanwezig is.
*AADSTS50011: De antwoord-URL die in de aanvraag is opgegeven, komt niet overeen met de antwoord-url's die zijn geconfigureerd voor de toepassing: '\<Toepassings-id-GUID > '.* | Het `postLogoutRedirectUri` opgegeven in **stap 6. b** bij [het bouwen en publiceren van de webtoepassing](#build-and-publish) moet overeenkomen met de waarde die is opgegeven onder **Authentication** > omleidings-**uri's** in de registratie van uw Azure AD-toepassing. |
De webtoepassing wordt geladen, maar heeft een onopgemaakte, alleen tekst-aanmeld pagina met een witte achtergrond. | Controleer of de paden die in **stap 6** zijn beschreven in [de webtoepassing bouwen en publiceren](#build-and-publish) juist zijn. Als de webtoepassing de CSS-bestanden niet kan vinden, wordt de pagina niet goed opgemaakt.

## <a name="clean-up-resources"></a>Resources opschonen

In deze zelfstudie worden verschillende actieve Azure-services gemaakt. Als u niet van plan bent om deze reeks zelf studies af te ronden, raden we u aan om alle resources te verwijderen om onnodige kosten te vermijden.

In het Azure Portal menu links:

1. Selecteer **resource groepen**en selecteer vervolgens de resource groep die u hebt gemaakt voor de time series Insights omgeving. Selecteer boven aan de pagina **resource groep verwijderen**, voer de naam van de resource groep in en selecteer **verwijderen**.
1. Selecteer **resource groepen**en selecteer vervolgens de resource groep die is gemaakt door de apparaat simulatie oplossings versneller. Selecteer boven aan de pagina **resource groep verwijderen**, voer de naam van de resource groep in en selecteer **verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Het ontwerp van de toepassing
> * Uw toepassing registreren bij Azure AD
> * Maken, publiceren en testen van de webtoepassing

Deze zelf studie wordt geïntegreerd met Azure AD en maakt gebruik van de identiteit van de gebruiker die is aangemeld om een toegangs token te verkrijgen. Zie dit artikel voor meer informatie over het openen van de Time Series Insights-API met behulp van de identiteit van een service-of daemon-toepassing:

> [!div class="nextstepaction"]
> [Verificatie en autorisatie voor Azure Time Series Insights-API](time-series-insights-authentication-and-authorization.md)
