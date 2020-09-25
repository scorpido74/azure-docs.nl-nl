---
title: Azure Defender gebruiken voor container registers
description: Meer informatie over het gebruik van Azure Defender voor container registers voor het scannen van afbeeldingen in uw registers
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 04a7b7f40e1591a919668e940a883dde9a6f4689
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91302006"
---
# <a name="use-azure-defender-for-container-registries-to-scan-your-images-for-vulnerabilities"></a>Azure Defender gebruiken voor container registers om uw installatie kopieën te scannen op beveiligings problemen

Op deze pagina wordt uitgelegd hoe u de ingebouwde beveiligings scanner gebruikt om de container installatie kopieën te scannen die zijn opgeslagen in uw op Azure Resource Manager gebaseerde Azure Container Registry.

Als **Azure Defender voor container registers** is ingeschakeld, wordt de installatie kopie die u naar het REGI ster pusht onmiddellijk gescand. Daarnaast wordt elke afbeelding die in de afgelopen 30 dagen is opgehaald, ook gescand. 

Wanneer de scanner beveiligings Security Center problemen meldt, Security Center de bevindingen en gerelateerde informatie als aanbevelingen. Daarnaast bevatten de bevindingen gerelateerde informatie zoals herbemiddelings tappen, relevante CVEs, CVSS-scores en meer. U kunt de geïdentificeerde beveiligings problemen weer geven voor een of meer abonnementen of voor een specifiek REGI ster.

## <a name="availability"></a>Beschikbaarheid

|Aspect|Details|
|----|:----|
|Release status:|Algemeen beschikbaar (GA)|
|Koers|**Azure Defender voor container registers** wordt gefactureerd, zoals wordt weer gegeven op [de pagina met prijzen](security-center-pricing.md)|
|Ondersteunde registers en installatie kopieën:|![Ja ](./media/icons/yes-icon.png) door Linux gehoste ACR-registers die toegankelijk zijn via het open bare Internet en shell toegang bieden.<br>![Geen door ](./media/icons/no-icon.png) Windows gehoste ACR-registers.<br>![Geen ](./media/icons/no-icon.png) persoonlijke registers-Security Center vereist dat uw registers toegankelijk zijn via het open bare Internet. Security Center kan momenteel geen verbinding maken met, of scannen, registers met toegang beperkt met een firewall, een service-eind punt of persoonlijke eind punten zoals een persoonlijke Azure-koppeling.<br>![Geen ](./media/icons/no-icon.png) minimale afbeeldings afbeeldingen zoals [docker](https://hub.docker.com/_/scratch/) -werk afbeeldingen, of "Distroless"-installatie kopieën die alleen een toepassing en de runtime-afhankelijkheden bevatten zonder pakket beheer, shell of besturings systeem.|
|Vereiste rollen en machtigingen:|Rol van **beveiligings lezer** en [Azure container Registry lezer](https://docs.microsoft.com/azure/container-registry/container-registry-roles)|
|Clouds|![Yes](./media/icons/yes-icon.png) Commerciële Clouds<br>![No](./media/icons/no-icon.png) National/soeverein (US Gov, China gov, andere gov)|
|||


## <a name="identify-vulnerabilities-in-images-in-azure-container-registries"></a>Beveiligings problemen in installatie kopieën in azure-container registers identificeren 

1. Voor het inschakelen van beveiligings problemen met installatie kopieën die zijn opgeslagen in uw op Azure Resource Manager gebaseerde Azure Container Registry:

    1. Schakel **Azure Defender in voor container registers** voor uw abonnement.

        Security Center is nu gereed voor het scannen van afbeeldingen in uw registers.

        >[!NOTE]
        > Deze functie wordt per afbeelding in rekening gebracht.

1. Scans van afbeeldingen worden geactiveerd tijdens elke push-of import bewerking, en als de installatie kopie in de afgelopen 30 dagen is getrokken. 

    Wanneer de scan is voltooid (doorgaans na ongeveer 2 minuten, maar Maxi maal 15 minuten kan zijn), zijn bevindingen als Security Center aanbevelingen beschikbaar.

1. [Bekijk en herstel de resultaten zoals hieronder wordt uitgelegd](#view-and-remediate-findings).

## <a name="identify-vulnerabilities-in-images-in-other-container-registries"></a>Beveiligings problemen in installatie kopieën in andere container registers identificeren 

1. Gebruik de ACR-hulpprogram ma's om installatie kopieën naar het REGI ster te verplaatsen vanuit docker hub of micro soft Container Registry.  Wanneer het importeren is voltooid, worden de geïmporteerde installatie kopieën gescand door Azure Defender. 

    Meer informatie over [het importeren van container installatie kopieën naar een container register](../container-registry/container-registry-import-images.md)

    Wanneer de scan is voltooid (doorgaans na ongeveer 2 minuten, maar Maxi maal 15 minuten kan zijn), zijn bevindingen als Security Center aanbevelingen beschikbaar.

1. [Bekijk en herstel de resultaten zoals hieronder wordt uitgelegd](#view-and-remediate-findings).


## <a name="view-and-remediate-findings"></a>Bevindingen weer geven en herstellen

1. Ga naar de pagina **aanbevelingen** om de resultaten weer te geven. Als er problemen zijn gevonden, ziet u de aanbevolen **beveiligings problemen in azure container Registry installatie kopieën moeten worden hersteld**

    ![Aanbeveling voor het oplossen van problemen ](media/monitor-container-security/acr-finding.png)

1. Selecteer de aanbeveling. 

    De pagina met aanbevelings Details wordt geopend met aanvullende informatie. Deze informatie omvat de lijst met registers met kwets bare installatie kopieën ("betrokken bronnen") en de herstels tappen. 

1. Selecteer een specifiek REGI ster voor een overzicht van de opslag plaatsen in die kwets bare opslag plaatsen.

    ![Een REGI ster selecteren](media/monitor-container-security/acr-finding-select-registry.png)

    De pagina register Details wordt geopend met de lijst met betrokken opslag plaatsen.

1. Selecteer een specifieke opslag plaats om de opslag plaatsen daarin te bekijken die kwets bare installatie kopieën hebben.

    ![Een opslag plaats selecteren](media/monitor-container-security/acr-finding-select-repository.png)

    De pagina Details van opslag plaats wordt geopend. Hierin worden de kwets bare installatie kopieën weer gegeven, samen met een evaluatie van de ernst van de bevindingen.

1. Selecteer een specifieke installatie kopie om de beveiligings problemen weer te geven.

    ![Installatie kopieën selecteren](media/monitor-container-security/acr-finding-select-image.png)

    De lijst met bevindingen voor de geselecteerde afbeelding wordt geopend.

    ![Lijst met bevindingen](media/monitor-container-security/acr-findings.png)

1. Als u meer wilt weten over het zoeken, selecteert u de zoek optie. 

    Het detail venster met resultaten wordt geopend.

    [![Detail venster met resultaten](media/monitor-container-security/acr-finding-details-pane.png)](media/monitor-container-security/acr-finding-details-pane.png#lightbox)

    Dit deel venster bevat een gedetailleerde beschrijving van het probleem en koppelingen naar externe bronnen om de bedreigingen te helpen verminderen.

1. Volg de stappen in het gedeelte herstel van dit deel venster.

1. Wanneer u de stappen hebt uitgevoerd die nodig zijn om het beveiligings probleem op te lossen, vervangt u de installatie kopie in het REGI ster:

    1. Push de bijgewerkte installatie kopie. Hiermee wordt een scan geactiveerd. 
    
    1. Controleer de pagina met aanbevelingen voor de aanbevolen beveiligings problemen in Azure Container Registry installatie kopieën moeten worden hersteld. 
    
        Als de aanbeveling nog steeds wordt weer gegeven en de installatie kopie die u hebt verwerkt nog steeds wordt weer gegeven in de lijst met kwets bare installatie kopieën, controleert u de herstel stappen opnieuw.

    1. Wanneer u zeker weet dat de bijgewerkte installatie kopie is gepusht en gescand en niet meer wordt weer gegeven in de aanbeveling, verwijdert u de kwets bare afbeelding "oud" uit het REGI ster.


## <a name="disable-specific-findings-preview"></a>Specifieke bevindingen uitschakelen (preview-versie)

Als u een organisatie nodig hebt om een zoek functie te negeren in plaats van deze te herstellen, kunt u deze eventueel uitschakelen. Uitgeschakelde bevindingen hebben geen invloed op uw beveiligde Score of genereren ongewenste ruis.

Wanneer een Zoek resultaat overeenkomt met de criteria die u hebt gedefinieerd in de regels voor uitschakelen, worden deze niet weer gegeven in de lijst met bevindingen. Typische scenario's zijn onder andere:

- Schakel de resultaten met Ernst kleiner dan gemiddeld uit
- Uitkomsten uitschakelen die niet-patchbaar zijn
- Schakel de resultaten uit met een CVSS Score van minder dan 6,5
- Resultaten uitschakelen met specifieke tekst in de beveiligings controle of-categorie (bijvoorbeeld ' RedHat ', ' CentOS Security Update for sudo ')

> [!IMPORTANT]
> Als u een regel wilt maken, hebt u machtigingen nodig voor het bewerken van een beleid in Azure Policy.
>
> Meer informatie vindt u in de [RBAC-machtigingen in azure Policy](../governance/policy/overview.md#rbac-permissions-in-azure-policy).

U kunt een van de volgende criteria gebruiken: 

- ID zoeken 
- Categorie
- Beveiligings controle 
- CVSS v3-scores
- Ernst 
- Patchable status 

Een regel maken:

1. Selecteer op de detail pagina met aanbevelingen voor **beveiligings problemen in azure container Registry installatie kopieën moet worden hersteld**de optie **regel uitschakelen**.
1. Selecteer het relevante bereik.
1. Definieer uw criteria.
1. Selecteer **regel Toep assen**.

    :::image type="content" source="./media/defender-for-container-registries-usage/new-disable-rule-for-registry-finding.png" alt-text="Een regel voor het uitschakelen van VA-bevindingen in het REGI ster maken":::

1. Een regel weer geven, overschrijven of verwijderen: 
    1. Selecteer **regel uitschakelen**.
    1. In de lijst bereik worden abonnementen met actieve regels weer gegeven als **regel toegepast**.
        :::image type="content" source="./media/remediate-vulnerability-findings-vm/modify-rule.png" alt-text="Een bestaande regel wijzigen of verwijderen":::
    1. Als u de regel wilt weer geven of verwijderen, selecteert u het menu met weglatings tekens (...).


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over Azure Defender](azure-defender.md)