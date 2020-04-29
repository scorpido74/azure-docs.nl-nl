---
title: De beveiliging van uw containers in Azure Security Center bewaken
description: Meer informatie over het controleren van de beveiligings postuur van uw containers van Azure Security Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: memildin
ms.openlocfilehash: 330cbc3f28f5e549d5a21417c3d7ccc1e5444769
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77919529"
---
# <a name="monitoring-the-security-of-your-containers"></a>De beveiliging van uw containers bewaken

Op deze pagina wordt uitgelegd hoe u de beveiligings functies van de container kunt gebruiken die worden beschreven in het [artikel Container Security](container-security.md) in het gedeelte concepten.

Azure Security Center heeft betrekking op de volgende drie aspecten van de container beveiliging:

- **Beveiligings beheer** : als u zich bevindt in de prijs categorie standard van Security Center (Zie [prijzen](/azure/security-center/security-center-pricing)), kunt u uw op arm gebaseerde Azure container Registry op elke keer scannen wanneer een nieuwe installatie kopie wordt gepusht. De scanner (aangedreven door Qualys) geeft conclusies als Security Center aanbevelingen.
    Zie voor gedetailleerde instructies [uw container registers scannen op beveiligings problemen](#scanning-your-arm-based-container-registries-for-vulnerabilities) hieronder.

- Als **u de docker-hosts van uw containers verhardt** -Security Center vindt u niet-beheerde containers die worden gehost op IaaS Linux vm's of andere Linux-machines met docker, en vergelijkt u de configuraties van containers met de Center for Internet Security (CIS) docker-referentie. Security Center u wordt gewaarschuwd als uw containers niet voldoen aan een van de besturings elementen. Voortdurende bewaking van beveiligings Risico's door onjuiste configuratie is een cruciaal onderdeel van elk beveiligings programma. 
    Zie voor gedetailleerde instructies [de verharding van de docker-hosts van uw containers](#hardening-your-containers-docker-hosts) hieronder.

- **De beveiliging van uw Azure Kubernetes Service-clusters** -Security Center biedt aanbevelingen wanneer er beveiligings problemen worden gevonden in de configuratie van uw Azure Kubernetes-Service clusters. Zie de aanbevelingen van de [Kubernetes-service](recommendations-reference.md#recs-containers)voor meer informatie over de specifieke aanbevelingen die kunnen worden weer gegeven.

- **Runtime-beveiliging** : als u zich in de Standard-prijs categorie van Security Center bevindt, ontvangt u real-time bedreigings beveiliging voor uw container omgevingen. Security Center genereert waarschuwingen voor verdachte activiteiten op het cluster niveau host en AKS. Zie de secties [waarschuwingen voor Azure Kubernetes Service-clusters](alerts-reference.md#alerts-akscluster) en [waarschuwingen voor containers-hostniveau](alerts-reference.md#alerts-containerhost) in de naslag tabel met waarschuwingen voor meer informatie over de relevante beveiligings waarschuwingen die kunnen worden weer gegeven.

## <a name="scanning-your-arm-based-container-registries-for-vulnerabilities"></a>Uw ARM-gebaseerde container registers scannen op beveiligings problemen 

1. Beveiligings scans van uw Azure Container Registry-installatie kopieën inschakelen:

    1. Zorg ervoor dat u zich in de prijs categorie Standard van Azure Security Center bevindt.

    1. Schakel op de pagina **prijs & instellingen** de optionele container registers bundel in voor uw abonnement: ![de bundel van de container registers inschakelen](media/monitor-container-security/enabling-container-registries-bundle.png)

        Security Center is nu gereed voor het scannen van afbeeldingen die naar het REGI ster worden gepusht. 

        >[!NOTE]
        >Deze functie wordt per afbeelding in rekening gebracht.


1. U kunt de scan van een afbeelding activeren door deze naar uw REGI ster te pushen. 

    Wanneer de scan is voltooid (doorgaans na ongeveer 10 minuten), zijn er conclusies beschikbaar in Security Center aanbevelingen.
    

1. Ga naar de pagina **aanbevelingen** om de resultaten weer te geven. Als er problemen zijn gevonden, wordt de volgende aanbeveling weer gegeven:

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


## <a name="hardening-your-containers-docker-hosts"></a>De docker-hosts van uw containers beveiligen

Security Center bewaakt voortdurend de configuratie van uw docker-hosts en genereert beveiligings aanbevelingen die de industrie normen weer spie gelen.

Beveiligings aanbevelingen van Azure Security Center voor de docker-hosts van uw containers weer geven:

1. Open in de Security Center navigatie balk **compute &-apps** en selecteer het tabblad **containers** .

1. U kunt desgewenst de lijst met container resources filteren op hosts hosts.

    ![Filter container resources](media/monitor-container-security/container-resources-filter.png)

1. Selecteer in de lijst van uw container-host-apparaten een om verder te onderzoeken.

    ![Aanbevelingen voor container host](media/monitor-container-security/container-resources-filtered-to-hosts.png)

    De **container hostgegevens pagina** wordt geopend met de details van de host en een lijst met aanbevelingen.

1. Selecteer in de lijst met aanbevelingen een aanbeveling om verder te onderzoeken.

    ![Lijst met aanbevelingen voor container host](media/monitor-container-security/container-host-rec.png)

1. Lees desgewenst de beschrijving, informatie, bedreigingen en herstel stappen. 

1. Selecteer **actie ondernemen** onder aan de pagina.

    [![Actie knop ondernemen](media/monitor-container-security/host-security-take-action-button.png)](media/monitor-container-security/host-security-take-action.png#lightbox)

    Log Analytics wordt geopend met een aangepaste bewerking die kan worden uitgevoerd. De aangepaste standaard query bevat een lijst met alle mislukte regels die zijn geëvalueerd, samen met richt lijnen voor het oplossen van de problemen.

    [![Log Analytics actie](media/monitor-container-security/log-analytics-for-action-small.png)](media/monitor-container-security/log-analytics-for-action.png#lightbox)

1. Verfijn de query parameters en selecteer **uitvoeren** wanneer u er zeker van bent dat deze klaar is voor uw host. 



## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u de beveiligings functies van Security Center-container kunt gebruiken. 

Zie de volgende pagina's voor meer gerelateerde materialen: 

- [Aanbevelingen voor Security Center voor containers](recommendations-reference.md#recs-containers)
- [Waarschuwingen voor het AKS-cluster niveau](alerts-reference.md#alerts-akscluster)
- [Waarschuwingen voor container niveau hostniveau](alerts-reference.md#alerts-containerhost)
