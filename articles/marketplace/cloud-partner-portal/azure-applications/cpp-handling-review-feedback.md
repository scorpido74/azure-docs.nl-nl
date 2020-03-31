---
title: Feedback voor het controleren van Azure-toepassingen verwerken | Azure Marketplace
description: Hier wordt uitgelegd hoe u Azure DevOps gebruikt om controlefeedback voor Azure-toepassingsaanbiedingen voor de Azure Marketplace te verwerken.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/05/2019
ms.author: dsindona
ms.openlocfilehash: b5f290bae908ac753801eef2c9b8394ca1bb7a40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285296"
---
# <a name="handling-review-feedback"></a>Beoordelingsfeedback verwerken

In dit artikel wordt uitgelegd hoe u toegang krijgt tot de Azure DevOps-omgeving die wordt gebruikt door het Microsoft Azure Marketplace-beoordelingsteam.  Als er tijdens de **microsoft-beoordelingsstap** kritieke problemen worden gevonden in uw Azure-toepassingsaanbieding, u zich bij dit systeem aanmelden om gedetailleerde informatie over deze problemen weer te geven (feedback bekijken).  Nadat u al deze problemen hebt opgelost, moet u uw aanbieding opnieuw indienen om deze te blijven publiceren op de Azure Marketplace.  In het volgende diagram ziet u hoe dit feedbackproces zich verhoudt tot het publicatieproces.

![Publicatiestappen met Azure DevOps-feedback](./media/pub-flow-vsts-access.png)

Doorgaans worden beoordelingskwesties aangeduid als pull request (PR).  Elke PR is gekoppeld aan een online [Azure DevOps](https://azure.microsoft.com/services/devops/) (voorheen Visual Studio Team Services (VSTS)) item, dat details over het probleem bevat.  In de volgende afbeelding wordt een voorbeeld van een pre-pr-verwijzing voor beoordeling weergegeven.  Voor complexe situaties kunnen de beoordelings- en ondersteuningsteams u ook een e-mail sturen. 

![Tabblad Status met controlefeedback](./media/status-tab-ms-review.png)


## <a name="azure-devops-access"></a>Azure DevOps-toegang

Om de PR-items waarnaar wordt verwezen in de feedback van de beoordeling te bekijken, moeten uitgevers eerst de juiste toestemming krijgen.  Anders ontvangen nieuwe uitgevers `401 - Not Authorized` een antwoordpagina wanneer ze p's proberen te bekijken.  Voer de volgende stappen uit om toegang tot deze Azure DevOps-opslagplaats aan te vragen:

1. Verzamel de volgende gegevens:
    - Uw naam en id van uw uitgever
    - Aanbiedingstype (Azure-app), aanbiedingsnaam en SKU-id
    - De koppeling pull request, `https://solutiontemplates.visualstudio.com/marketplacesolutions/_git/contoso/pullrequest/<number>` bijvoorbeeld: Deze URL kan worden opgehaald uit het meldingsbericht of het adres van de 401-antwoordpagina.
    - Het e-mailadres(en) van de personen van uw publicatieorganisatie waartoe u toegang wilt krijgen.  Deze lijst moet het adres(en) van de eigenaar bevatten dat u hebt opgegeven bij het registreren als uitgever op de Cloud Partner Portal.
2. Maak een ondersteuningsincident.  Selecteer in de titelbalk van de Cloud Partner Portal de **Knop Help** en kies **Ondersteuning** in het menu.  U moet de standaardwebbrowser starten en naar de nieuwe ondersteuningsincidentpagina van Microsoft navigeren.  (Mogelijk moet u zich eerst aanmelden.)
3. Geef het **probleemtype** op als onboarding en **categorie als** **Access-probleem** en selecteer Vervolgens **Startaanvraag .** **Access problem**

    ![Categorie ondersteuningstickets](./media/support-incident1.png)

4. Lever in **stap 1 van 2** pagina uw contactgegevens op en selecteer **Doorgaan**.
5. Geef in **stap 2 van 2** pagina `Request Azure DevOps access`een incidenttitel op (bijvoorbeeld) en geef de informatie die u in de eerste stap hebt verzameld (hierboven).  Lees en accepteer de overeenkomst en selecteer **Verzenden**.

Als het maken van incidenten is gelukt, wordt een bevestigingspagina weergegeven.  Sla de bevestigingsgegevens op deze pagina op voor uw referentie.  Het Microsoft Support Team moet binnen enkele werkdagen op uw toegangsverzoek reageren.


## <a name="reviewing-the-pull-request"></a>Het pull-verzoek bekijken 

Gebruik de volgende procedure om problemen te bekijken die zijn gedocumenteerd in de pull-aanvraag.

1. Klik in het gedeelte **Microsoft-recensie** van het formulier **Publicatiestappen** op een PR-koppeling om uw browser te starten en navigeer naar **de** startpagina (startpagina) voor deze PR.  In de volgende afbeelding wordt een voorbeeld afbeelding weergegeven waarop een kritieke probleempagina voor de contoso-voorbeeld-app wordt aangeboden.  Deze pagina bevat nuttige overzichtsinformatie over de beoordelingsproblemen in de Azure-app.  

    [![Startpagina van aanvraag trekken](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)
    <br/> *Klik op de afbeelding om uit te vouwen.*
    
2. (Optioneel) Klik aan de rechterkant van het venster in de sectie **Beleid**op het probleembericht (in dit voorbeeld: **Beleidsvalidatie is mislukt)** om de details op laag niveau van het probleem te onderzoeken, inclusief de bijbehorende logboekbestanden.  Fouten worden meestal onder aan de logboekbestanden weergegeven.

3. **Selecteer** bestanden om de lijstbestanden weer te geven die de technische elementen voor deze aanbieding bevatten in het menu aan de linkerkant van de startpagina.  De Microsoft-beoordelaars moeten opmerkingen hebben toegevoegd waarin de ontdekte kritieke problemen worden beschreven.  In het volgende voorbeeld zijn twee problemen ontdekt. 

    [![Startpagina van aanvraag trekken](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)
    <br/> *Klik op de afbeelding om uit te vouwen.*

4. Klik op elk opmerkingsknooppunt in de linkerstructuur om naar de opmerking in de context van de omringende code te navigeren.  Repareer uw broncode in het project van uw team om het probleem te verhelpen dat door de opmerking wordt beschreven.

> [!Note]
> U de technische elementen van uw aanbieding niet bewerken binnen de Azure DevOps-omgeving van het beoordelingsteam.  Voor uitgevers is dit een alleen-lezen omgeving voor de opgenomen broncode.  U echter antwoorden op de opmerkingen achterlaten ten behoeve van het Microsoft-beoordelingsteam.

   In het volgende voorbeeld heeft de uitgever het eerste nummer beoordeeld, gecorrigeerd en geantwoord.

   ![Eerste fix en commentaar antwoord](./media/first-comment-reply.png)


## <a name="next-steps"></a>Volgende stappen

Nadat u de kritieke problemen hebt verholpen die zijn gedocumenteerd in de e-meter-NL beoordeling, moet u [uw Azure-app-aanbieding opnieuw publiceren.](./cpp-publish-offer.md)
