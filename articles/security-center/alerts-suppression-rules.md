---
title: Regels voor het onderdrukken van waarschuwingen gebruiken om onjuiste positieve of andere ongewenste beveiligings waarschuwingen in Azure Security Center te onderdrukken.
description: In dit artikel wordt uitgelegd hoe u de onderdrukkings regels van Azure Security Center gebruikt om ongewenste beveiligings waarschuwingen te verbergen.
author: memildin
manager: rkarlin
services: security-center
ms.author: memildin
ms.date: 05/04/2020
ms.service: security-center
ms.topic: conceptual
ms.openlocfilehash: 341373c9a8429f335f3064db7a94973d34e0ca1c
ms.sourcegitcommit: 1a0dfa54116aa036af86bd95dcf322307cfb3f83
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/10/2020
ms.locfileid: "88042497"
---
# <a name="suppress-alerts-from-azure-security-centers-threat-protection"></a>Waarschuwingen onderdrukken van de bedreigings beveiliging van Azure Security Center

Op deze pagina wordt uitgelegd hoe u regels voor het onderdrukken van waarschuwingen kunt gebruiken om foutieve positieve of andere ongewenste beveiligings waarschuwingen in Azure Security Center te onderdrukken.

## <a name="availability"></a>Beschikbaarheid

|Aspect|Details|
|----|:----|
|Release status:|Preview|
|Koers|Gratis laag<br>(De meeste beveiligings waarschuwingen gelden alleen voor de Standard-laag)|
|Vereiste rollen en machtigingen:|**Beveiligings beheerder** en- **eigenaar** kunnen regels maken/verwijderen.<br>**Beveiligings lezer** en **lezer** kunnen regels weer geven.|
|Clouds|![Ja](./media/icons/yes-icon.png) Commerciële Clouds<br>![Ja](./media/icons/yes-icon.png) National/soeverein (US Gov, China gov, andere gov)|
|||




## <a name="what-are-suppression-rules"></a>Wat zijn onderdrukkings regels?

De bedreigings beveiligings onderdelen van Azure Security Center het detecteren van bedreigingen in elk gebied van uw omgeving en het genereren van beveiligings waarschuwingen.

Wanneer één waarschuwing niet interessant of relevant is, kunt u deze hand matig verwijderen. U kunt ook de functie onderdrukkings regels gebruiken om in de toekomst automatisch vergelijk bare waarschuwingen te negeren. Normaal gesp roken gebruikt u een onderdrukkings regel voor het volgende:

- Waarschuwingen onderdrukken die u als foutieve positieven hebt geïdentificeerd

- Waarschuwingen onderdrukken die te vaak worden geactiveerd om nuttig te zijn

Met de onderdrukkings regels worden de criteria gedefinieerd waarvoor waarschuwingen automatisch moeten worden genegeerd.

> [!CAUTION]
> Beveiligings waarschuwingen onderdrukken vermindert de bedreigings beveiliging van Security Center. U moet de mogelijke gevolgen van elke onderdrukkings regel zorgvuldig controleren en deze na verloop van tijd controleren.

![Waarschuwings onderdrukkings regel maken](media\alerts-suppression-rules\create-suppression-rule.gif)

## <a name="create-a-suppression-rule"></a>Een onderdrukkings regel maken

Er zijn een paar manieren waarop u regels kunt maken om ongewenste beveiligings waarschuwingen te onderdrukken:

- Gebruik Azure Policy om waarschuwingen op het niveau van de beheer groep te onderdrukken.

- Als u waarschuwingen op het abonnements niveau wilt onderdrukken, kunt u de Azure Portal of de REST API gebruiken zoals hieronder wordt uitgelegd

Onderdrukkings regels kunnen alleen waarschuwingen negeren die al zijn geactiveerd voor de geselecteerde abonnementen.

Een regel rechtstreeks in het Azure Portal maken:

1. Op de pagina beveiligings waarschuwingen van Security Center:

    - Zoek de specifieke waarschuwing die u niet meer wilt weer geven en selecteer in het menu met het weglatings teken (...) voor de waarschuwing de optie **onderdrukkings regel maken**:

        [![* * Onderdrukkings regel maken * * optie](media/alerts-suppression-rules/auto-dismiss-future-option.png)](media/alerts-suppression-rules/auto-dismiss-future-option.png#lightbox)

    - Of selecteer de koppeling **onderdrukt regels** boven aan de pagina en selecteer op de pagina onderdrukkings regels de optie **nieuwe onderdrukkings regel maken**:

        ![Nieuwe onderdrukkings regel maken * * knop](media/alerts-suppression-rules/create-new-suppression-rule.png)

1. Voer in het deel venster nieuwe onderdrukkings regel de details van de nieuwe regel in.

    - Uw regel kan de waarschuwing voor **alle resources** negeren, zodat u geen waarschuwingen zoals deze in de toekomst ontvangt. 
    
    - Uw regel kan de waarschuwing **voor specifieke criteria** negeren: wanneer deze betrekking heeft op een specifiek IP-adres, een proces naam, een gebruikers account, een Azure-resource of een locatie.

    > [!TIP]
    > Als u de pagina nieuwe regel van een specifieke waarschuwing hebt geopend, worden de waarschuwing en het abonnement automatisch geconfigureerd in de nieuwe regel. Als u de koppeling **nieuwe onderdrukkings regel maken** hebt gebruikt, zullen de geselecteerde abonnementen overeenkomen met het huidige filter in de portal.

    [![Deel venster voor het maken van de onderdrukkings regel](media/alerts-suppression-rules/new-suppression-rule-pane.png)](media/alerts-suppression-rules/new-suppression-rule-pane.png#lightbox)

1. Voer de details van de regel in:

    - **Naam** : een naam voor de regel. Regel namen moeten beginnen met een letter of een cijfer, moeten tussen de 2 en 50 tekens lang zijn en mogen geen andere symbolen dan streepjes (-) of onderstrepings teken (_) bevatten. 
    - **Status** : ingeschakeld of uitgeschakeld.
    - **Reden** : Selecteer een van de ingebouwde redenen of Overig als ze niet voldoen aan uw behoeften.
    - **Verval datum** : een eind datum en-tijd voor de regel. Regels kunnen Maxi maal zes maanden worden uitgevoerd.

1. U kunt de regel eventueel testen met behulp van de knop **simuleren** om te zien hoeveel waarschuwingen er zouden zijn genegeerd als deze regel actief was.

1. Sla de regel op. 

## <a name="edit-a-suppression-rules"></a>Een onderdrukkings regel bewerken

Als u een regel wilt bewerken die u hebt gemaakt, gebruikt u de pagina onderdrukkings regels.

1. Selecteer op de pagina beveiligings waarschuwingen van Security Center de koppeling **onderdrukt regels** aan de bovenkant van de pagina.

1. De pagina onderdrukkings regels wordt geopend met alle regels voor de geselecteerde abonnementen.

    [![Lijst met onderdrukkings regels](media/alerts-suppression-rules/suppression-rules-page.png)](media/alerts-suppression-rules/suppression-rules-page.png#lightbox)

1. Als u één regel wilt bewerken, opent u het menu met weglatings tekens (...) voor de regel en selecteert u **bewerken**.

1. Breng de benodigde wijzigingen aan en selecteer **Toep assen**. 

## <a name="delete-a-suppression-rule"></a>Een onderdrukkings regel verwijderen

Als u een of meer regels die u hebt gemaakt, wilt verwijderen, gebruikt u de pagina onderdrukkings regels.

1. Selecteer op de pagina beveiligings waarschuwingen van Security Center de koppeling **onderdrukt regels** aan de bovenkant van de pagina.

1. De pagina onderdrukkings regels wordt geopend met alle regels voor de geselecteerde abonnementen.

1. Als u één regel wilt verwijderen, opent u het menu met weglatings tekens (...) voor de regel en selecteert u **verwijderen**.

1. Als u meerdere regels wilt verwijderen, schakelt u de selectie vakjes in voor de regels die moeten worden verwijderd en selecteert u **verwijderen**.

    ![Een of meer onderdrukkings regels verwijderen](media/alerts-suppression-rules/delete-multiple-alerts.png)

## <a name="view-suppressed-alerts"></a>Onderdrukt waarschuwingen weer geven

Waarschuwingen die overeenkomen met uw ingeschakelde onderdrukkings regels worden nog steeds gegenereerd, maar de status wordt ingesteld op **genegeerd**. U kunt de status bekijken in de Azure Portal of u hebt echter toegang tot uw Security Center beveiligings waarschuwingen. 

> [!TIP]
> [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) maakt geen incidenten voor onderdrukte waarschuwingen. Voor andere Siem's kunt u onderdrukt waarschuwingen filteren met behulp van de waarschuwingen ' status ' (' genegeerd ').

Gebruik het filter van Security Center om waarschuwingen weer te geven die door uw regels zijn genegeerd.

* Open op de pagina beveiligings waarschuwingen van Security Center de filter opties en selecteer **genegeerd**.  

   [![Genegeerde waarschuwingen weer geven](media/alerts-suppression-rules/view-dismissed-alerts.png)](media/alerts-suppression-rules/view-dismissed-alerts.png#lightbox)


## <a name="create-and-manage-suppression-rules-with-the-api"></a>Onderdrukkings regels maken en beheren met de API

U kunt regels voor het onderdrukken van waarschuwingen maken, weer geven of verwijderen via de REST API van Security Center. 

De relevante HTTP-methoden voor onderdrukkings regels in de REST API zijn:

- **Put**: voor het maken of bijwerken van een onderdrukkings regel in een opgegeven abonnement.

- **Ophalen**:

    - Alle regels weer geven die zijn geconfigureerd voor een opgegeven abonnement. Met deze methode wordt een matrix van de toepasselijke regels geretourneerd.

    - Om de details van een specifieke regel voor een opgegeven abonnement op te halen. Deze methode retourneert één onderdrukkings regel.

    - Om de impact van een onderdrukkings regel nog steeds in de ontwerp fase te simuleren. Deze aanroep geeft aan welke van uw bestaande waarschuwingen zouden zijn genegeerd als de regel actief was.

- **Verwijderen**: Hiermee verwijdert u een bestaande regel (maar wijzigt u de status van waarschuwingen die al zijn genegeerd).

Zie de [API-documentatie](https://docs.microsoft.com/rest/api/securitycenter/)voor gedetailleerde informatie en voor beelden van gebruik. 


## <a name="next-steps"></a>Volgende stappen

In dit artikel worden de onderdrukkings regels in Azure Security Center beschreven waarmee automatisch ongewenste waarschuwingen worden genegeerd.

Voor meer informatie over beveiligings waarschuwingen in Azure Security Center raadpleegt u de volgende pagina's:

- [Beveiligings waarschuwingen en de Bewaar keten van de bedoeling](alerts-reference.md) : een referentie handleiding voor de beveiligings waarschuwingen die u kunt zien in de module bedreigings beveiliging van Azure Security Center.
- [Beveiliging tegen bedreigingen in azure Security Center](threat-protection.md) : een beschrijving van de vele aspecten van uw omgeving die worden bewaakt door de Threat Protection-module van Azure Security Center.