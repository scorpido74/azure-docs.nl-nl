---
title: Een instantie en authenticatie instellen (CLI)
titleSuffix: Azure Digital Twins
description: Zie een exemplaar van de Azure Digital Apparaatdubbels-service instellen met behulp van de CLI
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 287ee62acf3a078c4b47803060f61c9dd4134ab7
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87408287"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-cli"></a>Een Azure Digital Apparaatdubbels-exemplaar en-authenticatie (CLI) instellen

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

In dit artikel worden de stappen beschreven voor het **instellen van een nieuwe Azure Digital apparaatdubbels-instantie**, inclusief het maken van het exemplaar en het instellen van verificatie. Nadat dit artikel is voltooid, hebt u een Azure Digital Apparaatdubbels-exemplaar gereed om te Program meren.

Deze versie van dit artikel doorloopt deze stappen hand matig, één voor één, met behulp van de CLI.
* Als u deze stappen hand matig wilt door lopen met behulp van de Azure Portal, raadpleegt u de portal versie van dit artikel: [*instructies: een exemplaar en verificatie instellen (Portal)*](how-to-set-up-instance-portal.md).
* Als u via een automatische installatie wilt uitvoeren met behulp van een voor beeld van een implementatie script, raadpleegt u de script versie van dit artikel: [*instructies: een exemplaar en authenticatie instellen (script)*](how-to-set-up-instance-scripted.md).

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-role-cli.md](../../includes/digital-twins-setup-role-cli.md)]

## <a name="set-up-cloud-shell-session"></a>Cloud Shell sessie instellen
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="create-the-azure-digital-twins-instance"></a>Het Azure Digital Apparaatdubbels-exemplaar maken

In deze sectie **maakt u een nieuw exemplaar van Azure Digital apparaatdubbels** met behulp van de Cloud shell opdracht. U moet het volgende opgeven:
* Een resource groep om deze in te implementeren. Als u nog geen bestaande resource groep hebt, kunt u er nu een maken met de volgende opdracht:
    ```azurecli
    az group create --location <region> --name <name-for-your-resource-group>
    ```
* Een regio voor de implementatie. Als u wilt zien welke regio's Azure Digital Apparaatdubbels ondersteunen, gaat u naar [*Azure-producten beschikbaar per regio*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
* Een naam voor uw exemplaar. De naam van het nieuwe exemplaar moet uniek zijn binnen de regio voor uw abonnement (wat betekent dat als uw abonnement een ander Azure Digital Apparaatdubbels-exemplaar heeft in de regio die al gebruikmaakt van de naam die u kiest, wordt u gevraagd een andere naam te kiezen).

Gebruik deze waarden in de volgende opdracht om het exemplaar te maken:

```azurecli
az dt create --dt-name <name-for-your-Azure-Digital-Twins-instance> -g <your-resource-group> -l <region>
```

### <a name="verify-success"></a>Controleren geslaagd

Als het exemplaar is gemaakt, wordt het resultaat in Cloud Shell er ongeveer als volgt uitzien: het uitvoeren van informatie over de resource die u hebt gemaakt.

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/create-instance.png" alt-text="Opdrachtvenster met het maken van een resource groep en een Azure Digital Apparaatdubbels-exemplaar":::

Noteer de *hostnaam*, *naam*en *ResourceGroup* van het Azure Digital apparaatdubbels-exemplaar van de uitvoer. Dit zijn alle belang rijke waarden die u mogelijk nodig hebt wanneer u met uw Azure Digital Apparaatdubbels-exemplaar blijft werken om verificatie en gerelateerde Azure-resources in te stellen. Als andere gebruikers op het exemplaar worden geprogrammeerd, moet u deze waarden met hen delen.

> [!TIP]
> U kunt deze eigenschappen, samen met alle eigenschappen van uw exemplaar, op elk gewenst moment weer geven door uit te voeren `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

U hebt nu een Azure Digital Apparaatdubbels-exemplaar klaar om te gaan. Vervolgens geeft u de juiste Azure-gebruikers machtigingen om deze te beheren.

## <a name="set-up-user-access-permissions"></a>Machtigingen voor gebruikers toegang instellen

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

Gebruik de volgende opdracht om de rol toe te wijzen (moet worden uitgevoerd door een eigenaar van het Azure-abonnement):

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<Azure-AD-email-of-user-to-assign>" --role "Azure Digital Twins Owner (Preview)"
```

Het resultaat van deze opdracht is een gegenereerde informatie over de roltoewijzing die is gemaakt.

> [!TIP]
> Als u in plaats daarvan een *400: onjuiste aanvraag* -fout krijgt, voert u de volgende opdracht uit om de *ObjectID* voor de gebruiker op te halen:
> ```azurecli
> az ad user show --id <Azure-AD-email-of-user-to-assign> --query objectId
> ```
> Herhaal vervolgens de opdracht roltoewijzing met de *object-id* van de gebruiker in plaats van hun e-mail adres.

### <a name="verify-success"></a>Controleren geslaagd

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

U hebt nu een Azure Digital Apparaatdubbels-exemplaar klaar om te gaan, en u hebt machtigingen toegewezen om het te beheren. Vervolgens stelt u de machtigingen voor een client-app in om deze te openen.

## <a name="set-up-access-permissions-for-client-applications"></a>Toegangs machtigingen voor client toepassingen instellen

[!INCLUDE [digital-twins-setup-app-registration.md](../../includes/digital-twins-setup-app-registration.md)]

Als u een app-registratie wilt maken, moet u de resource-Id's voor de Azure Digital Apparaatdubbels-Api's en de basislijn machtigingen voor de API opgeven.

Maak een nieuw bestand in uw werkmap en voer het volgende JSON-fragment in om deze details te configureren: 

```json
[{
    "resourceAppId": "0b07f429-9f4b-4714-9392-cc5e8e80c8b0",
    "resourceAccess": [
     {
       "id": "4589bd03-58cb-4e6c-b17f-b580e39652f8",
       "type": "Scope"
     }
    ]
}]
``` 

Sla dit bestand _**op alsmanifest.jsop**_.

> [!NOTE] 
> Er zijn enkele locaties waar een ' vriendelijke ', lees bare teken reeks `https://digitaltwins.azure.net` kan worden gebruikt voor de app-id van de Azure Digital apparaatdubbels-bron in plaats van de GUID `0b07f429-9f4b-4714-9392-cc5e8e80c8b0` . Zo kunnen bijvoorbeeld veel voor beelden in deze documentatieset authenticatie gebruiken met de MSAL-bibliotheek, en de beschrijvende teken reeks kan hiervoor worden gebruikt. Tijdens deze stap voor het maken van de app-registratie is de GUID-vorm van de ID echter vereist, zoals hierboven wordt weer gegeven. 

Vervolgens uploadt u dit bestand naar Cloud Shell. Klik in het venster Cloud Shell op het pictogram bestanden uploaden/downloaden en kies uploaden.

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="Cloud Shell venster met de selectie van de Upload optie":::
Ga naar de *manifest.js* die u zojuist hebt gemaakt en klik op openen.

Voer vervolgens de volgende opdracht uit om een app-registratie te maken (waar nodig tijdelijke aanduidingen vervangen):

```azurecli
az ad app create --display-name <name-for-your-app> --native-app --required-resource-accesses manifest.json --reply-url http://localhost
```

Hier volgt een fragment van de uitvoer van deze opdracht, met informatie over de registratie die u hebt gemaakt:

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/new-app-registration.png" alt-text="Cloud Shell uitvoer van nieuwe Azure AD-App-registratie":::

### <a name="verify-success"></a>Controleren geslaagd

[!INCLUDE [digital-twins-setup-verify-app-registration-1.md](../../includes/digital-twins-setup-verify-app-registration-1.md)]

Controleer eerst of de instellingen van uw geüploade *manifest.jsop* de juiste wijze zijn ingesteld voor de registratie. Als u dit wilt doen, selecteert u *manifest* in de menu balk om de manifest code van de app-registratie weer te geven. Ga naar de onderkant van het code venster en zoek naar de velden van uw *manifest.js* onder `requiredResourceAccess` :

[!INCLUDE [digital-twins-setup-verify-app-registration-2.md](../../includes/digital-twins-setup-verify-app-registration-2.md)]

### <a name="collect-important-values"></a>Belang rijke waarden verzamelen

Selecteer vervolgens *overzicht* in de menu balk om de details van de app-registratie te bekijken:

:::image type="content" source="media/how-to-set-up-instance/portal/app-important-values.png" alt-text="Portal weergave van de belang rijke waarden voor de app-registratie":::

Noteer de ID van de *toepassings* -id en de *Directory (Tenant)* die op **de** pagina wordt weer gegeven. Deze waarden zijn later nodig om [een client-app te verifiëren tegen de Azure Digital apparaatdubbels-api's](how-to-authenticate-client.md). Als u niet de persoon bent die code gaat schrijven voor dergelijke toepassingen, moet u deze waarden delen met de persoon die het gaat doen.

### <a name="other-possible-steps-for-your-organization"></a>Andere mogelijke stappen voor uw organisatie

[!INCLUDE [digital-twins-setup-additional-requirements.md](../../includes/digital-twins-setup-additional-requirements.md)]

## <a name="next-steps"></a>Volgende stappen

Bekijk hoe u uw client toepassing verbindt met uw exemplaar door de verificatie code van de client-app te schrijven:
* [*Instructies: app-verificatie code schrijven*](how-to-authenticate-client.md)