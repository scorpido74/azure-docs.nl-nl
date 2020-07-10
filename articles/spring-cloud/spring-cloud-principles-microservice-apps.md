---
title: Java-en basis besturingssysteem voor Azure lente Cloud micro service-apps
description: Principes voor het onderhouden van een gezonde Java-en base-besturings systeem voor Azure lente Cloud micro service-apps
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 05/27/2020
ms.openlocfilehash: 1a8f48ff365ffae4a7013d2bfd537721ba2bc381
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86172588"
---
# <a name="java-and-base-os-for-spring-microservice-apps"></a>Java-en basis besturingssysteem voor lente-micro service-apps
Hieronder vindt u de principes voor het onderhouden van een gezonde Java-en basis besturingssysteem voor lente-micro service-apps.
## <a name="principles-for-healthy-java-and-base-os"></a>Principes voor een gezonde Java-en basis besturingssysteem
* Is hetzelfde basis besturingssysteem voor alle lagen-basis | Standaard | Ultieme.
    * Momenteel gebruiken apps in azure lente-Cloud een combi natie van Debian 10 en Ubuntu 18,04.
    * VMware build service maakt gebruik van Ubuntu 18,04.
* Moeten hetzelfde basis besturingssysteem zijn, ongeacht de begin punten van de implementatie-bron | JAR
    * Momenteel gebruiken apps in azure lente-Cloud een combi natie van Debian 10 en Ubuntu 18,04.
* Het basis besturingssysteem is vrij van beveiligings problemen.
    * Debian 10 base Operating System heeft 147 open CVEs.
    * Ubuntu 18,04-basis besturings systeem heeft 132 open CVEs.
* Moet JRE-headless gebruiken.
    * Momenteel gebruiken apps in azure lente Cloud JDK. JRE-headless is een kleinere afbeelding.
* Maakt gebruik van de meest recente versies van Java.
    * Momenteel gebruiken apps in azure lente-Cloud Java 8 build 242. Dit is een verouderde build.
 
Azul-systemen scannen voortdurend op wijzigingen in de basis besturingssystemen en houden de laatste gebouwde installatie kopieën up-to-date. Azure lente-Cloud zoekt naar wijzigingen in installatie kopieën en werkt deze voortdurend bij in implementaties.
 
## <a name="faq-for-azure-spring-cloud"></a>Veelgestelde vragen over Azure Spring Cloud

* Welke versies van Java worden ondersteund? Primaire versie en buildnummer.
    * Ondersteuning voor LTS-versies-Java 8 en 11.
    * Maakt gebruik van de meest recente build: u kunt nu bijvoorbeeld het volgende doen: Java 8 build 252 en Java 11 build 7.
* Wie heeft deze Java-Runtimes gemaakt?
    * Azul-systemen.
* Wat is het basis besturingssysteem voor installatie kopieën?
    * Ubuntu 20,04 LTS (brand Fossa). Apps blijven blijven werken met de meest recente LTS-versie van Ubuntu.
    * Zie [Ubuntu 20,04 LTS (brandpuntsafstand Fossa)](http://releases.ubuntu.com/focal/)
* Hoe kan ik een ondersteunde Java-runtime voor lokale ontwikkel aars downloaden? 
    * Zie [de JDK voor Azure en Azure stack installeren](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-install)
* Hoe kan ik ondersteuning krijgen voor problemen met het Java-runtime niveau?
    * Open een ondersteunings ticket met ondersteuning voor Azure.
 
## <a name="default-deployment-on-azure-spring-cloud"></a>Standaard implementatie in azure lente-Cloud

> ![Standaard implementatie](media/spring-cloud-principles/spring-cloud-default-deployment.png)
 
## <a name="next-steps"></a>Volgende stappen
* [Snelstartgids: een bestaande Azure lente-Cloud toepassing starten met behulp van de Azure Portal](spring-cloud-quickstart-launch-app-portal.md)
* [Java-ondersteuning op lange termijn voor Azure en Azure Stack](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-long-term-support)
