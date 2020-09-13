---
title: Integreren met Logic Apps
titleSuffix: Azure Digital Twins
description: Zie Logic Apps verbinding maken met Azure Digital Apparaatdubbels met behulp van een aangepaste connector
author: baanders
ms.author: baanders
ms.date: 8/14/2020
ms.topic: how-to
ms.service: digital-twins
ms.reviewer: baanders
ms.openlocfilehash: 20959709854f8366cc067437fe86c245fcbc3ef0
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/02/2020
ms.locfileid: "89401058"
---
# <a name="integrate-with-logic-apps-using-a-custom-connector"></a>Integreren met Logic Apps met behulp van een aangepaste connector

[Azure Logic apps](../logic-apps/logic-apps-overview.md) is een Cloud service die u helpt bij het automatiseren van werk stromen in apps en services. Door Logic Apps te verbinden met de Azure Digital Apparaatdubbels Api's, kunt u dergelijke geautomatiseerde stromen maken rond Azure Digital Apparaatdubbels en hun gegevens.

Azure Digital Apparaatdubbels heeft momenteel geen gecertificeerde (vooraf gebouwde) connector voor Logic Apps. In plaats daarvan is het huidige proces voor het gebruik van Logic Apps met Azure Digital Apparaatdubbels een [**aangepaste Logic Apps-Connector**](../logic-apps/custom-connector-overview.md)te maken met behulp van een [aangepaste Azure Digital apparaatdubbels Swagger](https://docs.microsoft.com/samples/azure-samples/digital-twins-custom-swaggers/azure-digital-twins-custom-swaggers/) die is aangepast aan het gebruik van Logic apps.

In dit artikel gaat u de [Azure Portal](https://portal.azure.com) gebruiken om **een aangepaste connector te maken** die kan worden gebruikt om Logic apps te verbinden met een Azure Digital apparaatdubbels-exemplaar. Vervolgens maakt u **een logische app** die gebruikmaakt van deze verbinding voor een voorbeeld scenario, waarbij gebeurtenissen die door een timer worden geactiveerd, automatisch een dubbele update bijwerkt in uw Azure Digital apparaatdubbels-exemplaar. 

## <a name="prerequisites"></a>Vereisten

Als u geen abonnement op Azure hebt, **maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)** voordat u begint.

Meld u aan bij de [Azure Portal](https://portal.azure.com) met dit account.

### <a name="set-up-azure-digital-twins-instance"></a>Een Azure Digital Twins-exemplaar instellen

Als u een Azure Digital Apparaatdubbels-exemplaar wilt verbinden met Logic Apps in dit artikel, moet u de **Azure Digital apparaatdubbels-instantie** al hebben ingesteld. 

Als u nu een nieuw exemplaar moet instellen, kunt u dit het beste doen door een geautomatiseerd implementatie script-voor beeld uit te voeren. Volg de instructies in [*How-to: een exemplaar en authenticatie instellen (met een script)*](how-to-set-up-instance-scripted.md) om een nieuw exemplaar in te stellen en de vereiste Azure AD-app te registreren. De instructies bevatten ook stappen om te controleren of u elke stap hebt voltooid en gereed bent om door te gaan met het nieuwe exemplaar.

In deze zelfstudie hebt u de volgende waarden nodig, van toen u het exemplaar hebt ingesteld. Als u deze waarden opnieuw wilt opzoeken, gebruikt u de koppelingen naar de bijbehorende secties in het artikel over installeren om ze te vinden in de [Azure-portal](https://portal.azure.com).
* Azure Digital Twins-exemplaar **_hostnaam_** ([beschikbaar in de portal](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))
* Azure AD-app-registratie **_Toepassings-id (client)_** ([beschikbaar in de portal](how-to-set-up-instance-portal.md#collect-important-values))
* Azure AD-app-registratie **_Map-id (tenant)_** ([beschikbaar in de portal](how-to-set-up-instance-portal.md#collect-important-values))

### <a name="get-app-registration-client-secret"></a>Client geheim voor registratie van apps ophalen

U moet ook een **_client geheim_** maken voor de registratie van uw Azure AD-app. Als u dit wilt doen, gaat u naar de pagina [app-registraties](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) in het Azure Portal (u kunt deze koppeling gebruiken of ernaar zoeken in de zoek balk van de portal). Selecteer uw registratie in de lijst om de details ervan te openen. 

Klik op *certificaten en geheimen* in het menu van de registratie en selecteer *+ Nieuw client geheim*.

:::image type="content" source="media/how-to-integrate-logic-apps/client-secret.png" alt-text="Portal weergave van een Azure AD-App-registratie. Er is een hooglicht rondom ' certificaten en geheimen ' in het resource menu en een highlight op de pagina rondom ' nieuw client geheim '":::

Voer de gewenste waarden in voor beschrijving en verloopt en klik op *toevoegen*.
Het geheim wordt toegevoegd aan de lijst met client geheimen op de pagina *certificaten en geheimen* . Noteer de waarde die u later wilt gebruiken (u kunt deze ook kopiëren naar het klem bord met het Kopieer pictogram).

### <a name="add-a-digital-twin"></a>Voeg een digitale dubbele

In dit artikel wordt Logic Apps gebruikt voor het bijwerken van een dubbele in uw Azure Digital Apparaatdubbels-exemplaar. Als u wilt door gaan, moet u ten minste één dubbele naam toevoegen aan uw exemplaar. 

U kunt apparaatdubbels toevoegen met behulp van de [DigitalTwins-api's](how-to-use-apis-sdks.md), de [.net (C#) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)of de [Azure Digital apparaatdubbels cli](how-to-use-cli.md). Zie [*How-to: Manage Digital apparaatdubbels*](how-to-manage-twin.md)(Engelstalig) voor gedetailleerde stappen voor het maken van apparaatdubbels met behulp van deze methoden.

U hebt de **_dubbele id_** nodig van een dubbele naam in uw exemplaar dat u hebt gemaakt.

## <a name="create-custom-logic-apps-connector"></a>Aangepaste Logic Apps-Connector maken

In deze stap gaat u een [aangepaste Logic Apps-Connector](../logic-apps/custom-connector-overview.md) maken voor de Azure Digital Apparaatdubbels-api's. Nadat u dit hebt gedaan, kunt u Azure Digital Apparaatdubbels koppelen bij het maken van een logische app in de volgende sectie.

Ga naar de pagina [Logic apps aangepaste connector](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Web%2FcustomApis) in de Azure Portal (u kunt deze koppeling gebruiken of ernaar zoeken in de zoek balk van de portal). Druk op *+ toevoegen*.

:::image type="content" source="media/how-to-integrate-logic-apps/logic-apps-custom-connector.png" alt-text="De pagina ' Logic Apps aangepaste connector ' in de Azure Portal. Markeren rond de knop toevoegen":::

Selecteer uw abonnement en resource groep en een naam en implementatie locatie voor uw nieuwe connector op de pagina *Logic apps aangepaste connector maken* die volgt. Druk op *beoordelen + maken*. Hiermee gaat u naar het tabblad *controleren en maken* , waar u onder *maken* kunt raken om uw resource te maken.

:::image type="content" source="media/how-to-integrate-logic-apps/create-logic-apps-custom-connector.png" alt-text="Het tabblad ' controleren + maken ' van de pagina ' Logic Apps aangepaste connector maken ' in de Azure Portal. Markeren rond de knop maken":::

U wordt naar de implementatie pagina voor de connector geleid. Wanneer de implementatie is voltooid, klikt u op de knop *Ga naar resource* om de details van de connector in de portal weer te geven.

### <a name="configure-connector-for-azure-digital-twins"></a>Connector configureren voor Azure Digital Apparaatdubbels

Vervolgens configureert u de connector die u hebt gemaakt om Azure Digital Apparaatdubbels te bereiken.

Down load eerst een aangepaste Apparaatdubbels Swagger van Azure die is gewijzigd om met Logic Apps te werken. Down load het voor beeld met **aangepaste Swagger-apparaatdubbels van Azure** via [deze koppeling](https://docs.microsoft.com/samples/azure-samples/digital-twins-custom-swaggers/azure-digital-twins-custom-swaggers/) door te klikken op de knop voor het downloaden van het *zip-bestand* . Ga naar de map gedownloade *Azure_Digital_Twins_Custom_Swaggers.zip* en pak deze uit. De aangepaste Swagger voor deze zelf studie bevindt zich op *Azure_Digital_Twins_Custom_Swaggers\LogicApps\preview\2020-05-31-preview\digitaltwins.jsop*.

Ga vervolgens naar de overzichts pagina van uw connector in de [Azure Portal](https://portal.azure.com) en klik op *bewerken*.

:::image type="content" source="media/how-to-integrate-logic-apps/edit-connector.png" alt-text="De pagina overzicht voor de connector die in de vorige stap is gemaakt. Markeren rond de knop bewerken":::

Configureer de volgende gegevens op de pagina *Logic apps aangepaste connector bewerken* :
* **Aangepaste connectors**
    - API-eind punt: REST (laat de standaard instelling)
    - Import modus: OpenAPI-bestand (standaard instelling behouden)
    - Bestand: dit is het aangepaste Swagger-bestand dat u eerder hebt gedownload. Klik op *importeren*, zoek het bestand op uw computer (*Azure_Digital_Twins_Custom_Swaggers\LogicApps\preview\2020-05-31-preview\digitaltwins.jsaan*) en klik op *openen*.
* **Algemene informatie**
    - Pictogram, achtergrond kleur van pictogram, beschrijving: Vul de gewenste waarden in.
    - Schema: HTTPS (standaard instelling behouden)
    - Host: de *hostnaam* van uw Azure Digital apparaatdubbels-exemplaar.
    - Basis-URL:/(standaard instelling behouden)

Klik vervolgens op de knop *beveiliging* aan de onderkant van het venster om door te gaan naar de volgende configuratie stap.

:::image type="content" source="media/how-to-integrate-logic-apps/configure-next.png" alt-text="Scherm afbeelding van de onderkant van de pagina Logic Apps aangepaste connector bewerken. Knop Markeren om door te gaan met beveiliging":::

Klik in de beveiligings stap op *bewerken* en configureer deze gegevens:
* **Verificatie type**: OAuth 2,0
* **OAuth 2,0**:
    - ID-provider: Azure Active Directory
    - Client-ID: de *toepassing (client)-ID* voor uw Azure AD-App-registratie
    - Client geheim: het *client geheim* dat u hebt gemaakt in [*vereisten*](#prerequisites) voor uw Azure AD-App-registratie
    - Aanmeldings-URL: https://login.windows.net (standaard behouden)
    - Tenant-ID: de *Directory-id (Tenant)* voor uw Azure AD-App-registratie
    - Resource-URL: 0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    - Bereik: map. AccessAsUser. all
    - Omleidings-URL: (de standaard instelling voor nu laten staan)

Houd er rekening mee dat het veld omleidings-URL *de aangepaste connector opslaat om de omleidings-URL te genereren*. Doe dit nu door op de pagina *Update connector* boven aan het deel venster te gaan om de connector instellingen te bevestigen.

:::image type="content" source="media/how-to-integrate-logic-apps/update-connector.png" alt-text="Scherm afbeelding van de bovenkant van de pagina Logic Apps aangepaste connector bewerken. De knop ' connector bijwerken ' markeren":::

<!-- Success message? didn't see one -->

Ga terug naar het veld omleidings-URL en kopieer de waarde die is gegenereerd. U gebruikt deze in de volgende stap.

:::image type="content" source="media/how-to-integrate-logic-apps/copy-redirect-url.png" alt-text="Het veld omleidings-URL op de pagina Logic Apps aangepaste connector bewerken heeft nu de waarde ' https://logic-apis-westus2.consent.azure-apim.net/redirect '. De knop voor het kopiëren van de waarde is gemarkeerd.":::

Dit is alle informatie die nodig is voor het maken van de connector (u hoeft de beveiliging van de definitie niet te voortzetten. U kunt het deel venster *aangepaste connector bewerken Logic apps* sluiten.

>[!NOTE]
>Op de overzichts pagina van uw connector waar u oorspronkelijk hebt *geklikt, moet u*er rekening mee houden dat het hele proces van het invoeren van de configuratie *keuzes opnieuw wordt* gestart. Uw waarden worden niet gevuld wanneer u deze voor het laatst hebt door lopen, dus als u een bijgewerkte configuratie met gewijzigde waarden wilt opslaan, moet u alle andere waarden ook opnieuw invoeren om te voor komen dat ze worden overschreven door de standaard instellingen.

### <a name="grant-connector-permissions-in-the-azure-ad-app"></a>Connector machtigingen verlenen in de Azure AD-app

Gebruik vervolgens de *omleidings-URL* -waarde van de aangepaste connector die u in de laatste stap hebt gekopieerd om de connector machtigingen te verlenen in uw Azure AD-App-registratie.

Ga naar de pagina [app-registraties](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) in het Azure Portal en selecteer uw registratie in de lijst. 

Onder *verificatie* vanuit het menu registratie voegt u een URI toe.

:::image type="content" source="media/how-to-integrate-logic-apps/add-uri.png" alt-text="De verificatie pagina voor de registratie van de app in de Azure Portal. ' Authentication ' in het menu is gemarkeerd en op de pagina is de knop een URI toevoegen gemarkeerd."::: 

Voer de *omleidings-URL* van de aangepaste connector in het nieuwe veld in en klik op het pictogram *Opslaan* .

:::image type="content" source="media/how-to-integrate-logic-apps/save-uri.png" alt-text="De verificatie pagina voor de registratie van de app in de Azure Portal. De nieuwe omleidings-URL wordt gemarkeerd en de knop Opslaan voor de pagina.":::

U bent nu klaar met het instellen van een aangepaste connector die toegang heeft tot de Azure Digital Apparaatdubbels-Api's. 

## <a name="create-logic-app"></a>Logische app maken

Vervolgens maakt u een logische app die uw nieuwe connector gebruikt voor het automatiseren van Azure Digital Apparaatdubbels-updates.

Ga naar de pagina [Logic apps](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Logic%2Fworkflows) in het Azure Portal (u kunt deze koppeling gebruiken of ernaar zoeken in de zoek balk van de portal). Druk op *logische app maken*.

:::image type="content" source="media/how-to-integrate-logic-apps/create-logic-app.png" alt-text="De pagina Logic Apps in de Azure Portal. De knop voor het maken van een logische app markeren":::

Selecteer op de pagina *logische app* die volgt uw abonnement en de resource groep en een naam en implementatie locatie voor uw nieuwe logische app. Druk op *beoordelen + maken*. Hiermee gaat u naar het tabblad *controleren en maken* , waar u onder *maken* kunt raken om uw resource te maken.

U gaat naar de implementatie pagina voor de logische app. Wanneer de implementatie is voltooid, klikt u op de knop *Ga naar resource* om door te gaan naar de *Logic apps Designer*, waarin u de logica van de werk stroom moet invullen.

### <a name="design-workflow"></a>Ontwerp werk stroom

Selecteer in de *ontwerp functie voor Logic apps*onder *beginnen met een algemene trigger de*optie _**terugkeer patroon**_.

:::image type="content" source="media/how-to-integrate-logic-apps/logic-apps-designer-recurrence.png" alt-text="De pagina Logic Apps designer in de Azure Portal. Markering rond de algemene trigger ' recurrence '":::

Wijzig de frequentie van het **terugkeer patroon** in de pagina *Logic apps Designer* die volgt op de *tweede*plaats, zodat de gebeurtenis elke 3 seconden wordt geactiveerd. Dit maakt het eenvoudig om de resultaten later te bekijken zonder lang te hoeven wachten.

Druk op *+ nieuwe stap*.

Hiermee opent u een vak *actie kiezen* . Schakel over naar het tabblad *aangepast* . U ziet de aangepaste connector eerder in het bovenste vak.

:::image type="content" source="media/how-to-integrate-logic-apps/custom-action.png" alt-text="Een stroom maken in de Logic Apps designer in de Azure Portal. In het vak Kies een actie selecteert u het tabblad aangepast. De aangepaste connector van de gebruiker uit eerdere versies wordt weer gegeven in het vak, met een markering eromheen.":::

Selecteer deze optie om de lijst met Api's in die connector weer te geven. Gebruik de zoek balk of blader door de lijst om **DigitalTwins_Add**te selecteren. (Dit is de API die in dit artikel wordt gebruikt, maar u kunt ook een andere API selecteren als een geldige keuze voor een Logic Apps verbinding).

U wordt mogelijk gevraagd om u aan te melden met uw Azure-referenties om verbinding te maken met de connector. Als u een dialoog venster *machtigingen hebt aangevraagd* , volgt u de aanwijzingen om toestemming te geven voor uw app en om te accepteren.

Vul in het vak nieuwe *DigitalTwinsAdd* de velden als volgt in:
* id: Vul de *dubbele id* van het digitale dubbele item in uw exemplaar dat u wilt dat de logische app wordt bijgewerkt.
* Item-1: in dit veld voert u de hoofd tekst in die voor de gekozen API-aanvraag nodig is. Voor *DigitalTwinsUpdate*is deze tekst in de vorm van de JSON-patch code. Voor meer informatie over het structureren van een JSON-patch voor het bijwerken van uw twee, raadpleegt u het artikel [een digitale dubbele sectie bijwerken](how-to-manage-twin.md#update-a-digital-twin) van *How-to: Manage Digital apparaatdubbels*.
* API-Version: in de huidige open bare preview is deze waarde *2020-05-31-preview*

Druk op *Opslaan* in de Logic apps Designer.

:::image type="content" source="media/how-to-integrate-logic-apps/save-logic-app.png" alt-text="Voltooide weer gave van de app in de logische app-connector. Het vak DigitalTwinsAdd is gevuld met de waarden die hierboven worden beschreven, inclusief een voor beeld van een JSON-patch voor de toepassing. De knop Opslaan voor het venster is gemarkeerd.":::

## <a name="query-twin-to-see-the-update"></a>Query dubbele om de update te zien

Nu uw logische app is gemaakt, moet de gebeurtenis voor dubbele updates die u hebt gedefinieerd in de Logic Apps Designer een herhaling van elke drie seconden plaatsvinden. Dit betekent dat u na drie seconden een query kunt uitvoeren op uw dubbele gegevens en de nieuwe patches weer geven.

U kunt een query uitvoeren op uw dubbele manier via uw keuze methode (zoals een [aangepaste client-app](tutorial-command-line-app.md), de voor [beeld-app van Azure Digital apparaatdubbels Explorer](https://docs.microsoft.com/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/), de [Sdk's en api's](how-to-use-apis-sdks.md)of de [cli](how-to-use-cli.md)). 

Zie [*How-to: query uitvoeren op de dubbele grafiek*](how-to-query-graph.md)voor meer informatie over het uitvoeren van query's op uw Azure Digital apparaatdubbels-exemplaar.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een logische app gemaakt die regel matig een dubbele update bijwerkt in uw Azure Digital Apparaatdubbels-exemplaar met een patch die u hebt ingevoerd. U kunt proberen om andere Api's te selecteren in de aangepaste connector om Logic Apps te maken voor diverse acties op uw exemplaar.

Ga voor meer informatie over de beschik bare Api's en de details die ze nodig hebben naar [*instructies: gebruik de Azure Digital Apparaatdubbels api's en sdk's*](how-to-use-apis-sdks.md).