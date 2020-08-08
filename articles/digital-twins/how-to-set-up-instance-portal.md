---
title: Een exemplaar en verificatie instellen (portal)
titleSuffix: Azure Digital Twins
description: Zie een exemplaar van de Azure Digital Apparaatdubbels-service instellen met behulp van de Azure Portal
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 33772d46f363b161c1faa5c953f48a702ae2b8bf
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/07/2020
ms.locfileid: "88009642"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-portal"></a>Een Azure Digital Apparaatdubbels-exemplaar en-verificatie (Portal) instellen

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

In dit artikel worden de stappen beschreven voor het **instellen van een nieuwe Azure Digital apparaatdubbels-instantie**, inclusief het maken van het exemplaar en het instellen van verificatie. Nadat dit artikel is voltooid, hebt u een Azure Digital Apparaatdubbels-exemplaar gereed om te Program meren.

Deze versie van dit artikel doorloopt deze stappen hand matig, één voor één, met behulp van de Azure Portal. De Azure-portal is een geïntegreerde web-console die een alternatief vormt voor opdrachtregelprogramma's.
* Als u deze stappen hand matig wilt door lopen met de CLI, raadpleegt u de CLI-versie van dit artikel: [*instructies: een exemplaar en authenticatie instellen (CLI)*](how-to-set-up-instance-cli.md).
* Als u via een automatische installatie wilt uitvoeren met behulp van een voor beeld van een implementatie script, raadpleegt u de script versie van dit artikel: [*instructies: een exemplaar en authenticatie instellen (script)*](how-to-set-up-instance-scripted.md).

[!INCLUDE [digital-twins-setup-steps-prereq.md](../../includes/digital-twins-setup-steps-prereq.md)]

## <a name="create-the-azure-digital-twins-instance"></a>Het Azure Digital Apparaatdubbels-exemplaar maken

In deze sectie **maakt u een nieuw exemplaar van Azure Digital apparaatdubbels** met behulp van de [Azure Portal](https://ms.portal.azure.com/). Ga naar de portal en meld u aan met uw referenties.

In de portal begint u met het selecteren van _een resource maken_ in het menu Start pagina van Azure Services.

:::image type="content" source= "media/how-to-set-up-instance/portal/create-resource.png" alt-text="Selecteren van ' een resource maken ' op de start pagina van de Azure Portal":::

Zoek in het zoekvak naar *Azure Digital apparaatdubbels* en kies de **Azure Digital apparaatdubbels-service (preview)** in de resultaten. Selecteer de knop _maken_ om een nieuw exemplaar van de service te maken.

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins.png" alt-text="' Maken ' selecteren op de pagina van de Azure Digital Apparaatdubbels-service":::

Vul op de pagina de volgende *resource maken* de waarden in die hieronder zijn opgegeven:
* **Abonnement**: het Azure-abonnement dat u gebruikt
  - **Resource groep**: een resource groep waarin het exemplaar moet worden geïmplementeerd. Als u nog geen bestaande resource groep hebt, kunt u hier een maken door de *nieuwe koppeling maken* te selecteren en een naam in te voeren voor een nieuwe resource groep.
* **Locatie**: een Azure Digital apparaatdubbels-regio voor de implementatie. Ga voor meer informatie over regionale ondersteuning naar [*Azure-producten die beschikbaar zijn per regio (Azure Digital apparaatdubbels)*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
* **Resource naam**: een naam voor uw Azure Digital apparaatdubbels-exemplaar. De naam van het nieuwe exemplaar moet uniek zijn binnen de regio voor uw abonnement (wat betekent dat als uw abonnement een ander Azure Digital Apparaatdubbels-exemplaar heeft in de regio die al gebruikmaakt van de naam die u kiest, wordt u gevraagd een andere naam te kiezen).

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins-2.png" alt-text="De beschreven waarden invullen om een Azure Digital Apparaatdubbels-resource te maken":::

Wanneer u klaar bent, selecteert u _controleren + maken_. Hiermee gaat u naar een overzichts pagina waar u de exemplaar gegevens kunt bekijken die u hebt ingevoerd en op _maken_hebt geklikt. 

### <a name="verify-success-and-collect-important-values"></a>Geslaagde pogingen controleren en belang rijke waarden verzamelen

Nadat u het *maken*hebt gepusht, kunt u de status van de implementatie van uw exemplaar bekijken in uw Azure-meldingen via de portal pictogram balk. De melding geeft aan wanneer de implementatie is voltooid en u kunt de knop _Ga naar resource_ selecteren om uw gemaakte exemplaar weer te geven.

:::image type="content" source="media/how-to-set-up-instance/portal/notifications-deployment.png" alt-text="Weer gave van meldingen van Azure met een geslaagde implementatie en het markeren van de knop ' go to resource '":::

Als de implementatie mislukt, wordt de melding ook aangegeven waarom. Bekijk het advies van het fout bericht en probeer het exemplaar opnieuw te maken.

>[!TIP]
>Als uw exemplaar eenmaal is gemaakt, kunt u op elk gewenst moment teruggaan naar de pagina door te zoeken naar de naam van uw instantie in de zoek balk van Azure Portal.

Noteer de *naam*, de *resource groep*en de *hostnaam*op de pagina *overzicht* van het exemplaar. Dit zijn alle belang rijke waarden die u mogelijk nodig hebt om te werken met uw Azure Digital Apparaatdubbels-exemplaar. Als andere gebruikers op het exemplaar worden geprogrammeerd, moet u deze waarden met hen delen.

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="Markeren van de belang rijke waarden van de overzichts pagina van het exemplaar":::

U hebt nu een Azure Digital Apparaatdubbels-exemplaar klaar om te gaan. Vervolgens geeft u de juiste Azure-gebruikers machtigingen om deze te beheren.

## <a name="set-up-user-access-permissions"></a>Machtigingen voor gebruikers toegang instellen

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

Open eerst de pagina voor uw Azure Digital Apparaatdubbels-exemplaar in het Azure Portal. Selecteer in het menu van de instantie *toegangs beheer (IAM)*. Selecteer de knop *toevoegen* onder *een roltoewijzing toevoegen*.

:::image type="content" source="media/how-to-set-up-instance/portal/add-role-assignment-1.png" alt-text="Selecteren om een roltoewijzing toe te voegen op de pagina toegangs beheer (IAM)":::

Vul de waarden in op de volgende pagina *roltoewijzing toevoegen* (moet worden voltooid door een gebruiker met [voldoende machtigingen](#prerequisites-permission-requirements) in het Azure-abonnement):
* **Rol**: Selecteer de *Azure Digital apparaatdubbels-eigenaar (preview)* in het vervolg keuzemenu
* **Toegang toewijzen aan**: Selecteer *Azure AD-gebruiker,-groep of Service-Principal* in het vervolg keuzemenu
* **Selecteer**: Zoek de naam of het e-mail adres van de gebruiker die u wilt toewijzen. Wanneer u het resultaat selecteert, wordt de gebruiker weer gegeven in de sectie *geselecteerde leden* .

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-set-up-instance/portal/add-role-assignment-2.png" alt-text="De vermelde velden invullen in het dialoog venster functie toewijzing toevoegen":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Wanneer u klaar bent met het invoeren van de details, klikt u op de knop *Opslaan* .

### <a name="verify-success"></a>Controleren geslaagd

U kunt de roltoewijzing weer geven die u hebt ingesteld onder *toegangs beheer (IAM) > roltoewijzingen*. De gebruiker moet in de lijst worden weer gegeven met een rol van de *Azure Digital apparaatdubbels-eigenaar (preview)*. 

:::image type="content" source="media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="Weer gave van de roltoewijzingen voor een Azure Digital Apparaatdubbels-exemplaar in Azure Portal":::

U hebt nu een Azure Digital Apparaatdubbels-exemplaar klaar om te gaan, en u hebt machtigingen toegewezen om het te beheren. Vervolgens stelt u de machtigingen voor een client-app in om deze te openen.

## <a name="set-up-access-permissions-for-client-applications"></a>Toegangs machtigingen voor client toepassingen instellen

[!INCLUDE [digital-twins-setup-app-registration.md](../../includes/digital-twins-setup-app-registration.md)]

Begin met het navigeren naar [Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) in het Azure Portal (u kunt deze koppeling gebruiken of zoeken naar de portal-zoek balk). Selecteer *app-registraties* in het menu service en klik vervolgens op *+ nieuwe registratie*.

:::image type="content" source="media/how-to-set-up-instance/portal/new-registration.png" alt-text="Weer gave van de Azure AD-service pagina in de Azure Portal, waarbij u de menu optie App-registraties en de knop + nieuwe registratie markeert":::

Vul op de pagina *een toepassing registreren* de gevraagde waarden in:
* **Naam**: een weergave naam voor de Azure AD-toepassing die u aan de registratie wilt koppelen
* **Ondersteunde account typen**: Selecteer *accounts in deze organisatie-Directory alleen (alleen standaard mappen-één Tenant)*
* **Omleidings-URI**: een antwoord-URL voor de *Azure AD-toepassing* voor de Azure AD-toepassing. U kunt gebruiken `http://localhost` .

Wanneer u klaar bent, klikt u op de knop *registreren* .

:::image type="content" source="media/how-to-set-up-instance/portal/register-an-application.png" alt-text="Weer gave van de pagina een toepassing registreren met de beschreven waarden":::

Wanneer de registratie is voltooid, wordt u doorgestuurd naar de pagina met details van de portal.

### <a name="provide-azure-digital-twins-api-permission"></a>Azure Digital Apparaatdubbels API-machtiging bieden

Vervolgens configureert u de app-registratie die u hebt gemaakt met basislijn machtigingen voor de Azure Digital Apparaatdubbels-Api's.

Selecteer vanuit de portal pagina voor de registratie van uw app *API-machtigingen* in het menu. Op de volgende machtigingen pagina, klikt u op de knop *+ een machtiging toevoegen* .

:::image type="content" source="media/how-to-set-up-instance/portal/add-permission.png" alt-text="Weer gave van de app-registratie in de Azure Portal, waarbij u de menu optie API-machtigingen en de knop + een machtiging toevoegen selecteert":::

Schakel op de pagina *API-machtigingen voor aanvragen* de volgende opdracht uit naar de api's die *Mijn organisatie gebruikt* en zoek naar *Azure Digital apparaatdubbels*. Selecteer *Azure Digital apparaatdubbels* in de zoek resultaten om machtigingen toe te wijzen voor de Azure Digital Apparaatdubbels-api's.

:::image type="content" source="media/how-to-set-up-instance/portal/request-api-permissions-1.png" alt-text="Weer gave van het Zoek resultaat van de pagina API-machtigingen aanvragen met Azure Digital Apparaatdubbels":::

Vervolgens selecteert u de machtigingen die u voor deze Api's wilt verlenen. Vouw de machtiging **Read (1)** uit en schakel het selectie vakje *lezen. schrijven* in om deze app-registratie lezer en schrijf machtigingen te verlenen.

:::image type="content" source="media/how-to-set-up-instance/portal/request-api-permissions-2.png" alt-text="Weer gave van de pagina ' API-machtigingen aanvragen ' om ' Lees. schrijf machtigingen ' te selecteren voor de Azure Digital Apparaatdubbels-Api's":::

Klik op *machtigingen toevoegen* wanneer u klaar bent.

### <a name="verify-success"></a>Controleren geslaagd

Ga terug naar de pagina *API-machtigingen* en controleer of er nu een vermelding voor de machtigingen voor lezen/schrijven wordt weer gegeven in azure Digital apparaatdubbels:

:::image type="content" source="media/how-to-set-up-instance/portal/verify-api-permissions.png" alt-text="Portal weergave van de API-machtigingen voor de Azure AD-App-registratie, met lees-en schrijf toegang voor Azure Digital Apparaatdubbels":::

U kunt ook de verbinding met Azure Digital Apparaatdubbels in de *manifest.jsvan*de app-registratie controleren, die automatisch is bijgewerkt met de gegevens van de Azure Digital apparaatdubbels wanneer u de API-machtigingen hebt toegevoegd.

Als u dit wilt doen, selecteert u *manifest* in het menu om de manifest code van de app-registratie weer te geven. Ga naar de onderkant van het code venster en zoek deze velden onder `requiredResourceAccess` . De waarden moeten overeenkomen met die in de onderstaande scherm afbeelding:

:::image type="content" source="media/how-to-set-up-instance/portal/verify-manifest.png" alt-text="Portal weergave van het manifest voor de registratie van de Azure AD-app. Genest onder ' requiredResourceAccess ', is de waarde ' resourceAppId ' van 0b07f429-9f4b-4714-9392-cc5e8e80c8b0 en een ' resourceAccess > id-waarde van 4589bd03-58cb-4e6c-b17f-b580e39652f8":::

### <a name="collect-important-values"></a>Belang rijke waarden verzamelen

Selecteer vervolgens *overzicht* in de menu balk om de details van de app-registratie te bekijken:

:::image type="content" source="media/how-to-set-up-instance/portal/app-important-values.png" alt-text="Portal weergave van de belang rijke waarden voor de app-registratie":::

Noteer de ID van de *toepassings* -id en de *Directory (Tenant)* die op **de** pagina wordt weer gegeven. Deze waarden zijn later nodig om [een client-app te verifiëren tegen de Azure Digital apparaatdubbels-api's](how-to-authenticate-client.md). Als u niet de persoon bent die code gaat schrijven voor dergelijke toepassingen, moet u deze waarden delen met de persoon die u wilt.

### <a name="other-possible-steps-for-your-organization"></a>Andere mogelijke stappen voor uw organisatie

[!INCLUDE [digital-twins-setup-additional-requirements.md](../../includes/digital-twins-setup-additional-requirements.md)]

## <a name="next-steps"></a>Volgende stappen

Bekijk hoe u uw client toepassing verbindt met uw exemplaar door de verificatie code van de client-app te schrijven:
* [*Instructies: app-verificatie code schrijven*](how-to-authenticate-client.md)