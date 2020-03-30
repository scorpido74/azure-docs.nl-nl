---
title: Uw eerste app implementeren in Cloud Foundry in Microsoft Azure
description: Een toepassing implementeren voor Cloud Foundry op Azure
author: seanmck
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 06/14/2017
ms.author: seanmck
ms.openlocfilehash: 793a8f291be4fcca6fad19d486849253dddc089f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294792"
---
# <a name="deploy-your-first-app-to-cloud-foundry-on-microsoft-azure"></a>Uw eerste app implementeren in Cloud Foundry in Microsoft Azure

[Cloud Foundry](https://cloudfoundry.org) is een populair open-source applicatieplatform dat beschikbaar is op Microsoft Azure. In dit artikel laten we zien hoe u een toepassing op Cloud Foundry implementeert en beheert in een Azure-omgeving.

## <a name="create-a-cloud-foundry-environment"></a>Een Cloud Foundry-omgeving maken

Er zijn verschillende opties voor het maken van een Cloud Foundry-omgeving op Azure:

- Gebruik de [Pivotal Cloud Foundry-aanbieding][pcf-azuremarketplace] in azure marketplace om een standaardomgeving te maken die PCF Ops Manager en de Azure Service Broker bevat. U vindt [de volledige instructies][pcf-azuremarketplace-pivotaldocs] voor het implementeren van het marketplace-aanbod in de Pivotal-documentatie.
- Creëer een aangepaste omgeving door [Pivotal Cloud Foundry handmatig te implementeren.][pcf-custom]
- [Implementeer de open-source Cloud Foundry-pakketten rechtstreeks][oss-cf-bosh] door een [BOSH-directeur](https://bosh.io) in te stellen, een VM die de implementatie van de Cloud Foundry-omgeving coördineert.

> [!IMPORTANT] 
> Als u PCF implementeert vanuit de Azure Marketplace, noteer dan de SYSTEMDOMAINURL en de beheerdersreferenties die nodig zijn om toegang te krijgen tot de Pivotal Apps Manager, die beide worden beschreven in de implementatiehandleiding voor marketplace. Ze zijn nodig om deze tutorial te voltooien. Voor marktplaatsimplementaties bevindt de SYSTEMDOMAINURL zich in de vorm `https://system.*ip-address*.cf.pcfazure.com`.

## <a name="connect-to-the-cloud-controller"></a>Verbinding maken met de cloudcontroller

De Cloud Controller is het primaire toegangspunt tot een Cloud Foundry-omgeving voor het implementeren en beheren van applicaties. De core Cloud Controller API (CCAPI) is een REST API, maar is toegankelijk via verschillende tools. In dit geval communiceren we ermee via de [Cloud Foundry CLI.][cf-cli] U de CLI installeren op Linux, macOS of Windows, maar als u deze liever helemaal niet installeert, is deze vooraf geïnstalleerd in de [Azure Cloud Shell.][cloudshell-docs]

Als u wilt `api` inloggen, u zich voorbereiden op de URL van systemdomainurl die u hebt verkregen van de implementatie van de marktplaats. Aangezien de standaardimplementatie een zelfondertekend certificaat gebruikt, `skip-ssl-validation` moet u ook de switch opnemen.

```bash
cf login -a https://api.SYSTEMDOMAINURL --skip-ssl-validation
```

U wordt gevraagd in te loggen op de cloudcontroller. Gebruik de beheerdersaccountreferenties die u hebt verkregen uit de implementatiestappen van marketplaces.

Cloud Foundry biedt *orgs* en *spaties* als naamruimten om de teams en omgevingen binnen een gedeelde implementatie te isoleren. De pcf-marktplaatsimplementatie omvat de *standaardsysteemorganisatie* en een set spaties die zijn gemaakt om de basiscomponenten te bevatten, zoals de autoscaling-service en de Azure-servicebroker. Kies voornu de *systeemruimte.*


## <a name="create-an-org-and-space"></a>Een organisatie en ruimte maken

Als u `cf apps`typt, ziet u een set systeemtoepassingen die zijn geïmplementeerd in de systeemruimte binnen de systeemorganisatie. 

U moet het *systeem* org gereserveerd voor systeemtoepassingen, dus maak een organisatie en ruimte om onze voorbeeld applicatie huis.

```bash
cf create-org myorg
cf create-space dev -o myorg
```

Gebruik de doelopdracht om over te schakelen naar de nieuwe organisatie en ruimte:

```bash
cf target -o testorg -s dev
```

Nu, wanneer u een toepassing implementeert, wordt deze automatisch gemaakt in de nieuwe organisatie en ruimte. Om te bevestigen dat er momenteel geen apps `cf apps` in de nieuwe organisatie/ruimte zijn, typt u opnieuw.

> [!NOTE] 
> Zie de [documentatie cloudgieterij][cf-orgs-spaces-docs]voor meer informatie over orgs en ruimten en hoe deze kunnen worden gebruikt voor op rollen gebaseerde toegangscontrole (RBAC).

## <a name="deploy-an-application"></a>Een app implementeren

Laten we gebruik maken van een voorbeeld Cloud Foundry applicatie genaamd Hello Spring Cloud, die is geschreven in Java en gebaseerd op de [Spring Framework](https://spring.io) en [Spring Boot](https://projects.spring.io/spring-boot/).

### <a name="clone-the-hello-spring-cloud-repository"></a>Kloon de Hello Spring Cloud repository

De Hello Spring Cloud sample applicatie is beschikbaar op GitHub. Kloon het naar uw omgeving en verander in de nieuwe map:

```bash
git clone https://github.com/cloudfoundry-samples/hello-spring-cloud
cd hello-spring-cloud
```

### <a name="build-the-application"></a>De toepassing bouwen

Bouw de app met [Apache Maven](https://maven.apache.org).

```bash
mvn clean package
```

### <a name="deploy-the-application-with-cf-push"></a>De toepassing implementeren met cf push

U de meeste toepassingen `push` implementeren in Cloud Foundry met de opdracht:

```bash
cf push
```

Wanneer u een toepassing *pusht,* detecteert Cloud Foundry het type toepassing (in dit geval een Java-app) en identificeert het de afhankelijkheden ervan (in dit geval het Framework Voorjaar). Vervolgens wordt alles verpakt wat nodig is om uw code in een zelfstandige containerafbeelding te laten uitvoeren, een *druppel.* Ten slotte plant Cloud Foundry de toepassing op een van de beschikbare machines in uw omgeving en maakt u een URL waar u deze bereiken, die beschikbaar is in de uitvoer van de opdracht.

![Uitvoer van cf-pushopdracht][cf-push-output]

Als u de hello-spring-cloud-toepassing wilt bekijken, opent u de opgegeven URL in uw browser:

![Standaardgebruikersinterface voor Hello Spring Cloud][hello-spring-cloud-basic]

> [!NOTE] 
> Zie Hoe toepassingen worden `cf push`gefaseerd in de documentatie over Cloud Foundry voor meer informatie over wat er tijdens , wordt [geënsceneerd.][cf-push-docs]

## <a name="view-application-logs"></a>Toepassingslogboeken weergeven

U de CLI cloudfoundry gebruiken om logboeken voor een toepassing met de naam te bekijken:

```bash
cf logs hello-spring-cloud
```

Standaard gebruikt de opdracht Logboeken *staart*, die nieuwe logboeken toont terwijl ze zijn geschreven. Als u nieuwe logboeken wilt zien verschijnen, vernieuwt u de hello-spring-cloud-app in de browser.

Als u logboeken wilt weergeven `recent` die al zijn geschreven, voegt u de schakelaar toe:

```bash
cf logs --recent hello-spring-cloud
```

## <a name="scale-the-application"></a>De toepassing schalen

Standaard maakt `cf push` u slechts één exemplaar van uw toepassing. Om een hoge beschikbaarheid te garanderen en uitschalen mogelijk te maken voor een hogere doorvoer, wilt u over het algemeen meer dan één exemplaar van uw toepassingen uitvoeren. U met de `scale` opdracht eenvoudig reeds geïmplementeerde toepassingen uitschalen:

```bash
cf scale -i 2 hello-spring-cloud
```

Als `cf app` u de opdracht op de toepassing uitvoert, wordt aangeeft dat Cloud Foundry een andere instantie van de toepassing maakt. Zodra de toepassing is gestart, start Cloud Foundry automatisch met het balanceren van het verkeer.


## <a name="next-steps"></a>Volgende stappen

- [Lees de documentatie over Cloud Foundry][cloudfoundry-docs]
- [De plug-in Azure DevOps Services instellen voor Cloud Foundry][vsts-plugin]
- [Het Microsoft Log Analytics-nozzle configureren voor Cloud Foundry][loganalytics-nozzle]

<!-- LINKS -->

[pcf-azuremarketplace]: https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry
[pcf-custom]: https://docs.pivotal.io/pivotalcf/1-10/customizing/azure.html
[oss-cf-bosh]: https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs
[pcf-azuremarketplace-pivotaldocs]: https://docs.pivotal.io/pivotalcf/customizing/pcf_azure.html
[cf-cli]: https://github.com/cloudfoundry/cli
[cloudshell-docs]: https://docs.microsoft.com/azure/cloud-shell/overview
[cf-orgs-spaces-docs]: https://docs.cloudfoundry.org/concepts/roles.html
[spring-boot]: https://projects.spring.io/spring-boot/
[spring-framework]: https://spring.io
[cf-push-docs]: https://docs.cloudfoundry.org/concepts/how-applications-are-staged.html
[cloudfoundry-docs]: https://docs.cloudfoundry.org
[vsts-plugin]: https://github.com/Microsoft/vsts-cloudfoundry
[loganalytics-nozzle]: https://github.com/Azure/oms-log-analytics-firehose-nozzle

<!-- IMAGES -->
[cf-push-output]: ./media/cloudfoundry-deploy-your-first-app/cf-push-output.png
[hello-spring-cloud-basic]: ./media/cloudfoundry-deploy-your-first-app/hello-spring-cloud-basic.png
