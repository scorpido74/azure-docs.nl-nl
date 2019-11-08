---
title: Feedback van Azure Application Review verwerken | Azure Marketplace
description: In dit artikel wordt uitgelegd hoe u Azure DevOps gebruikt voor het afhandelen van feedback over de aanbiedingen van Azure-toepassingen voor Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/05/2019
ms.author: pabutler
ms.openlocfilehash: ef4aff57948034fb369bd74564306b7b8674b377
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827588"
---
# <a name="handling-review-feedback"></a>Beoordelingsfeedback verwerken

In dit artikel wordt uitgelegd hoe u toegang krijgt tot de Azure DevOps-omgeving die wordt gebruikt door het Microsoft Azure Marketplace beoordelings team.  Als er in uw Azure-toepassings aanbieding kritieke problemen zijn gevonden tijdens de stap van **micro soft Review** , kunt u zich aanmelden bij dit systeem om gedetailleerde informatie over deze problemen te bekijken (feedback beoordelen).  Nadat u al deze problemen hebt opgelost, moet u uw aanbieding opnieuw verzenden om deze te kunnen publiceren op de Azure Marketplace.  In het volgende diagram ziet u hoe dit feedback proces is gekoppeld aan het publicatie proces.

![Stappen publiceren met Azure DevOps feedback](./media/pub-flow-vsts-access.png)

Normaal gesp roken raadpleegt u problemen als pull-aanvraag (PR).  Elke PR is gekoppeld aan een online [Azure DevOps](https://azure.microsoft.com/services/devops/) (voorheen Visual Studio Team Services (VSTS)) item met details over het probleem.  In de volgende afbeelding ziet u een voor beeld van een verwijzing naar een overzichts PR.  Voor complexe situaties kunnen de teams met beoordelings-en ondersteunings team u ook een e-mail sturen. 

![Tabblad status met feedback geven](./media/status-tab-ms-review.png)


## <a name="azure-devops-access"></a>Toegang tot Azure DevOps

Voor het weer geven van de PR-items waarnaar wordt verwezen in feedback over de beoordeling, moeten uitgevers eerst de juiste autorisatie krijgen.  Anders ontvangen nieuwe uitgevers een `401 - Not Authorized` antwoord pagina wanneer wordt geprobeerd pull weer te geven.  Voer de volgende stappen uit om toegang te vragen tot deze Azure DevOps-opslag plaats:

1. Verzamel de volgende gegevens:
    - Naam en ID van de uitgever
    - Aanbiedings type (Azure-app), naam van aanbieding en SKU-ID
    - De koppeling voor de pull-aanvraag, bijvoorbeeld: `https://solutiontemplates.visualstudio.com/marketplacesolutions/_git/contoso/pullrequest/<number>` deze URL kan worden opgehaald uit het meldings bericht of het adres van de 401-antwoord pagina.
    - Het e-mail adres (sen) van de personen van de publicatie organisatie waartoe u toegang wilt verlenen.  Deze lijst moet de adres (sen) van de eigenaar bevatten die u hebt ingevoerd bij het registreren van een uitgever op het Cloud Partner-portal.
2. Een ondersteunings incident maken.  Selecteer de knop **Help** in de titel balk van het Cloud Partner-Portal en kies vervolgens **ondersteuning** in het menu.  U moet de standaard webbrowser starten en navigeren naar de pagina met het nieuwe ondersteunings incident van micro soft.  (Mogelijk moet u zich eerst aanmelden.)
3. Geef het **probleem type** op als **onboarding voor Marketplace** en **categorie** als **toegangs probleem**en selecteer vervolgens **aanvraag starten**.

    ![Categorie ondersteunings tickets](./media/support-incident1.png)

4. Geef in **stap 1 van 2** pagina uw contact gegevens op en selecteer **door gaan**.
5. Geef op de pagina **stap 2 van 2** de titel van een incident op (bijvoorbeeld `Request Azure DevOps access`) en geef de gegevens op die u in de eerste stap hebt verzameld (hierboven).  Lees en accepteer de overeenkomst en selecteer vervolgens **verzenden**.

Als het maken van het incident is gelukt, wordt er een bevestigings pagina weer gegeven.  Sla de bevestigings gegevens op deze pagina op voor uw referentie.  Het Microsoft Ondersteuning team moet binnen een paar werk dagen uw toegangs aanvraag beantwoorden.


## <a name="reviewing-the-pull-request"></a>De pull-aanvraag controleren 

Gebruik de volgende procedure om de problemen te bekijken die worden beschreven in de pull-aanvraag.

1. Klik in de sectie **micro soft Review** van het formulier **publicatie stappen** op een PR-koppeling om uw browser te openen en navigeer naar de pagina **overzicht** (Home) voor deze PR.  In de volgende afbeelding ziet u een voor beeld van een start pagina voor een kritiek probleem voor de aanbieding van de contoso-voor beeld-app.  Deze pagina bevat nuttige samenvattings informatie over de beoordelings problemen die in de Azure-app zijn gevonden.  

    [![start pagina voor pull-aanvragen](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)
    <br/> *Klik op de afbeelding om uit te breiden.*
    
2. Beschrijving Klik aan de rechter kant van het venster in het gedeelte **beleid**op het probleem bericht (in dit voor beeld: **beleids validatie mislukt**) om de details op laag niveau van het probleem te onderzoeken, inclusief de bijbehorende logboek bestanden.  Fouten worden doorgaans weer gegeven aan de onderkant van de logboek bestanden.

3. Selecteer in het menu aan de linkerkant van de start pagina de optie **bestanden** om de lijst bestanden weer te geven waaruit de technische activa voor deze aanbieding bestaan.  De micro soft-revisoren moeten opmerkingen hebben toegevoegd waarin de gedetecteerde kritieke problemen worden beschreven.  In het volgende voor beeld zijn er twee problemen ontdekt. 

    [![start pagina voor pull-aanvragen](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)
    <br/> *Klik op de afbeelding om uit te breiden.*

4. Klik op elk opmerkings knooppunt in de linker boom structuur om naar de opmerking in de context van de omringende code te navigeren.  Corrigeer de bron code in het project van uw team om het probleem te verhelpen dat wordt beschreven in de opmerking.

> [!Note]
> U kunt de technische activa van uw aanbieding niet bewerken in de Azure DevOps-omgeving van het beoordelings team.  Voor uitgevers is dit een alleen-lezen omgeving voor de Inge sloten bron code.  U kunt echter reacties op reacties geven op het voor deel van het micro soft Review-team.

   In het volgende voor beeld heeft de uitgever gecontroleerd, gecorrigeerd en gereageerd op het eerste probleem.

   ![Eerste oplossing en antwoord op Opmerking](./media/first-comment-reply.png)


## <a name="next-steps"></a>Volgende stappen

Nadat u de kritieke problemen hebt gecorrigeerd die in de beoordelings PR (s) zijn beschreven, moet u [uw Azure-app-aanbieding opnieuw publiceren](./cpp-publish-offer.md).
