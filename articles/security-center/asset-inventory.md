---
title: Bedrijfs voorraad van Azure Security Center
description: Meer informatie Azure Security Center over de ervaring met het beheer van bedrijfs middelen met volledige zicht baarheid van al uw Security Center bewaakte resources.
author: memildin
manager: rkarlin
services: security-center
ms.author: memildin
ms.date: 08/11/2020
ms.service: security-center
ms.topic: conceptual
ms.openlocfilehash: f3a542cd62c3d593dbc0cce7982d47222e9a7c88
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/31/2020
ms.locfileid: "89181100"
---
# <a name="explore-and-manage-your-resources-with-asset-inventory-and-management-tools"></a>Uw resources verkennen en beheren met Asset Inventory and management tools

De pagina Asset Inventory van Azure Security Center biedt één pagina voor het weer geven van de beveiligings postuur van de resources waarmee u verbinding hebt gemaakt met Security Center. 

Security Center regel matig de beveiligings status van uw Azure-resources analyseren om mogelijke beveiligings problemen te identificeren. Vervolgens krijgt u aanbevelingen voor het oplossen van deze beveiligings problemen.

Wanneer een resource openstaande aanbevelingen heeft, worden deze weer gegeven in de inventarisatie.

Gebruik deze weer gave en de bijbehorende filters om deze vragen te beantwoorden:

- Welke van mijn abonnementen op de Standard-laag hebben openstaande aanbevelingen?
- Op welke van mijn machines met het label productie ontbreekt de Log Analytics-agent?
- Hoeveel van mijn machines hebben uitstaande aanbevelingen voor een label met een specifieke tag?
- Hoeveel resources in een specifieke resource groep hebben beveiligings bevindingen van een beveiligings evaluatie service?

De mogelijkheden voor het beheer van middelen voor dit hulp programma zijn aanzienlijk en blijven groeien. 


## <a name="availability"></a>Beschikbaarheid

|Aspect|Details|
|----|:----|
|Release status:|Preview|
|Koers|Gratis|
|Vereiste rollen en machtigingen:|Alle gebruikers|
|Clouds|![Ja](./media/icons/yes-icon.png) Commerciële Clouds<br>![Nee](./media/icons/no-icon.png) National/soeverein (US Gov, China gov, andere gov)|
|||


## <a name="what-are-the-key-features-of-asset-inventory"></a>Wat zijn de belangrijkste functies van Asset Inventory?

De pagina inventarisatie bevat de volgende hulpprogram ma's:

- **Samen vattingen** : voordat u filters definieert, ziet u boven aan de inventarisatie weergave een duidelijke Stripe van waarden:

    - **Totaal resources**: het totale aantal resources dat is verbonden met Security Center.
    - **Slechte resources**: bronnen met actieve beveiligings aanbevelingen. Meer [informatie over beveiligings aanbevelingen](https://docs.microsoft.com/azure/security-center/security-center-recommendations).
    - Niet- **bewaakte resources**: bronnen met problemen met de agent bewaking-ze hebben de log Analytics agent geïmplementeerd, maar de agent verzendt geen gegevens of heeft andere status problemen.

- **Filters** : de meerdere filters boven aan de pagina bieden een manier om de lijst met resources snel te verfijnen op basis van de vraag die u probeert te beantwoorden. Als u bijvoorbeeld de vraag wilt beantwoorden op *welke van mijn machines met het label productie ontbreekt de log Analytics agent?* u kunt het **bewakings** filter van de agent combi neren met het filter **Tags** , zoals wordt weer gegeven in de volgende clip:

    ![Filteren op productie resources die niet worden bewaakt](./media/asset-inventory/filtering-to-prod-unmonitored.gif)

    Zodra u filters hebt toegepast, worden de samenvattings waarden bijgewerkt om te koppelen aan de query resultaten. 

- **Export opties** -inventaris biedt de mogelijkheid om de resultaten van de geselecteerde filter opties te exporteren naar een CSV-bestand. Daarnaast kunt u de query zelf exporteren naar Azure resource Graph Explorer om de KQL-query verder te verfijnen, op te slaan of te wijzigen.

    ![Export opties van de inventaris](./media/asset-inventory/inventory-export-options.png)

    > [!TIP]
    > De KQL-documentatie biedt een Data Base met een aantal voorbeeld gegevens in combi natie met enkele eenvoudige query's om het ' gevoel ' voor de taal te krijgen. [Meer informatie vindt u in deze KQL-zelf studie](https://docs.microsoft.com/azure/data-explorer/kusto/query/tutorial?pivots=azuredataexplorer).

- **Asset Management-opties** : inventaris biedt u de mogelijkheid om complexe detectie query's uit te voeren. Wanneer u de resources hebt gevonden die overeenkomen met uw query's, biedt de inventarisatie snelkoppelingen voor bewerkingen, zoals:

    - Tags toewijzen aan de gefilterde resources: Selecteer de selectie vakjes naast de resources die u wilt labelen
    - Nieuwe servers onboarden naar Security Center: gebruik de werkbalk knop **niet-Azure-servers toevoegen**


## <a name="how-does-asset-inventory-work"></a>Hoe werkt Asset Inventory?

Asset Inventory maakt gebruik van [Azure resource Graph (ARG)](https://docs.microsoft.com/azure/governance/resource-graph/), een Azure-service die de mogelijkheid biedt om de postuur gegevens van Security Center te doorzoeken op meerdere abonnementen.

ARG is ontworpen om efficiënte bronnen te verkennen met de mogelijkheid om op schaal te zoeken.

Met behulp van de [Kusto-query taal (KQL)](https://docs.microsoft.com/azure/data-explorer/kusto/query/)kan de inventaris van de activa snel dieper inzicht verkrijgen door kruis verwijzingen naar ASC-gegevens met andere bron eigenschappen.


## <a name="how-to-use-asset-inventory"></a>Inventarisatie van activa gebruiken

1. Selecteer op de zijbalk van Security Center de optie **inventaris**.

1. Als u een specifieke resource wilt weer geven, voert u de naam in het vak **filteren op naam** in.

1. Selecteer de relevante opties in de filters om de specifieke query te maken die u wilt uitvoeren.

    ![Inventarisatie filters](./media/asset-inventory/inventory-filters.png)

    Standaard worden de resources gesorteerd op het aantal actieve beveiligings aanbevelingen.

    > [!IMPORTANT]
    > De opties in elk filter zijn specifiek voor de resources in de momenteel geselecteerde abonnementen **en** uw selecties in de andere filters.
    >
    > Als u bijvoorbeeld slechts één abonnement hebt geselecteerd en het abonnement geen resources heeft met openstaande beveiligings aanbevelingen om te herstellen (0 slechte bronnen), bevat het filter **aanbevelingen** geen opties. 

1. Als u de **beveiligings resultaten** wilt gebruiken, moet u de vrije tekst van de id, de beveiligings controle of de CVE-naam van een beveiligings probleem bepalen dat kan worden gefilterd op de betrokken resources:

    ![' Beveiligings resultaten bevatten ' filter](./media/asset-inventory/security-findings-contain-elements.png)

    > [!TIP]
    > De **beveiligings resultaten bevatten** en **Tags** filters accepteren slechts één waarde. Gebruik filters toevoegen om op meer dan één filter **toe te voegen**.

1. Als u het filter **prijs categorie** wilt gebruiken, selecteert u een of meer opties (gratis, gedeeltelijk of standaard):

    - **Gratis** -resources die zich in de gratis prijs categorie bevinden
    - **Standard** -resources die zich op de prijs categorie Standard bevinden
    - **Gedeeltelijk** : dit is van toepassing op abonnementen die zich op de prijs categorie Standard bevinden, maar waarvoor enkele van de optionele beveiligings abonnementen zijn uitgeschakeld. Het volgende abonnement bevindt zich bijvoorbeeld op de Standard-laag, maar heeft vijf elementen van de laag standaard uitgeschakeld. 

        ![Prijs categorie abonnement op Standard (gedeeltelijk)](./media/asset-inventory/pricing-tier-partial.png)

1. Als u de resultaten van uw query verder wilt onderzoeken, selecteert u de resources die u interesseren.

1. Selecteer eventueel **weer gave in resource Graph Explorer** om de query te openen in resource Graph Explorer.

    ![Inventarisatie query in ARG](./media/asset-inventory/inventory-query-in-resource-graph-explorer.png)

1. Als u sommige filters hebt gedefinieerd en de pagina hebt geopend, worden Security Center de resultaten niet automatisch bijgewerkt. Wijzigingen in resources zijn niet van invloed op de weer gegeven resultaten tenzij u de pagina hand matig opnieuw laadt of **vernieuwen**selecteert.


## <a name="faq---inventory"></a>Veelgestelde vragen-inventaris

### <a name="why-arent-all-of-my-subscriptions-machines-storage-accounts-etc-shown"></a>Waarom worden niet alle abonnementen, machines, opslag accounts, enz. weer gegeven?

De inventaris weergave bevat uw resources van een CSPM-perspectief (Cloud Security postuur Management). De filters retour neren niet alle resources in uw omgeving. alleen de taken met openstaande (of ' Active ') aanbevelingen. 

Als u bijvoorbeeld negen abonnementen hebt, maar er nog maar acht aanbevelingen hebt, worden de acht abonnementen met actieve aanbevelingen alleen weer geven als u filtert op **resource type = abonnementen** .

![Niet alle hoeven worden geretourneerd wanneer er geen actieve aanbevelingen zijn](./media/asset-inventory/filtered-subscriptions-some.png)


### <a name="why-do-some-of-my-resources-show-blank-values-in-the-pricing-or-agent-monitoring-columns"></a>Waarom worden in sommige resources lege waarden weer gegeven in de kolommen prijzen of agent bewaking?

Niet alle Security Center bewaakte resources hebben agents. Bijvoorbeeld Azure Storage accounts of PaaS resources zoals schijven, Logic Apps, Data Lake analyse en Event hub.

Wanneer de prijs-of agent bewaking niet relevant is voor een resource, worden er niets weer gegeven in die kolommen met de inventarisatie.

![In sommige resources worden lege gegevens weer gegeven in de kolom agent bewaking of prijzen](./media/asset-inventory/agent-pricing-blanks.png)



## <a name="next-steps"></a>Volgende stappen

In dit artikel wordt de pagina Asset Inventory van Azure Security Center beschreven.

Zie de volgende pagina's voor meer informatie over gerelateerde hulpprogram ma's:

- [Azure-resource grafiek (ARG)](https://docs.microsoft.com/azure/governance/resource-graph/)

- [Kusto-querytaal (KQL)](https://docs.microsoft.com/azure/data-explorer/kusto/query/)