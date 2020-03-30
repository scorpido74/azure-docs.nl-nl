---
title: (AFGESCHAFT) Containerbeveiliging in Azure Container Service
description: Overwegingen voor het beveiligen van Docker-containers die zijn geïmplementeerd in Azure Container Service en gerelateerde Azure-services.
author: sauryadas
ms.service: container-service
ms.topic: conceptual
ms.date: 03/28/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: d90d872175febf775e7d0892e133883f1a8ff8a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75552378"
---
# <a name="deprecated-securing-docker-containers-in-azure-container-service"></a>(AFGESCHAFT) Docker-containers beveiligen in Azure Container Service

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Dit artikel bevat overwegingen en aanbevelingen voor het beveiligen van Docker-containers die zijn geïmplementeerd in Azure Container Service. Veel van deze overwegingen zijn algemeen van toepassing op Docker-containers die zijn geïmplementeerd in Azure of andere omgevingen. 

## <a name="image-security"></a>Beveiliging van installatiekopieën

Containers worden gemaakt van installatiekopieën die zijn opgeslagen in een of meer opslagplaatsen. Deze opslagplaatsen kunnen deel uitmaken van openbare of persoonlijke containerregisters. Een voorbeeld van een openbaar register is [Docker Hub](https://hub.docker.com/). Een voorbeeld van een persoonlijk register is de [Docker Trusted Registry](https://docs.docker.com/datacenter/dtr/2.0/), dat lokaal kan worden geïnstalleerd of in een virtuele privécloud. Er zijn ook cloudgebaseerde persoonlijke containerregisterservices, waaronder [Azure Container Registry](../../container-registry/container-registry-intro.md).

### <a name="public-and-private-images"></a>Openbare en persoonlijke installatiekopieën
In het algemeen kan voor een openbaar beschikbare containerinstallatiekopie geen veiligheid worden gegarandeerd, net als bij elk openbaar gepubliceerd softwarepakket. Containerinstallatiekopieën bestaan uit meerdere softwarelagen en elke softwarelaag kan beveiligingsproblemen bevatten. Het is cruciaal om de oorsprong van de containerinstallatiekopie te kennen, inclusief de eigenaar van de installatiekopie (om te bepalen of het een betrouwbare bron is), de softwarelagen waaruit deze bestaat en de softwareversies. 

Als u bijvoorbeeld naar de officiële [nginx-opslagplaats](https://hub.docker.com/_/nginx/) in Docker Hub gaat en naar het tabblad **Tags** navigeert, ziet u beveiligingsproblemen in elke installatiekopie met kleurcodering. Elke kleur geeft de kwetsbaarheid aan van een softwarelaag in de installatiekopie. Zie [Understanding official repos on Docker Hub](https://blog.docker.com/2015/06/understanding-official-repos-docker-hub/) (Officiële opslagplaatsen in Docker Hub begrijpen) voor meer informatie over het scannen van beveiligingsproblemen in Docker Hub.

![Nginx-afbeeldingen in Docker Hub](./media/container-service-security/docker-hub-nginx.png)

Beveiliging is erg belangrijk voor ondernemingen. Om zich te beschermen tegen beveiligingsaanvallen, zouden ze installatiekopieën van een persoonlijk register moeten opslaan en ophalen, zoals Azure Container Registry of Docker Trusted Registry. Azure Container Registry biedt niet alleen een beheerd persoonlijk register, maar ondersteunt ook [verificatie gebaseerd op service-principals](../../container-registry/container-registry-authentication.md) via Azure Active Directory voor basisverificatiestromen, met inbegrip van op rollen gebaseerde toegang voor alleen-lezen-, schrijven- en eigenaarsmachtigingen.

### <a name="image-security-scanning"></a>Installatiekopieën scannen op veiligheid

Zelfs wanneer u een persoonlijk register gebruikt, is het een goed idee om scanoplossingen voor installatiekopieën te gebruiken voor extra beveiligingsvalidatie. Elke softwarelaag in een containerinstallatiekopie is mogelijk kwetsbaar voor beveiligingsproblemen, afzonderlijk van de andere lagen in de containerinstallatiekopie. Steeds meer bedrijven implementeren hun productieworkloads op basis van containertechnologieën. Daardoor wordt het scannen van installatiekopieën steeds belangrijker om veiligheidsbedreigingen voor organisaties te voorkomen. 

Oplossingen voor beveiligingsbewaking en scannen zoals [Twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry) en [Aqua Security](https://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry) kunnen worden gebruikt om containerinstallatiekopieën in een persoonlijk register te scannen en mogelijke beveiligingsproblemen te identificeren. Het is belangrijk om te weten welk scanniveau de verschillende oplossingen bieden. Bepaalde oplossingen controleren de lagen van de installatiekopie bijvoorbeeld alleen maar op bekende beveiligingsproblemen. Deze oplossingen kunnen mogelijk niet software voor installatiekopielagen controleren die via bepaalde pakketbeheersoftware is gemaakt. Andere oplossingen hebben diepere scanintegratie en kunnen beveiligingsproblemen in alle verpakte software vinden.

### <a name="production-deployment-rules-and-audit"></a>Regels voor productie-implementatie en controle
Zodra een toepassing naar productie is geïmplementeerd, is het essentieel om enkele regels in te stellen om ervoor te zorgen dat de installatiekopieën die worden gebruikt in productieomgevingen beveiligd zijn en geen beveiligingsproblemen bevatten.

* Installatiekopieën met beveiligingsproblemen, zelfs kleine, mogen niet in een productieomgeving worden uitgevoerd. Daarnaast moeten alle installatiekopieën die naar productie zijn geïmplementeerd, idealiter in een persoonlijk register worden opgeslagen dat maar voor een paar mensen toegankelijk is. Het is ook belangrijk om het aantal productie-installatiekopieën beperkt te houden, zodat ze effectief kunnen worden beheerd.

* Omdat de oorsprong van software uit een openbaar beschikbare containerinstallatiekopie moeilijk te achterhalen is, is het verstandig om installatiekopieën vanaf de bron op te bouwen voor een goede kennis van de oorsprong van een laag. Wanneer er een beveiligingsprobleem aan het licht komt in een zelf gebouwde containerinstallatiekopie, kunnen klanten sneller een oplossing vinden. Bij een openbare installatiekopie moeten klanten de hoofdmap van de installatiekopie vinden om deze te repareren of een andere veilige installatiekopie verkrijgen van de uitgever.

* Een grondig gescande installatiekopie die naar productie is geïmplementeerd, blijft niet gegarandeerd de hele levensduur van de toepassing up-to-date. Er kunnen beveiligingsproblemen worden gerapporteerd voor lagen van de installatiekopie die niet bekend waren of die na de implementatie naar productie zijn geïntroduceerd. Periodieke controle van installatiekopieën die naar productie zijn geïmplementeerd is nodig om installatiekopieën te identificeren die verouderd zijn of lang niet zijn bijgewerkt. Blauwgroene implementatiemethoden en rolling upgrade-mechanismen kunnen worden gebruikt om containerinstallatiekopieën bij te werken zonder uitvaltijd. Scannen van installatiekopieën kan worden gedaan met hulpprogramma's die in het vorige gedeelte zijn beschreven. 

* Een CI-pijplijn (continue integratie) voor het bouwen van installatiekopieën en geïntegreerde beveiligingsscans kan helpen persoonlijke registers met containerinstallatiekopieën veilig te houden. De ingebouwde scanfunctie voor beveiligingsproblemen van de CI-oplossing zorgt ervoor dat installatiekopieën die voor alle tests slagen, naar het persoonlijke register worden gepusht van waaruit productieworkloads worden geïmplementeerd. Een fout in een CI-pijplijn zorgt ervoor dat kwetsbare installatiekopieën niet naar het persoonlijke register worden gepusht dat wordt gebruikt voor workloadimplementaties voor productie. Met de pijplijn kunt u ook beveiligingsscans voor de installatiekopieën automatisch laten uitvoeren bij een groot aantal installatiekopieën. Het handmatig controleren van installatiekopieën op beveiligingsproblemen kan erg langdurig en foutgevoelig zijn.

## <a name="host-level-container-isolation"></a>Isolatie van containers op hostniveau
Wanneer een klant containertoepassingen op Azure-resources implementeert, worden ze op een abonnementsniveau in resourcegroepen geïmplementeerd en hebben ze niet meerdere tenants. Dit betekent dat als een klant een abonnement met anderen deelt, er geen grenzen kunnen worden opgesteld tussen twee implementaties in hetzelfde abonnement. Beveiliging op containerniveau kan daarom niet worden gegarandeerd. 

Het is ook essentieel om te begrijpen dat containers de kernel en de resources van de host delen (in Azure Container Service is de host een Azure-VM in een cluster). Containers die worden uitgevoerd in de productieomgeving moeten daarom worden uitgevoerd in de modus voor niet-gemachtigde gebruikers. Wanneer een container met bevoegdheden op hoofdniveau wordt uitgevoerd, loopt de gehele omgeving risico. Een hacker kan met toegang op hoofdniveau in een container toegang krijgen tot de volledige bevoegdheden op hoofdniveau van de host. Daarnaast is het belangrijk om containers met alleen-lezenbestandssystemen uit te voeren. Dit voorkomt dat iemand die toegang heeft tot de aangetaste container, schadelijke scripts kan schrijven naar het bestandssysteem en toegang tot andere bestanden kan krijgen. Daarnaast is het belangrijk om het aantal resources (zoals geheugen, CPU en netwerkbandbreedte) dat aan een container is toegewezen, te beperken. Dit helpt voorkomen dat hackers van hogging middelen en het nastreven van illegale activiteiten, zoals creditcardfraude of bit coin mijnbouw, die kunnen voorkomen dat andere containers draaien op de host of cluster.

## <a name="orchestrator-considerations"></a>Orchestratoroverwegingen

Elke orchestrator die beschikbaar is in Azure Container Service heeft zijn eigen beveiligingsoverwegingen. U kunt bijvoorbeeld het beste directe SSH-toegang tot orchestratorknooppunten in de Container Service beperken. U kunt beter de gebruikersinterface of de opdrachtregelprogramma's voor elke orchestrator gebruiken (zoals `kubectl` voor Kubernetes) voor het beheren van de containeromgeving zonder de hosts te openen.

Zie de volgende bronnen voor extre orchestrator-specifieke informatie over beveiliging:

* **Kubernetes**: [aanbevolen beveiligingsprocedures voor Kubernetes-implementatie](https://kubernetes.io/blog/2016/08/security-best-practices-kubernetes-deployment/)

* **DC/OS**: [uw Cluster beveiligen](https://docs.mesosphere.com/1.12/administering-clusters/securing-your-cluster)

* **Docker Swarm**: [Docker-beveiliging](https://www.docker.com/docker-security)

## <a name="next-steps"></a>Volgende stappen

* Zie [Introduction to Container Security](https://www.docker.com/sites/default/files/WP_IntrotoContainerSecurity_08.19.2016.pdf) (Inleiding tot containerbeveiliging) voor meer informatie over Docker-architectuur en containerbeveiliging.

* Ga naar het [Azure Security Center](https://www.microsoft.com/trustcenter/cloudservices/azure) voor informatie over de beveiliging van het Azure-platform.
