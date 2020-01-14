---
title: Verwerkings feedback voor Azure apps-aanbieding in de commerciële Marketplace
description: Beoordelings feedback voor Azure apps-aanbieding verwerken voor het aanbieden of verkopen in azure Marketplace, AppSource of via het programma Cloud Solution Provider (CSP) met behulp van de portal voor commerciële Marketplace in micro soft Partner Center.
author: MaggiePucciEvans
manager: evansma
ms.author: evansma
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/11/2019
ms.openlocfilehash: 5dd74a7be95ecf92289f2a144c9c509c71ce935b
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75933646"
---
# <a name="handling-review-feedback"></a>Beoordelingsfeedback verwerken

In dit artikel wordt uitgelegd hoe u toegang krijgt tot de Azure DevOps-omgeving die wordt gebruikt door het Microsoft Azure Marketplace beoordelings team. Als er in uw Azure-toepassings aanbieding kritieke problemen zijn gevonden tijdens de stap van **micro soft Review** , kunt u zich aanmelden bij dit systeem om gedetailleerde informatie over deze problemen te bekijken (feedback beoordelen). Nadat u al deze problemen hebt opgelost, moet u uw aanbieding opnieuw verzenden om deze te kunnen publiceren op de Azure Marketplace. In het volgende diagram ziet u hoe dit feedback proces is gekoppeld aan het publicatie proces.

![Feedback proces controleren](./media/review-feedback-process.png)

Normaal gesp roken raadpleegt u problemen als pull-aanvraag (PR). Elke PR is gekoppeld aan een online [Azure DevOps](https://azure.microsoft.com/services/devops/) (voorheen Visual Studio Team Services (VSTS)) item met details over het probleem. In de volgende afbeelding ziet u een voor beeld van de ervaring van het partner centrum als er problemen zijn gevonden tijdens de evaluatie. 

![Publicatiestatus](./media/publishing-status.png)

De PR met specifieke details over de inzending wordt vermeld in de koppeling ' certificerings rapport weer geven '. Voor complexe situaties kunnen de teams met beoordelings-en ondersteunings team u ook een e-mail sturen.

## <a name="azure-devops-access"></a>Toegang tot Azure DevOps

Alle gebruikers met toegang tot de rol ' ontwikkelaar ' in het partner centrum hebben toegang tot het weer geven van de PR-items waarnaar wordt verwezen in de feedback van de beoordeling.

<!---
To view the PR items referenced in review feedback, publishers must first be granted proper authorization. Otherwise, new publishers receive a 401 - Not Authorized response page when trying to view PRs. To request access to this Azure DevOps repository, perform the following steps:

1. Collect the following information:
    * Your publisher name and ID
    * Offer type (Azure app), offer name, and SKU ID
    * The pull request link, for example: `https://solutiontemplates.visualstudio.com/marketplacesolutions/_git/contoso/pullrequest/<number>`. This URL can be retrieved from the notification message or the address of the 401 response page.
    * The email address(es) of the individuals from your publishing organization that you want access granted to. This list should include the owner address(es) you provided when registering as a publisher on the Cloud Partner Portal (or Partner Center??).
1. Create a support incident. In the title bar of the Cloud Partner Portal (??), select the **Help** button, then choose **Support** from the menu. Your default web browser should launch and navigate to the Microsoft new support incident page. (You may have to sign in first.)
1. Specify the **Problem type** as **marketplace onboarding** and **Category** as **Access problem**, then select **Start request**.

    ![Create an incident](./media/create-an-incident.png)

1. In **Step 1 of 2** page, supply your contact information and select **Continue**.
1. In **Step 2 of 2** page, specify an incident title (for example Request Azure DevOps access) and supply the information you collected in the first step (above). Read and accept the agreement, then select **Submit**.

If the incident creation was successful, a confirmation page is displayed. Save the confirmation information on this page for your reference. The Microsoft Support Team should reply to your access request within a few business days. --->

## <a name="reviewing-the-pull-request"></a>De pull-aanvraag controleren

Gebruik de volgende procedure om de problemen te bekijken die worden beschreven in de pull-aanvraag.

1. Klik in de sectie **micro soft beoordeling** van het formulier publicatie stappen op een PR-koppeling om uw browser te openen en navigeer naar de pagina **overzicht** (Home) voor deze PR. In de volgende afbeelding ziet u een voor beeld van de start pagina van het kritieke probleem voor de aanbieding van de voor beeld-app van contoso. Deze pagina bevat nuttige samenvattings informatie over de beoordelings problemen die in de Azure-app zijn gevonden.

    [![start pagina voor pull-aanvragen](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)
    <br/> *Klik op de afbeelding om uit te breiden.*

1. Beschrijving Klik aan de rechter kant van het venster in het gedeelte **beleid**op het probleem bericht (in dit voor beeld: **beleids validatie mislukt**) om de details op laag niveau van het probleem te onderzoeken, inclusief de bijbehorende logboek bestanden. Fouten worden doorgaans weer gegeven aan de onderkant van de logboek bestanden.
1. Selecteer in het menu aan de linkerkant van de start pagina de optie **bestanden** om de lijst bestanden weer te geven waaruit de technische activa voor deze aanbieding bestaan. De micro soft-revisoren moeten opmerkingen hebben toegevoegd waarin de gedetecteerde kritieke problemen worden beschreven. In het volgende voor beeld zijn er twee problemen ontdekt.

    [![start pagina voor pull-aanvragen](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)
    <br/> *Klik op de afbeelding om uit te breiden.*

1. Klik op elk opmerkings knooppunt in de linker boom structuur om naar de opmerking in de context van de omringende code te navigeren. Corrigeer de bron code in het project van uw team om het probleem te verhelpen dat wordt beschreven in de opmerking.

>[!Note]
>U kunt de technische activa van uw aanbieding niet bewerken in de Azure DevOps-omgeving van het beoordelings team. Voor uitgevers is dit een alleen-lezen omgeving voor de Inge sloten bron code. U kunt echter reacties op reacties geven op het voor deel van het micro soft Review-team.

   In het volgende voor beeld heeft de uitgever gecontroleerd, gecorrigeerd en gereageerd op het eerste probleem.

   ![Eerste oplossing en antwoord op Opmerking](./media/first-comment-reply.png)

## <a name="next-steps"></a>Volgende stappen

Nadat u de kritieke problemen hebt gecorrigeerd die in de beoordelings PR (s) zijn beschreven, moet u [uw Azure-app-aanbieding opnieuw publiceren](./create-new-azure-apps-offer.md#publish).
