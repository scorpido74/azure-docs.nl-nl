---
title: SharePoint-bestanden - QnA Maker
description: Voeg beveiligde SharePoint-gegevensbronnen toe aan uw kennisbank om de kennisbank te verrijken met vragen en antwoorden die kunnen worden beveiligd met Active Directory.
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 6f05079e39c8afb001bd4ba09d68f435c18efad5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294882"
---
# <a name="add-a-secured-sharepoint-data-source-to-your-knowledge-base"></a>Een beveiligde SharePoint-gegevensbron toevoegen aan uw kennisbank

Voeg beveiligde cloudgebaseerde SharePoint-gegevensbronnen toe aan uw kennisbank om de kennisbank te verrijken met vragen en antwoorden die kunnen worden beveiligd met Active Directory.

Wanneer u als QnA Maker-manager een beveiligd SharePoint-document toevoegt aan uw kennisbank, moet u Active Directory-toestemming aanvragen voor QnA Maker. Zodra deze toestemming is gegeven van de Active Directory manager aan QnA Maker voor toegang tot SharePoint, hoeft deze niet meer te worden gegeven. Elke volgende documenttoevoeging aan de kennisbank heeft geen autorisatie nodig als deze zich in dezelfde SharePoint-bron bevindt.

Als de kennisbankmanager van QnA Maker niet de Active Directory-manager is, moet u communiceren met de Active Directory-manager om dit proces te voltooien.

## <a name="prerequisites"></a>Vereisten

* SharePoint in de cloud - QnA Maker gebruikt Microsoft Graph voor machtigingen. Als uw SharePoint on-premises is, u geen uittreksel uit SharePoint halen omdat Microsoft Graph geen machtigingen kan bepalen.
* URL-indeling - QnA Maker ondersteunt alleen SharePoint-url's die worden gegenereerd voor delen en van formaat zijn`https://\*.sharepoint.com`

## <a name="add-supported-file-types-to-knowledge-base"></a>Ondersteunde bestandstypen toevoegen aan knowledge base

U alle door QnA Maker ondersteunde [bestandstypen](../Concepts/content-types.md) van een SharePoint-site toevoegen aan uw kennisbank. Mogelijk moet u [machtigingen](#permissions) verlenen als de bestandsbron is beveiligd.

1. Selecteer in de bibliotheek met de SharePoint-site het `...`menu ellips van het bestand.
1. Kopieer de URL van het bestand.

   ![Download de URL van het SharePoint-bestand door het menu ellips van het bestand te selecteren en vervolgens de URL te kopiëren.](../media/add-sharepoint-datasources/get-sharepoint-file-url.png)

1. Voeg in de QnA Maker-portal op de pagina **Instellingen** [de URL toe](manage-knowledge-bases.md#edit-knowledge-base) aan de kennisbank.

### <a name="images-with-sharepoint-files"></a>Afbeeldingen met SharePoint-bestanden

Als bestanden afbeeldingen bevatten, worden deze niet geëxtraheerd. U de afbeelding toevoegen, vanuit de QnA Maker-portal, nadat het bestand is geëxtraheerd in QnA-paren.

Voeg de afbeelding toe aan de volgende afwaarderingssyntaxis:

```markdown
![Explanation or description of image](URL of public image)
```

De tekst in de `[]`vierkante haakjes, , verklaart de afbeelding. De URL tussen haakjes, `()`, is de directe link naar de afbeelding.

Wanneer u het QnA-paar test in het interactieve testpaneel, wordt de afbeelding in de QnA Maker-portal weergegeven in plaats van de markeringstekst. Dit valideert de afbeelding kan openbaar worden opgehaald uit uw client-applicatie.

## <a name="permissions"></a>Machtigingen

Het verlenen van machtigingen gebeurt wanneer een beveiligd bestand van een SharePoint-server wordt toegevoegd aan een kennisbank. Afhankelijk van de manier waarop het SharePoint is ingesteld en de machtigingen van de persoon die het bestand toevoegt, kan dit nodig zijn:

* geen extra stappen - de persoon die het bestand toevoegt, heeft alle benodigde machtigingen.
* stappen van zowel [knowledge base manager](#knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal) als Active Directory [manager](#active-directory-manager-grant-file-read-access-to-qna-maker).

Bekijk de onderstaande stappen.

### <a name="knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal"></a>Knowledge base manager: SharePoint data source toevoegen in QnA Maker portal

Wanneer de **QnA Maker-manager** een beveiligd SharePoint-document toevoegt aan een kennisbank, initieert de knowledge base manager een verzoek om toestemming die de Active Directory-manager moet voltooien.

De aanvraag begint met een pop-up om te verifiëren naar een Active Directory-account.

![Gebruikersaccount verifiëren](../media/add-sharepoint-datasources/authenticate-user-account.png)

Zodra de QnA Maker-manager het account heeft geselecteerd, ontvangt de Azure Active Directory-beheerder een bericht dat ze de QnA Maker-app (niet de QnA Maker-manager) toegang moeten geven tot de SharePoint-bron. De Azure Active Directory manager moet dit doen voor elke SharePoint-bron, maar niet elk document in die bron.

### <a name="active-directory-manager-grant-file-read-access-to-qna-maker"></a>Active directory manager: geef toegang tot het lezen van bestanden aan QnA Maker

De Active Directory manager (niet de QnA Maker manager) moet QnA Maker toegang verlenen om toegang te krijgen tot de SharePoint-bron door [deze koppeling](https://login.microsoftonline.com/common/oauth2/v2.0/authorize?response_type=id_token&scope=Files.Read%20Files.Read.All%20Sites.Read.All%20User.Read%20User.ReadBasic.All%20profile%20openid%20email&client_id=c2c11949-e9bb-4035-bda8-59542eb907a6&redirect_uri=https%3A%2F%2Fwww.qnamaker.ai%3A%2FCreate&state=68) te selecteren om de QnA Maker Portal SharePoint-bedrijfsapp toestemming te geven voor bestandsleesmachtigingen.

![Azure Active Directory manager verleent op interactieve wijze toestemming](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)

<!--
The Active Directory manager must grant QnA Maker access either by application name, `QnAMakerPortalSharePoint`, or by application ID, `c2c11949-e9bb-4035-bda8-59542eb907a6`.
-->
<!--
### Grant access from the interactive pop-up window

The Active Directory manager will get a pop-up window requesting permissions to the `QnAMakerPortalSharePoint` app. The pop-up window includes the QnA Maker Manager email address that initiated the request, an `App Info` link to learn more about **QnAMakerPortalSharePoint**, and a list of permissions requested. Select **Accept** to provide those permissions.

![Azure Active Directory manager grants permission interactively](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)
-->
<!--

### Grant access from the App Registrations list

1. The Active Directory manager signs in to the Azure portal and opens **[App registrations list](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ApplicationsListBlade)**.

1. Search for and select the **QnAMakerPortalSharePoint** app. Change the second filter box from **My apps** to **All apps**. The app information will open on the right side.

    ![Select QnA Maker app in App registrations list](../media/add-sharepoint-datasources/select-qna-maker-app-in-app-registrations.png)

1. Select **Settings**.

    [![Select Settings in the right-side blade](../media/add-sharepoint-datasources/select-settings-for-qna-maker-app-registration.png)](../media/add-sharepoint-datasources/select-settings-for-qna-maker-app-registration.png#lightbox)

1. Under **API access**, select **Required permissions**.

    ![Select 'Settings', then under 'API access', select 'Required permission'](../media/add-sharepoint-datasources/select-required-permissions-in-settings-blade.png)

1. Do not change any settings in the **Enable Access** window. Select **Grant Permission**.

    [![Under 'Grant Permission', select 'Yes'](../media/add-sharepoint-datasources/grant-app-required-permissions.png)](../media/add-sharepoint-datasources/grant-app-required-permissions.png#lightbox)

1. Select **YES** in the pop-up confirmation windows.

    ![Grant required permissions](../media/add-sharepoint-datasources/grant-required-permissions.png)
-->
### <a name="grant-access-from-the-azure-active-directory-admin-center"></a>Toegang verlenen vanuit het Azure Active Directory-beheercentrum

1. Active Directory-manager meldt zich aan bij de Azure-portal en opent **[Enterprise-toepassingen](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps)**.

1. Zoek `QnAMakerPortalSharePoint` naar de selecteer de QnA Maker-app.

    [![Zoeken naar QnAMakerPortalSharePoint in lijst met Enterprise-apps](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png)](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png#lightbox)

1. Ga **onder Beveiliging**naar **Machtigingen.** Selecteer **Toestemming voor beheerders verlenen voor organisatie**.

    [![Geverifieerde gebruiker selecteren voor Active Directory-beheerder](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png)](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png#lightbox)

1. Selecteer een aanmeldingsaccount met machtigingen om machtigingen toe te kennen voor de Active Directory.



<!--

## Add SharePoint data source with APIs

You need to get the SharePoint file's URI before adding it to QnA Maker.

## Get SharePoint File URI

Use the following steps to transform the SharePoint URL into a sharing token.

1. Encode the URL using [base64](https://en.wikipedia.org/wiki/Base64).

1. Convert the base64-encoded result to an unpadded base64url format with the following character changes.

    * Remove the equal character, `=` from the end of the value.
    * Replace `/` with `_`.
    * Replace `+` with `-`.
    * Append `u!` to be beginning of the string.

1. Sign in to Graph explorer and run the following query, where `sharedURL` is ...:

    ```
    https://graph.microsoft.com/v1.0/shares/<sharedURL>/driveitem
    ```

    Get the **@microsoft.graph.downloadUrl** and use this as `fileuri` in the QnA Maker APIs.

### Add or update a SharePoint File URI to your knowledge base

Use the **@microsoft.graph.downloadUrl** from the previous section as the `fileuri` in the QnA Maker API for [adding a knowledge base](https://go.microsoft.com/fwlink/?linkid=2092179) or [updating a knowledge base](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update). The following fields are mandatory: name, fileuri, filename, source.

```
{
    "name": "Knowledge base name",
    "files": [
        {
            "fileUri": "<@microsoft.graph.downloadURL>",
            "fileName": "filename.xlsx",
            "source": "<SharePoint link>"
        }
    ],
    "urls": [],
    "users": [],
    "hostUrl": "",
    "qnaList": []
}
```



## Remove QnA Maker app from SharePoint authorization

1. Use the steps in the previous section to find the Qna Maker app in the Active Directory admin center.
1. When you select the **QnAMakerPortalSharePoint**, select **Overview**.
1. Select **Delete** to remove permissions.

-->

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Werk samen aan uw kennisbank](collaborate-knowledge-base.md)
