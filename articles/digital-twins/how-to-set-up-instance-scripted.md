---
title: Een exemplaar en verificatie instellen (met een script)
titleSuffix: Azure Digital Twins
description: Zie een exemplaar van de Azure Digital Apparaatdubbels-service instellen door een script voor automatische implementatie uit te voeren
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 63bc46f679b71f6965cda8f9db800a125683c093
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/02/2020
ms.locfileid: "89298280"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-scripted"></a>Een Azure Digital Apparaatdubbels-exemplaar en-verificatie instellen (met een script)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

In dit artikel worden de stappen beschreven voor het **instellen van een nieuwe Azure Digital apparaatdubbels-instantie**, inclusief het maken van het exemplaar en het instellen van verificatie. Nadat dit artikel is voltooid, hebt u een Azure Digital Apparaatdubbels-exemplaar gereed om te Program meren.

Deze versie van dit artikel voert u deze stappen uit door een voor beeld van een [ **geautomatiseerd implementatie script** ](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples/) uit te voeren dat het proces stroomlijnt. 
* Zie de CLI-versie van dit artikel voor informatie over de hand matige CLI-stappen die door het script worden uitgevoerd achter de schermen: [*instructies: een exemplaar en authenticatie instellen (CLI)*](how-to-set-up-instance-cli.md).
* Als u de hand matige stappen wilt bekijken volgens de Azure Portal, raadpleegt u de portal versie van dit artikel: [*instructies: een exemplaar en authenticatie instellen (Portal)*](how-to-set-up-instance-portal.md).

[!INCLUDE [digital-twins-setup-steps-prereq.md](../../includes/digital-twins-setup-steps-prereq.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="run-the-deployment-script"></a>Het implementatie script uitvoeren

In dit artikel wordt een voor beeld van een Azure Digital Apparaatdubbels-code gebruikt om een Azure Digital Apparaatdubbels-exemplaar en de vereiste verificatie semi-automatisch te implementeren. Het kan ook worden gebruikt als uitgangs punt voor het schrijven van uw eigen interscript-interacties.

Het voorbeeld script is geschreven in Power shell. Het maakt deel uit van de [Azure Digital apparaatdubbels](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples/)-voor beelden, die u kunt downloaden naar uw computer door te navigeren naar de voorbeeld koppeling en de knop *zip downloaden* te selecteren onder de titel.

In de gedownloade voorbeeld map bevindt het implementatie script zich in _Azure_Digital_Twins_samples.zip > scripts > **deploy.ps1** _.

Hier volgen de stappen voor het uitvoeren van het implementatie script in Cloud Shell.
1. Ga naar een [Azure Cloud shell](https://shell.azure.com/) -venster in uw browser. Meld u aan met deze opdracht:
    ```azurecli-interactive
    az login
    ```
    Als de CLI uw standaardbrowser kan openen, gebeurt dat ook en wordt er een Azure-aanmeldingspagina geladen. Als dat niet het geval is, opent u een browser pagina op *https://aka.ms/devicelogin* en voert u de autorisatie code in die wordt weer gegeven in uw Terminal.
 
2. Nadat u zich hebt aangemeld, gaat u naar de pictogram balk van het Cloud Shell venster. Selecteer het pictogram bestanden uploaden/downloaden en kies uploaden.

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="Cloud Shell venster met de selectie van de Upload optie":::

    Ga naar het _**deploy.ps1**_ -bestand op uw computer en klik op openen. Hiermee wordt het bestand geüpload naar Cloud Shell, zodat u het kunt uitvoeren in het Cloud Shell-venster.

3. Voer het script uit door de `./deploy.ps1` opdracht in het venster Cloud shell te verzenden. Wanneer het script wordt uitgevoerd via de automatische installatie stappen, wordt u gevraagd de volgende waarden door te geven:
    * Voor het exemplaar: de *abonnements-id* van uw Azure-abonnement dat moet worden gebruikt
    * Voor het exemplaar: een *locatie* waar u het exemplaar wilt implementeren. Als u wilt zien welke regio's Azure Digital Apparaatdubbels ondersteunen, gaat u naar [*Azure-producten beschikbaar per regio*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
    * Voor het exemplaar: een naam van een *resource groep* . U kunt een bestaande resource groep gebruiken of een nieuwe naam opgeven om deze te maken.
    * Voor het exemplaar: een *naam* voor uw Azure Digital apparaatdubbels-exemplaar. De naam van het nieuwe exemplaar moet uniek zijn binnen de regio voor uw abonnement (wat betekent dat als uw abonnement een ander Azure Digital Apparaatdubbels-exemplaar heeft in de regio die al gebruikmaakt van de naam die u kiest, wordt u gevraagd een andere naam te kiezen).
    * Voor de app-registratie: een *weergave naam van de Azure AD-toepassing* die u aan de registratie wilt koppelen. Met deze app-registratie kunt u toegangs machtigingen voor de [Azure Digital apparaatdubbels-api's](how-to-use-apis-sdks.md)configureren. Later wordt de client-app geverifieerd op basis van de app-registratie, en worden daarom de geconfigureerde toegangs machtigingen voor de Api's verleend.
    * Voor de app-registratie: een antwoord-URL voor de *Azure AD-toepassing* voor de Azure AD-toepassing. Gebruik `http://localhost`. Met het script wordt een URI voor een *open bare client/systeem eigen (mobile & Desktop)* ingesteld.

Met het script maakt u een Azure Digital Apparaatdubbels-exemplaar, wijst u uw Azure-gebruiker de rol *Azure Digital apparaatdubbels-eigenaar (preview)* toe aan het exemplaar en stelt u een Azure AD-App-registratie in voor uw client-app.

>[!NOTE]
>Er is momenteel een **bekend probleem** met het instellen van een script, waarbij sommige gebruikers (met name gebruikers van persoonlijke [micro soft-accounts (msa's)](https://account.microsoft.com/account)) de roltoewijzing kunnen vinden **voor de _Azure Digital apparaatdubbels-eigenaar (preview)_ is niet gemaakt**.
>
>U kunt de roltoewijzing controleren met het gedeelte [*toewijzing*](#verify-user-role-assignment) van gebruikersrol controleren verderop in dit artikel, en, indien nodig, de roltoewijzing hand matig instellen met behulp van de [Azure Portal](how-to-set-up-instance-portal.md#set-up-user-access-permissions) of [cli](how-to-set-up-instance-cli.md#set-up-user-access-permissions).
>
>Zie [*probleem oplossing: bekende problemen in azure Digital apparaatdubbels*](troubleshoot-known-issues.md#missing-role-assignment-after-scripted-setup)voor meer informatie over dit probleem.

Hier volgt een fragment van het uitvoer logboek van het script:

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/deployment-script-output.png" alt-text="Cloud Shell venster met het logboek van de invoer en uitvoer via het uitvoeren van het script implementeren" lightbox="media/how-to-set-up-instance/cloud-shell/deployment-script-output.png":::

Als het script is voltooid, wordt de uiteindelijke afdruk weer te zeggen `Deployment completed successfully` . Als dat niet het geval is, adresseert u het fout bericht en voert u het script opnieuw uit. De stappen die u al hebt voltooid, worden overgeslagen en de invoer wordt opnieuw gestart op het punt waar u was gebleven.

Wanneer het script is voltooid, hebt u nu een Azure Digital Apparaatdubbels-exemplaar klaar om te gaan met de machtigingen om het te beheren en hebt u machtigingen voor een client-app ingesteld voor toegang.

> [!NOTE]
> Met het script wordt momenteel de vereiste beheer functie in azure Digital Apparaatdubbels (*Azure Digital Apparaatdubbels owner)* toegewezen aan dezelfde gebruiker die het script uitvoert vanuit Cloud shell. Als u deze rol moet toewijzen aan iemand anders die het exemplaar gaat beheren, kunt u dit nu doen via de Azure Portal ([instructies](how-to-set-up-instance-portal.md#set-up-user-access-permissions)) of cli ([instructies](how-to-set-up-instance-cli.md#set-up-user-access-permissions)).

## <a name="collect-important-values"></a>Belang rijke waarden verzamelen

Er zijn verschillende belang rijke waarden van de resources die door het script worden ingesteld, die u mogelijk nodig hebt om te werken met uw Azure Digital Apparaatdubbels-exemplaar. In deze sectie gebruikt u de [Azure Portal](https://portal.azure.com) om deze waarden te verzamelen. Sla deze op een veilige plaats op of ga terug naar deze sectie om deze later te vinden wanneer u ze nodig hebt.

Als andere gebruikers op het exemplaar worden geprogrammeerd, moet u deze waarden ook met hen delen.

### <a name="collect-instance-values"></a>Instantie waarden verzamelen

Zoek in het [Azure Portal](https://portal.azure.com)naar uw Azure Digital apparaatdubbels-exemplaar door te zoeken naar de naam van uw exemplaar in de zoek balk van de portal.

Als u deze optie selecteert, wordt de *overzichts* pagina van het exemplaar geopend. Noteer de *naam*, de *resource groep*en de *hostnaam*. U hebt deze mogelijk later nodig om uw exemplaar te identificeren en er verbinding mee te maken.

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="Markeren van de belang rijke waarden van de overzichts pagina van het exemplaar":::

### <a name="collect-app-registration-values"></a>App-registratie waarden verzamelen 

Er zijn twee belang rijke waarden van de app-registratie die u later nodig hebt om [de client-app-verificatie code te schrijven voor de Azure Digital apparaatdubbels-api's](how-to-authenticate-client.md). 

Als u deze wilt vinden, volgt u [deze koppeling](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) om naar de pagina overzicht van Azure AD-App-registratie in de Azure portal te gaan. Op deze pagina worden alle app-registraties weer gegeven die zijn gemaakt in uw abonnement.

De registratie van de app die u zojuist hebt gemaakt, ziet u in deze lijst. Selecteer deze om de details ervan te openen:

:::image type="content" source="media/how-to-set-up-instance/portal/app-important-values.png" alt-text="Portal weergave van de belang rijke waarden voor de app-registratie":::

Noteer de ID van de *toepassings* -id en de *Directory (Tenant)* die op **de** pagina wordt weer gegeven. Als u niet de persoon bent die code gaat schrijven voor client toepassingen, moet u deze waarden delen met de persoon die het gaat doen.

## <a name="verify-success"></a>Controleren geslaagd

Als u wilt controleren of uw resources en machtigingen zijn ingesteld door het script, kunt u deze bekijken in de [Azure Portal](https://portal.azure.com).

### <a name="verify-instance"></a>Exemplaar controleren

Als u wilt controleren of uw exemplaar is gemaakt, gaat u naar de [pagina Azure Digital apparaatdubbels](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) in de Azure Portal. Op deze pagina vindt u een lijst met alle Azure Digital Apparaatdubbels-instanties. Zoek naar de naam van uw nieuw gemaakte exemplaar in de lijst.

### <a name="verify-user-role-assignment"></a>Gebruikersrol toewijzing controleren

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

> [!NOTE]
> U herinnert dat het script momenteel deze vereiste rol toewijst aan dezelfde gebruiker die het script uitvoert van Cloud Shell. Als u deze rol moet toewijzen aan iemand anders die het exemplaar gaat beheren, kunt u dit nu doen via de Azure Portal ([instructies](how-to-set-up-instance-portal.md#set-up-user-access-permissions)) of cli ([instructies](how-to-set-up-instance-cli.md#set-up-user-access-permissions)).
>
> U kunt ook de portal of CLI gebruiken om uw eigen roltoewijzing opnieuw uit te stellen als er problemen zijn met het instellen van een script.

### <a name="verify-app-registration"></a>App-registratie verifiëren

[!INCLUDE [digital-twins-setup-verify-app-registration-1.md](../../includes/digital-twins-setup-verify-app-registration-1.md)]

Controleer eerst of de instellingen voor de Azure Digital Apparaatdubbels-machtigingen correct zijn ingesteld voor de registratie. Als u dit wilt doen, selecteert u *manifest* in de menu balk om de manifest code van de app-registratie weer te geven. Ga naar de onderkant van het code venster en zoek deze velden onder `requiredResourceAccess` . De waarden moeten overeenkomen met die in de onderstaande scherm afbeelding:

[!INCLUDE [digital-twins-setup-verify-app-registration-2.md](../../includes/digital-twins-setup-verify-app-registration-2.md)]

## <a name="other-possible-steps-for-your-organization"></a>Andere mogelijke stappen voor uw organisatie

[!INCLUDE [digital-twins-setup-additional-requirements.md](../../includes/digital-twins-setup-additional-requirements.md)]

## <a name="next-steps"></a>Volgende stappen

Test afzonderlijke REST API-aanroepen voor uw exemplaar met behulp van de Azure Digital Apparaatdubbels CLI-opdrachten: 
* [AZ DT-referentie](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt?view=azure-cli-latest)
* [*Instructies: De Azure Digital Twins-CLI gebruiken*](how-to-use-cli.md)

U kunt ook zien hoe u uw client toepassing verbindt met uw instantie door de verificatie code van de client-app te schrijven:
* [*Instructies: app-verificatie code schrijven*](how-to-authenticate-client.md)
