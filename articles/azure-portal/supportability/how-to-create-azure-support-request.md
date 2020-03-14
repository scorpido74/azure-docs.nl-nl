---
title: Een ondersteunings aanvraag voor Azure maken | Microsoft Docs
description: Klanten die hulp nodig hebben, kunnen de Azure Portal gebruiken om self-service oplossingen te vinden en ondersteunings aanvragen te maken en te beheren.
services: Azure Supportability
author: ganganarayanan
manager: scotthit
ms.assetid: fd6841ea-c1d5-4bb7-86bd-0c708d193b89
ms.service: azure-supportability
ms.topic: article
ms.date: 01/23/2020
ms.author: kfollis
ms.openlocfilehash: 28a29bbb94f63657a69e873bb8f969e96ee92c76
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79248487"
---
# <a name="how-to-create-an-azure-support-request"></a>Een ondersteuningsaanvraag maken voor Azure

## <a name="overview"></a>Overzicht

Met Azure kunt u ondersteunings aanvragen maken en beheren, ook wel ondersteunings tickets genoemd. U kunt aanvragen maken en beheren in de [Azure Portal](https://portal.azure.com), die in dit artikel worden besproken. U kunt ook via een programma aanvragen maken en beheren met behulp van de [ondersteunings ticket rest API van Azure](/rest/api/support).

> [!NOTE]
> De Azure Portal-URL is specifiek voor de Azure-Cloud waar uw organisatie is geïmplementeerd.
>
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

U krijgt hulp en **ondersteuning** in de Azure Portal. Deze is beschikbaar via het Azure Portal menu, de globale koptekst of het resource menu voor een service. Voordat u een ondersteunings aanvraag kunt indienen, moet u de juiste machtigingen hebben.

### <a name="role-based-access-control"></a>Op rollen gebaseerd toegangsbeheer

Als u een ondersteunings aanvraag wilt maken, moet u een beheerder zijn of worden toegewezen aan de rol [Inzender voor ondersteunings aanvragen](../../role-based-access-control/built-in-roles.md#support-request-contributor) op abonnements niveau.

### <a name="go-to-help--support-from-the-global-header"></a>Ga naar Help en ondersteuning vanuit de Global header

Als u een ondersteunings aanvraag wilt starten vanaf elke locatie in de Azure Portal:

1. Selecteer de **?** in de globale header. Selecteer vervolgens **Help + ondersteuning**.

   ![Help en ondersteuning](./media/how-to-create-azure-support-request/helpandsupportnewlower.png)

2. Selecteer **Nieuwe ondersteuningsaanvraag**. Volg de aanwijzingen om ons informatie te geven over uw probleem. We suggereren een aantal mogelijke oplossingen, verzamelen Details over het probleem en helpen u bij het indienen en bijhouden van de ondersteunings aanvraag.

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

Op het tabblad **basis beginselen** van de **nieuwe ondersteunings aanvraag**gebruikt u de selecters om ons te laten weten wat het probleem is. Eerst identificeert u enkele algemene categorieën voor het probleem type en kiest u het gerelateerde abonnement. Selecteer de service, bijvoorbeeld **virtuele machine met Windows**. Selecteer de resource, zoals de naam van de virtuele machine. Beschrijf het probleem in uw eigen woorden en **Selecteer vervolgens probleem type** voor specifiekere informatie.

![Blade Grondbeginselen](./media/how-to-create-azure-support-request/basics2lower.png)

> [!NOTE]
> Azure biedt onbeperkte ondersteuning voor abonnements beheer, met inbegrip van facturering, quotum aanpassingen en account overdrachten. Voor technische ondersteuning hebt u een ondersteunings abonnement nodig. Meer [informatie over ondersteunings abonnementen](https://azure.microsoft.com/support/plans).
>
>

### <a name="solutions"></a>Oplossingen

Nadat u basis informatie hebt verzameld, worden oplossingen hierna weer gegeven die u zelf kunt proberen. In sommige gevallen kunnen we zelfs een snelle diagnose uitvoeren. Oplossingen worden geschreven door Azure-technici en zullen de meest voorkomende problemen oplossen.

### <a name="details"></a>Details

Vervolgens verzamelen we aanvullende informatie over het probleem. Door in deze stap uitgebreide en gedetailleerde informatie te verstrekken, kunnen we uw ondersteunings aanvraag naar de juiste agent routeren.

Laat ons, indien mogelijk, weten wanneer het probleem is gestart en alle stappen om het te reproduceren. U kunt een bestand, zoals een logboek bestand of uitvoer van diagnostische gegevens, uploaden.

Nadat u alle informatie over het probleem hebt, kiest u hoe u ondersteuning kunt krijgen. Selecteer in de sectie **ondersteunings methode** de **ernst van de**impact. Geef uw voorkeurs methode voor contact personen, een goede tijd om contact met u op te nemen en uw ondersteunings taal.

Vul vervolgens de sectie **contact gegevens** in zodat we weten hoe ze contact met u kunnen opnemen.

### <a name="review--create"></a>Controleren en maken

Vul alle vereiste gegevens op elk tabblad in en selecteer vervolgens **controleren + maken**. Controleer de details die u gaat verzenden naar ondersteuning. Ga terug naar een tabblad om zo nodig een wijziging aan te brengen. Wanneer u hebt nagevraagd of het ondersteunings verzoek is voltooid, selecteert u **maken**.

Een ondersteunings agent neemt contact met u op met de methode die u hebt opgegeven. Zie voor meer informatie over de eerste reactie tijd het [ondersteunings bereik en de reactie snelheid](https://azure.microsoft.com/support/plans/response/).

## <a name="all-support-requests"></a>Alle ondersteunings aanvragen

U kunt de details en status van ondersteunings aanvragen bekijken door te gaan naar **Help en ondersteuning** >  **alle ondersteunings aanvragen**.

![Alle ondersteunings aanvragen](./media/how-to-create-azure-support-request/allrequestslower.png)

Op deze pagina kunt u de ondersteunings aanvragen filteren op basis van het **abonnement**, de **aanmaak** datum (UTC) en de **status**. Daarnaast kunt u op deze pagina ondersteunings aanvragen sorteren en zoeken.

Selecteer een ondersteunings aanvraag om details weer te geven, met inbegrip van de ernst en de verwachte tijd die het duurt voordat een ondersteunings agent reageert.

Als u de ernst van de aanvraag wilt wijzigen, selecteert u **bedrijfs impact**. Kies uit een lijst met de ernst die u wilt toewijzen.

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
