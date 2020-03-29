---
title: Bewaken van de beveiliging van uw containers in Azure Security Center
description: Meer informatie over het controleren van de beveiligingshouding van uw containers vanuit Azure Security Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: memildin
ms.openlocfilehash: 330cbc3f28f5e549d5a21417c3d7ccc1e5444769
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919529"
---
# <a name="monitoring-the-security-of-your-containers"></a>Bewaken van de veiligheid van uw containers

Op deze pagina wordt uitgelegd hoe u de containerbeveiligingsfuncties gebruikt die zijn beschreven in het [artikel ContainerBeveiliging](container-security.md) in onze sectie Concepten.

Azure Security Center behandelt de volgende drie aspecten van containerbeveiliging:

- **Kwetsbaarheidsbeheer** - Als u zich op de standaardprijslaag van het Beveiligingscentrum bevindt (zie [prijzen),](/azure/security-center/security-center-pricing)u uw ARM-gebaseerde Azure Container Registry scannen telkens wanneer een nieuwe afbeelding wordt gepusht. De scanner (aangedreven door Qualys) presenteert bevindingen als Security Center aanbevelingen.
    Zie Hieronder voor gedetailleerde instructies [uw containerregisters scannen op kwetsbaarheden.](#scanning-your-arm-based-container-registries-for-vulnerabilities)

- **Het verharden van de Docker-hosts van uw containers** - Security Center vindt onbeheerde containers die worden gehost op IaaS Linux VM's of andere Linux-machines met Docker en vergelijkt continu de configuraties van de containers met de Docker Benchmark (Center for Internet Security). Security Center waarschuwt u als uw containers niet voldoen aan een van de controles. Continue bewaking van beveiligingsrisico's als gevolg van verkeerde configuraties is een cruciaal onderdeel van elk beveiligingsprogramma. 
    Zie Hieronder de [Dockerhosts van uw containers hardening.](#hardening-your-containers-docker-hosts)

- **Verharding van uw Azure Kubernetes Service-clusters** - Security Center geeft aanbevelingen wanneer er kwetsbaarheden worden gevonden in de configuratie van uw Azure Kubernetes Service-clusters. Zie de [aanbevelingen](recommendations-reference.md#recs-containers)van Kubernetes Service voor meer informatie over de specifieke aanbevelingen die kunnen worden weergegeven.

- **Runtime-beveiliging** - Als u op de standaardprijslaag van security center zit, krijgt u realtime bescherming tegen bedreigingen voor uw gecontaineriseerde omgevingen. Security Center genereert waarschuwingen voor verdachte activiteiten op host- en AKS-clusterniveau. Zie de [clusters waarschuwingen voor Azure Kubernetes Service](alerts-reference.md#alerts-akscluster) en Waarschuwingen voor containers - secties op [hostniveau](alerts-reference.md#alerts-containerhost) van de referentietabel voor waarschuwingen voor meer informatie over de relevante beveiligingswaarschuwingen die kunnen worden weergegeven.

## <a name="scanning-your-arm-based-container-registries-for-vulnerabilities"></a>Uw ARM-gebaseerde containerregisters scannen op kwetsbaarheden 

1. Ga als het gaat om het inschakelen van kwetsbaarheidsscans van uw Azure Container Registry-afbeeldingen:

    1. Zorg ervoor dat u zich op de standaardprijscategorie van Azure Security Center bevindt.

    1. Schakel op de pagina **Prijs& instellingen** de optionele containerregistersbundel in voor uw abonnement: ![De containerregistersbundel inschakelen](media/monitor-container-security/enabling-container-registries-bundle.png)

        Security Center is nu klaar om afbeeldingen te scannen die naar het register worden gepusht. 

        >[!NOTE]
        >Deze functie wordt per afbeelding in rekening gebracht.


1. Als u de scan van een afbeelding wilt activeren, duwt u deze naar uw register. 

    Wanneer de scan is voltooid (meestal na ongeveer 10 minuten), zijn bevindingen beschikbaar in aanbevelingen van het Beveiligingscentrum.
    

1. Ga naar de pagina **Aanbevelingen** om de bevindingen te bekijken. Als er problemen zijn gevonden, ziet u de volgende aanbeveling:

    ![Aanbeveling om problemen te verwerkeren ](media/monitor-container-security/acr-finding.png)


1. Selecteer de aanbeveling. 
    De pagina met aanbevelingsdetails wordt geopend met aanvullende informatie. Deze informatie omvat de lijst met registers met kwetsbare afbeeldingen ('Getroffen bronnen') en de herstelstappen. 

1. Selecteer een specifiek register om de repositories te zien die kwetsbare repositories hebben.

    ![Een register selecteren](media/monitor-container-security/acr-finding-select-registry.png)

    De pagina met registerdetails wordt geopend met de lijst met getroffen opslagplaatsen.

1. Selecteer een specifieke opslagplaats om de repositories te zien die kwetsbare afbeeldingen hebben.

    ![Een opslagplaats selecteren](media/monitor-container-security/acr-finding-select-repository.png)

    De pagina met bewaarplaatsen wordt geopend. Het geeft een overzicht van de kwetsbare beelden samen met een beoordeling van de ernst van de bevindingen.

1. Selecteer een specifieke afbeelding om de kwetsbaarheden te zien.

    ![Afbeeldingen selecteren](media/monitor-container-security/acr-finding-select-image.png)

    De lijst met bevindingen voor de geselecteerde afbeelding wordt geopend.

    ![Lijst van bevindingen](media/monitor-container-security/acr-findings.png)

1. Selecteer de bevinding voor meer informatie over een bevinding. 

    Het deelvenster bevindingen details wordt geopend.

    [![Deelvenster Bevindingen](media/monitor-container-security/acr-finding-details-pane.png)](media/monitor-container-security/acr-finding-details-pane.png#lightbox)

    Dit venster bevat een gedetailleerde beschrijving van het probleem en koppelingen naar externe bronnen om de bedreigingen te beperken.

1. Volg de stappen in het herstelgedeelte van dit deelvenster.

1. Wanneer u de stappen hebt genomen die nodig zijn om het beveiligingsprobleem te verhelpen, vervangt u de afbeelding in uw register:

    1. Druk op de bijgewerkte afbeelding. Dit zal leiden tot een scan. 
    
    1. Controleer de aanbevelingenpagina voor de aanbeveling "Kwetsbaarheden in Azure Container Registry-afbeeldingen moeten worden verholpen". 
    
        Als de aanbeveling nog steeds wordt weergegeven en de afbeelding die u hebt verwerkt nog steeds wordt weergegeven in de lijst met kwetsbare afbeeldingen, controleert u de herstelstappen opnieuw.

    1. Wanneer u zeker weet dat de bijgewerkte afbeelding is gepusht, gescand en niet meer in de aanbeveling wordt weergegeven, verwijdert u de 'oude' kwetsbare afbeelding uit uw register.


## <a name="hardening-your-containers-docker-hosts"></a>Het verharden van de Docker-hosts van uw containers

Security Center controleert voortdurend de configuratie van uw Docker-hosts en genereert beveiligingsaanbevelingen die overeenkomen met de industriestandaarden.

Ga als volgt te werk om de beveiligingsaanbevelingen van Azure Security Center voor de Docker-hosts van uw containers weer te geven:

1. Open op de navigatiebalk van het Beveiligingscentrum **&-apps** en selecteer het tabblad **Containers.**

1. Filter eventueel de lijst met containerbronnen op containerhosts.

    ![Containerresources-filter](media/monitor-container-security/container-resources-filter.png)

1. Selecteer er een in de lijst met uw containerhostmachines om verder te onderzoeken.

    ![Aanbevelingen voor containerhost](media/monitor-container-security/container-resources-filtered-to-hosts.png)

    De **pagina containerhostinformatie** wordt geopend met details van de host en een lijst met aanbevelingen.

1. Selecteer in de lijst met aanbevelingen een aanbeveling om verder te onderzoeken.

    ![Aanbevelingslijst containerhost](media/monitor-container-security/container-host-rec.png)

1. Lees eventueel de beschrijving, informatie, bedreigingen en herstelstappen. 

1. Selecteer **Actie uitvoeren** onder aan de pagina.

    [![Knop Actie uitvoeren](media/monitor-container-security/host-security-take-action-button.png)](media/monitor-container-security/host-security-take-action.png#lightbox)

    Log Analytics wordt geopend met een aangepaste bewerking die klaar is om uit te voeren. De standaardaangepaste query bevat een lijst met alle mislukte regels die zijn beoordeeld, samen met richtlijnen om u te helpen de problemen op te lossen.

    [![Log Analytics-actie](media/monitor-container-security/log-analytics-for-action-small.png)](media/monitor-container-security/log-analytics-for-action.png#lightbox)

1. Pas de queryparameters aan en selecteer **Uitvoeren** wanneer u zeker weet dat deze klaar is voor uw host. 



## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u de containerbeveiligingsfuncties van Security Center gebruiken. 

Zie voor ander gerelateerd materiaal de volgende pagina's: 

- [Aanbevelingen voor beveiligingscentrum voor containers](recommendations-reference.md#recs-containers)
- [Waarschuwingen voor AKS-clusterniveau](alerts-reference.md#alerts-akscluster)
- [Waarschuwingen voor containerhostniveau](alerts-reference.md#alerts-containerhost)
