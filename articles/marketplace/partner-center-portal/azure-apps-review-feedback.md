---
title: Controlefeedback verwerken voor Azure Apps-aanbieding in de commerciële marketplace
description: Controlefeedback voor Azure Apps-aanbieding voor vermelding of verkoop in het Azure Marketplace-, AppSource- of Via het CSP-programma (Cloud Solution Provider) verwerken met behulp van de Commerciële Marketplace-portal in Microsoft Partner Center.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/11/2019
ms.openlocfilehash: 3932f3fc71dc6427b6cdf93d3a7bc58534a9981c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80279790"
---
# <a name="handling-review-feedback"></a>Beoordelingsfeedback verwerken

In dit artikel wordt uitgelegd hoe u toegang krijgt tot de Azure DevOps-omgeving die wordt gebruikt door het Microsoft Azure Marketplace-beoordelingsteam. Als er tijdens de **microsoft-beoordelingsstap** kritieke problemen worden gevonden in uw Azure-toepassingsaanbieding, u zich bij dit systeem aanmelden om gedetailleerde informatie over deze problemen weer te geven (feedback bekijken). Nadat u al deze problemen hebt opgelost, moet u uw aanbieding opnieuw indienen om deze te blijven publiceren op de Azure Marketplace. In het volgende diagram ziet u hoe dit feedbackproces zich verhoudt tot het publicatieproces.

![Feedbackproces bekijken](./media/review-feedback-process.png)

Doorgaans worden beoordelingskwesties aangeduid als pull request (PR). Elke PR is gekoppeld aan een online [Azure DevOps](https://azure.microsoft.com/services/devops/) (voorheen Visual Studio Team Services (VSTS)) item, dat details over het probleem bevat. In de volgende afbeelding wordt een voorbeeld van de ervaring van het Partnercentrum weergegeven als er problemen worden gevonden tijdens beoordelingen. 

![Publicatiestatus](./media/publishing-status.png)

De PR met specifieke details over de indiening wordt vermeld in de link 'Certificeringsrapport bekijken'. Voor complexe situaties kunnen de beoordelings- en ondersteuningsteams u ook een e-mail sturen.

## <a name="azure-devops-access"></a>Azure DevOps-toegang

Alle gebruikers met toegang tot de rol 'ontwikkelaar' in partnercentrum hebben toegang tot de PR-items waarnaar wordt verwezen in de feedback van de beoordeling.

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

## <a name="reviewing-the-pull-request"></a>Het pull-verzoek bekijken

Gebruik de volgende procedure om problemen te bekijken die zijn gedocumenteerd in de pull-aanvraag.

1. Klik in de gedeelten **van Microsoft-recensie** van het formulier Publiceren op een PR-koppeling om uw browser te starten en navigeer naar **de** startpagina (startpagina) voor deze PR. In de volgende afbeelding wordt een voorbeeld weergegeven van de startpagina van kritieke problemen voor de aanbieding van de Contoso-voorbeeld-app. Deze pagina bevat nuttige overzichtsinformatie over de beoordelingsproblemen in de Azure-app.

    [![Startpagina van aanvraag trekken](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)
    <br/> *Klik op de afbeelding om uit te vouwen.*

1. (Optioneel) Klik aan de rechterkant van het venster in de sectie **Beleid**op het probleembericht (in dit voorbeeld: **Beleidsvalidatie is mislukt)** om de details op laag niveau van het probleem te onderzoeken, inclusief de bijbehorende logboekbestanden. Fouten worden meestal onder aan de logboekbestanden weergegeven.
1. **Selecteer** bestanden om de lijstbestanden weer te geven die de technische elementen voor deze aanbieding bevatten in het menu aan de linkerkant van de startpagina. De Microsoft-beoordelaars moeten opmerkingen hebben toegevoegd waarin de ontdekte kritieke problemen worden beschreven. In het volgende voorbeeld zijn twee problemen ontdekt.

    [![Startpagina van aanvraag trekken](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)
    <br/> *Klik op de afbeelding om uit te vouwen.*

1. Klik op elk opmerkingsknooppunt in de linkerstructuur om naar de opmerking in de context van de omringende code te navigeren. Repareer uw broncode in het project van uw team om het probleem te verhelpen dat door de opmerking wordt beschreven.

>[!Note]
>U de technische elementen van uw aanbieding niet bewerken binnen de Azure DevOps-omgeving van het beoordelingsteam. Voor uitgevers is dit een alleen-lezen omgeving voor de opgenomen broncode. U echter antwoorden op de opmerkingen achterlaten ten behoeve van het Microsoft-beoordelingsteam.

   In het volgende voorbeeld heeft de uitgever het eerste nummer beoordeeld, gecorrigeerd en geantwoord.

   ![Eerste fix en commentaar antwoord](./media/first-comment-reply.png)

## <a name="next-steps"></a>Volgende stappen

Nadat u de kritieke problemen hebt verholpen die zijn gedocumenteerd in de e-meter-NL beoordeling, moet u [uw Azure-app-aanbieding opnieuw publiceren.](./create-new-azure-apps-offer.md#publish)
