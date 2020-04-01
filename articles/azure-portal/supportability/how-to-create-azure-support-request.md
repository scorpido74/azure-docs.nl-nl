---
title: Een Azure-ondersteuningsaanvraag maken | Microsoft Documenten
description: Klanten die hulp nodig hebben, kunnen de Azure-portal gebruiken om selfserviceoplossingen te vinden en ondersteuningsaanvragen te maken en te beheren.
services: Azure Supportability
author: ganganarayanan
manager: scotthit
ms.assetid: fd6841ea-c1d5-4bb7-86bd-0c708d193b89
ms.service: azure-supportability
ms.topic: article
ms.date: 03/31/2020
ms.author: kfollis
ms.openlocfilehash: 2b68b6ddf19d6b07475e7009b47e162bfb2d0d2f
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478983"
---
# <a name="how-to-create-an-azure-support-request"></a>Een ondersteuningsaanvraag maken voor Azure

## <a name="overview"></a>Overzicht

Met Azure u ondersteuningsaanvragen maken en beheren, ook wel ondersteuningstickets genoemd. U aanvragen maken en beheren in de [Azure-portal,](https://portal.azure.com)die in dit artikel wordt behandeld. U aanvragen ook programmatisch maken en beheren met behulp van de [REST API voor Azure-ondersteuningsticket.](/rest/api/support)

> [!NOTE]
> De URL van de Azure-portal is specifiek voor de Azure-cloud waarin uw organisatie is geïmplementeerd.
>
>* Azure-portal voor commercieel gebruik is:[https://portal.azure.com](https://portal.azure.com)
>* Azure-portal voor Duitsland is:[https://portal.microsoftazure.de](https://portal.microsoftazure.de)
>* Azure-portal voor de Amerikaanse overheid is:[https://portal.azure.us](https://portal.azure.us)
>
>

Op basis van feedback van klanten hebben we de ervaring met ondersteuningsaanvragen bijgewerkt om ons te concentreren op drie hoofddoelen:

* **Gestroomlijnd:** maak ondersteuning en probleemoplossing eenvoudig te vinden en vereenvoudig de manier waarop u een ondersteuningsaanvraag indient.
* **Geïntegreerd:** u eenvoudig een ondersteuningsaanvraag openen wanneer u een probleem met een Azure-bron oplost, zonder van context te veranderen.
* **Efficiënt:** verzamel de belangrijkste informatie die uw ondersteuningsmedewerker nodig heeft om uw probleem efficiënt op te lossen.

## <a name="getting-started"></a>Aan de slag

U **hulp + ondersteuning** krijgen in de Azure-portal. Het is beschikbaar in het Azure-portalmenu, de algemene koptekst of het resourcemenu voor een service. Voordat u een ondersteuningsverzoek indienen, moet u over de juiste machtigingen beschikken.

### <a name="role-based-access-control"></a>Op rollen gebaseerd toegangsbeheer

Als u een ondersteuningsverzoek wilt maken, moet u [eigenaar,](../../role-based-access-control/built-in-roles.md#owner) [inzender](../../role-based-access-control/built-in-roles.md#contributor) of toegewezen zijn aan de rol [Inzender van ondersteuningsaanvragen](../../role-based-access-control/built-in-roles.md#support-request-contributor) op abonnementsniveau. Als u een ondersteuningsaanvraag zonder abonnement wilt maken, bijvoorbeeld het Azure Active Directory-scenario (AAD), moet u een [beheerder](../../active-directory/users-groups-roles/directory-assign-admin-roles.md)zijn.

### <a name="go-to-help--support-from-the-global-header"></a>Ga naar Help + ondersteuning vanuit de globale koptekst

Ga als een ondersteuningsaanvraag overal in de Azure-portal te werk:

1. Selecteer het **?** in de globale header. Selecteer vervolgens **Help + ondersteuning**.

   ![Help en ondersteuning](./media/how-to-create-azure-support-request/helpandsupportnewlower.png)

2. Selecteer **Nieuw ondersteuningsverzoek**. Volg de aanwijzingen om ons te voorzien van informatie over uw probleem. We stellen een aantal mogelijke oplossingen voor, verzamelen details over het probleem en helpen u het ondersteuningsverzoek in te dienen en bij te houden.

   ![Nieuw ondersteuningsverzoek](./media/how-to-create-azure-support-request/newsupportrequest2lower.png)

### <a name="go-to-help--support-from-a-resource-menu"></a>Ga naar Help + ondersteuning vanuit een resourcemenu

Als u een ondersteuningsaanvraag wilt starten in de context van de resource, werkt u momenteel met:

1. Selecteer in het resourcemenu in de sectie **Ondersteuning + probleemoplossing** de optie **Nieuw ondersteuningsverzoek**.

   ![In context](./media/how-to-create-azure-support-request/incontext2lower.png)

2. Volg de aanwijzingen om ons te voorzien van informatie over het probleem dat u ondervindt. Wanneer u het ondersteuningsaanvraagproces vanuit de resource start, zijn sommige opties vooraf voor u geselecteerd.

## <a name="create-a-support-request"></a>Een ondersteuningsaanvraag maken

We nemen je een aantal stappen onder de rug om informatie over je probleem te verzamelen en je te helpen het op te lossen. Elke stap wordt beschreven in de volgende secties.

### <a name="basics"></a>Basisbeginselen

De eerste stap van het ondersteuningsaanvraagproces verzamelt basisinformatie over uw probleem en uw ondersteuningsplan.

Gebruik op het tabblad **Basisbeginselen** van **Nieuw ondersteuningsverzoek**de selecteurs om ons over het probleem te vertellen. Eerst identificeert u enkele algemene categorieën voor het probleemtype en kiest u het gerelateerde abonnement. Selecteer de service, bijvoorbeeld **Virtual Machine met Windows**. Selecteer de resource, zoals de naam van uw virtuele machine. Beschrijf het probleem in uw eigen woorden en **selecteer probleemtype** om specifieker te worden.

![Blade Grondbeginselen](./media/how-to-create-azure-support-request/basics2lower.png)

> [!NOTE]
> Azure biedt onbeperkte ondersteuning voor abonnementsbeheer, waaronder facturering, quotaaanpassingen en accountoverdrachten. Voor technische ondersteuning hebt u een ondersteuningsplan nodig. [Meer informatie over ondersteuningsplannen](https://azure.microsoft.com/support/plans).
>
>

### <a name="solutions"></a>Oplossingen

Na het verzamelen van basisinformatie, laten we u vervolgens oplossingen zien om het zelf te proberen. In sommige gevallen kunnen we zelfs een snelle diagnose uitvoeren. Oplossingen zijn geschreven door Azure-technici en lossen de meest voorkomende problemen op.

### <a name="details"></a>Details

Vervolgens verzamelen we aanvullende details over het probleem. Het verstrekken van grondige en gedetailleerde informatie in deze stap helpt ons uw ondersteuningsverzoek door te sturen naar de juiste agent.

Vertel ons indien mogelijk wanneer het probleem is begonnen en welke stappen het kan reproduceren. U een bestand uploaden, zoals een logboekbestand of uitvoer uit diagnostische gegevens.

Nadat we alle informatie over het probleem hebben, kies dan hoe we ondersteuning krijgen. Selecteer in de sectie **Ondersteuningsmethode** **details**de ernst van de impact. Geef uw gewenste contactmethode, een goed moment om contact met u op te nemen en uw ondersteuningstaal.

Vul vervolgens de sectie **Contactgegevens** in, zodat we weten hoe we contact met u kunnen opnemen.

### <a name="review--create"></a>Controleren + maken

Vul alle vereiste informatie op elk tabblad in en selecteer **Controleren + maken**. Controleer de details die u naar Ondersteuning stuurt. Ga terug naar een tabblad om een wijziging aan te brengen indien nodig. Als u tevreden bent dat de ondersteuningsaanvraag is voltooid, selecteert u **Maken**.

Een ondersteuningsmedewerker neemt contact met u op via de door u aangegeven methode. Zie [Ondersteuningsbereik en responsiviteit](https://azure.microsoft.com/support/plans/response/)voor informatie over de initiële responstijd.

## <a name="all-support-requests"></a>Alle ondersteuningsaanvragen

U de details en status van ondersteuningsaanvragen bekijken door naar **Help + ondersteuning** >  **alle ondersteuningsverzoeken**te gaan.

![Alle ondersteuningsaanvragen](./media/how-to-create-azure-support-request/allrequestslower.png)

Op deze pagina u ondersteuningsaanvragen filteren op **Abonnements-** **en Aanmaakdatum** (UTC) en **Status**. Daarnaast u op deze pagina ondersteuningsverzoeken sorteren en zoeken.

Selecteer een ondersteuningsverzoek om details weer te geven, inclusief de ernst en de verwachte tijd die een ondersteuningsmedewerker nodig heeft om te reageren.

Als u de ernst van de aanvraag wilt wijzigen, selecteert u **De impact van bedrijven**. Kies uit een lijst met ernst die u wilt toewijzen.

> [!NOTE]
> Het maximale ernstniveau is afhankelijk van uw ondersteuningsplan. [Meer informatie over ondersteuningsplannen](https://azure.microsoft.com/support/plans).
>
>
Bekijk de video voor meer informatie over opties voor zelfhulpondersteuning in Azure:

> [!VIDEO https://www.youtube.com/embed/gNhzR5FE9DY]

## <a name="next-steps"></a>Volgende stappen

* [Stuur ons uw feedback en suggesties](https://feedback.azure.com/forums/266794-support-feedback)
* Neem contact met ons op [Twitter](https://twitter.com/azuresupport)
* Krijg hulp van uw collega's in de [MSDN-forums](https://social.msdn.microsoft.com/Forums/azure)
* Meer informatie in [de veelgestelde vragen over Azure Support](https://azure.microsoft.com/support/faq)
