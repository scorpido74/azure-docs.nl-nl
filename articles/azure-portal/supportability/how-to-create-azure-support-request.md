---
title: Een ondersteunings aanvraag voor Azure maken | Microsoft Docs
description: Klanten die hulp nodig hebben, kunnen de Azure Portal gebruiken om self-service oplossingen te vinden en ondersteunings aanvragen te maken en te beheren.
services: Azure Supportability
author: ganganarayanan
manager: scotthit
ms.assetid: fd6841ea-c1d5-4bb7-86bd-0c708d193b89
ms.service: azure-supportability
ms.topic: article
ms.date: 11/13/2019
ms.author: kfollis
ms.openlocfilehash: 35d70d37b881c610d1a38a312d79f39c351c65a5
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75897414"
---
# <a name="how-to-create-an-azure-support-request"></a>Een ondersteuningsaanvraag maken voor Azure

## <a name="overview"></a>Overzicht

Azure-klanten kunnen ondersteunings aanvragen maken en beheren in de [Azure Portal](https://portal.azure.com).

> [!NOTE]
> De Azure Portal-URL is specifiek voor de Azure-Cloud waar uw organisatie is geïmplementeerd. 
>* Azure Portal voor commercieel gebruik is: [https://portal.azure.com](https://portal.azure.com)
>* Azure Portal voor Duitsland is: [https://portal.microsoftazure.de](https://portal.microsoftazure.de)
>* Azure Portal voor de Verenigde Staten overheid is: [https://portal.azure.us](https://portal.azure.us)
>
>

Op basis van feedback van klanten hebben we de ervaring voor ondersteunings aanvragen bijgewerkt om te richten op drie hoofd doelen:

* **Gestroomlijnd**: Maak ondersteuning en probleem oplossing eenvoudig om te ontdekken hoe u een ondersteunings aanvraag indient.
* **Geïntegreerd**: u kunt eenvoudig een ondersteunings aanvraag openen wanneer u een probleem met een Azure-resource oplost zonder de context te scha kelen.
* **Efficiënt**: Verzamel de belang rijke informatie die uw ondersteunings agent nodig heeft om uw probleem efficiënt op te lossen.

## <a name="getting-started"></a>Aan de slag

U krijgt hulp en **ondersteuning** in de Azure Portal. Deze is beschikbaar via de globale koptekst of vanuit het resource menu voor een service. Voordat u een ondersteunings aanvraag kunt indienen, moet u de juiste machtigingen hebben.

### <a name="role-based-access-control"></a>Toegangsbeheer op basis van rollen

Als u een ondersteunings aanvraag wilt maken, moet u een beheerder zijn of worden toegewezen aan de rol [Inzender voor ondersteunings aanvragen](../../role-based-access-control/built-in-roles.md#support-request-contributor) .

### <a name="go-to-help--support-from-the-global-header"></a>Ga naar Help en ondersteuning vanuit de Global header

Als u een ondersteunings aanvraag wilt starten vanaf elke locatie in de Azure Portal:

1. Het pictogram **?** in de globale header. Selecteer vervolgens **Help + ondersteuning**.

   ![Help en ondersteuning](./media/how-to-create-azure-support-request/helpandsupportnewlower.png)

2. Selecteer **+ nieuwe ondersteunings aanvraag**. Volg de aanwijzingen om ons informatie te geven over uw probleem. We suggereren een aantal mogelijke oplossingen, verzamelen Details over het probleem en helpen u bij het indienen en bijhouden van de ondersteunings aanvraag.

   ![Nieuw ondersteuningsverzoek](./media/how-to-create-azure-support-request/newsupportrequest2lower.png)

### <a name="go-to-help--support-from-a-resource-menu"></a>Ga naar Help en ondersteuning vanuit een resource menu

Als u een ondersteunings aanvraag in de context van de resource wilt starten, bent u momenteel bezig met het volgende:

1. Klik in het menu resource, in de sectie **ondersteuning en probleem oplossing** , op **nieuwe ondersteunings aanvraag**.

   ![In de context](./media/how-to-create-azure-support-request/incontext2lower.png)

2. Volg de aanwijzingen om ons informatie te geven over het probleem dat u hebt. Wanneer u het proces voor de ondersteunings aanvraag vanuit de resource start, zijn sommige opties vooraf voor u geselecteerd.

## <a name="create-a-support-request"></a>Een ondersteuningsaanvraag maken

U wordt begeleid bij een aantal stappen voor het verzamelen van informatie over uw probleem en het oplossen van problemen. Elke stap wordt beschreven in de volgende secties.

### <a name="basics"></a>Basisbeginselen

De eerste stap van het proces voor ondersteunings aanvragen verzamelt basis informatie over uw probleem en uw ondersteunings plan.

Op het tabblad **basis beginselen** van de **nieuwe ondersteunings aanvraag**gebruikt u de selecters om ons te laten weten wat het probleem is. Eerst identificeert u enkele algemene categorieën voor het probleem type en kiest u het gerelateerde abonnement. Als u de service selecteert (bijvoorbeeld ' virtuele machine met Windows ') en de resource (de naam van uw virtuele machine) de volgende stap is om hulp te krijgen. Geef een korte beschrijving van het probleem in uw eigen woorden en **Selecteer vervolgens probleem type** om specifiekere informatie te verkrijgen.

![Blade Grondbeginselen](./media/how-to-create-azure-support-request/basics2lower.png)

> [!NOTE]
> Azure biedt onbeperkte ondersteuning voor abonnements beheer (zoals facturering, quotum aanpassingen en account overdracht). Voor technische ondersteuning hebt u een ondersteunings abonnement nodig. Meer [informatie over ondersteunings abonnementen](https://azure.microsoft.com/support/plans).
>
>

### <a name="solutions"></a>Oplossingen

Nadat u basis informatie hebt verzameld, worden oplossingen hierna weer gegeven die u zelf kunt proberen. In sommige gevallen kunnen we zelfs een snelle diagnose uitvoeren. Oplossingen worden geschreven door Azure-technici en zullen de meest voorkomende problemen oplossen.

### <a name="details"></a>Details

Vervolgens verzamelen we aanvullende informatie over het probleem. Door in deze stap uitgebreide en gedetailleerde informatie te verstrekken, kunnen we uw ondersteunings aanvraag door sturen naar de juiste agent en kunnen ze de oorzaak van het probleem vaststellen.

Het is een goed idee om ons te laten weten wanneer het probleem is gestart en alle stappen om het te reproduceren. U kunt er ook voor kiezen om een bestand te uploaden, zoals een logboek bestand of uitvoer van diagnostische gegevens.

Nadat u alle informatie hebt over het probleem dat u ondervindt, kunt u kiezen hoe u ondersteuning krijgt. Selecteer in het gedeelte **ondersteunings methode** van het tabblad **Details** de ernst van de impact. Geef uw voorkeurs methode voor contact personen, een goede tijd om contact met u op te nemen en uw ondersteunings taal.

Vul vervolgens de sectie **contact gegevens** in zodat we weten hoe ze contact met u kunnen opnemen.

### <a name="review--create"></a>Beoordelen en maken

Vul alle vereiste gegevens op elk tabblad in en selecteer vervolgens **controleren + maken**. Controleer de details die u gaat verzenden naar ondersteuning. Ga terug naar een tabblad om zo nodig een wijziging aan te brengen. Wanneer u hebt nagevraagd of het ondersteunings verzoek is voltooid, selecteert u **maken**.

Een ondersteunings agent neemt contact met u op met de methode die u hebt opgegeven. Raadpleeg het [ondersteunings bereik en de reactie snelheid](https://azure.microsoft.com/support/plans/response/) voor informatie over de eerste reactie tijd.

## <a name="all-support-requests"></a>Alle ondersteunings aanvragen

U kunt de details en status van ondersteunings aanvragen bekijken door te gaan naar **Help en ondersteuning** >  **alle ondersteunings aanvragen**.

![Alle ondersteunings aanvragen](./media/how-to-create-azure-support-request/allrequestslower.png)

Op deze pagina kunt u de ondersteunings aanvragen filteren op basis van het abonnement, de aanmaak datum (UTC) en de status. Daarnaast kunt u op deze pagina ondersteunings aanvragen sorteren en zoeken.

Selecteer een ondersteunings aanvraag om details weer te geven, met inbegrip van de ernst en de verwachte tijd die het duurt voordat een ondersteunings agent reageert.

Als u de ernst van de aanvraag wilt wijzigen, selecteert u **bedrijfs impact**. Er wordt een lijst weer gegeven met de beschik bare ernst voor toewijzen.

> [!NOTE]
> Het maximale Ernst niveau is afhankelijk van uw ondersteunings plan. Meer [informatie over ondersteunings abonnementen](https://azure.microsoft.com/support/plans).
>
>
Bekijk de volgende video voor meer informatie over de ondersteunings opties voor Self-Help in Azure:

> [!VIDEO https://www.youtube.com/embed/gNhzR5FE9DY]

## <a name="next-steps"></a>Volgende stappen
* [Stuur ons uw feedback en suggesties](https://feedback.azure.com/forums/266794-support-feedback)
* Neem contact met ons op [Twitter](https://twitter.com/azuresupport)
* Hulp krijgen van uw collega's in de [MSDN-Forums](https://social.msdn.microsoft.com/Forums/azure)
* Meer informatie in de [Veelgestelde vragen over ondersteuning voor Azure](https://azure.microsoft.com/support/faq)
