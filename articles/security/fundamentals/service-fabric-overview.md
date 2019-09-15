---
title: Overzicht van Azure Service Fabric Security | Microsoft Docs
description: Dit artikel bevat een overzicht van de beveiliging van Azure Service Fabric.
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2017
ms.author: tomsh
ms.openlocfilehash: 5329323122d8470b19055040b6f00b6db7c0e75f
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/15/2019
ms.locfileid: "70998949"
---
# <a name="azure-service-fabric-security-overview"></a>Overzicht van Azure Service Fabric-beveiliging
[Azure service Fabric](../../service-fabric/service-fabric-overview.md) is een platform voor gedistribueerde systemen waarmee schaal bare en betrouw bare micro services eenvoudig kunnen worden verpakt, geïmplementeerd en beheerd. Service Fabric verhelpt de uitdagingen bij het ontwikkelen en beheren van Cloud toepassingen. Ontwikkel aars en beheerders kunnen complexe infrastructuur problemen voor komen en zich richten op het implementeren van essentiële, veeleisende workloads die schaalbaar en betrouwbaar zijn.

Dit artikel bevat een overzicht van beveiligings overwegingen voor een Service Fabric-implementatie.

## <a name="secure-your-cluster"></a>Een cluster beveiligen
Azure Service Fabric coördineert Services in een cluster van machines. Clusters moeten worden beveiligd om te voor komen dat onbevoegde gebruikers er verbinding mee maken, vooral wanneer ze productie-workloads uitvoeren. Hoewel het mogelijk is om een niet-beveiligd cluster te maken, is het mogelijk dat anonieme gebruikers verbinding kunnen maken met het cluster (als er beheer eindpunten aan het open bare internet beschikbaar zijn).

Voor clusters die zelfstandig of op Azure worden uitgevoerd, moet u rekening houden met de beveiliging van knoop punt-naar-knoop punt en client-naar-knoop punt. U kunt verschillende technologieën gebruiken om deze scenario's te implementeren.

### <a name="node-to-node-security"></a>Beveiliging van knoop punt naar knoop punt
Beveiliging tussen knoop punten is van toepassing op de communicatie tussen de Vm's of machines in een cluster. Met de beveiliging van knoop punt naar knoop punt kunnen alleen computers die zijn gemachtigd om lid te worden van het cluster deel nemen aan het hosten van toepassingen en services in het cluster.

Clusters die worden uitgevoerd op Azure of zelfstandige clusters die worden uitgevoerd op Windows kunnen [certificaat beveiliging](https://msdn.microsoft.com/library/ff649801.aspx) of Windows- [beveiliging](https://msdn.microsoft.com/library/ff649396.aspx) voor Windows Server-computers gebruiken.

#### <a name="node-to-node-certificate-security"></a>Certificaat beveiliging van knoop punt naar knoop punt

Service Fabric maakt gebruik van X. 509-server certificaten die u opgeeft wanneer u een cluster maakt. Zie [werken met certificaten](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/working-with-certificates)voor een snel overzicht van wat deze certificaten zijn en hoe u deze kunt verkrijgen of maken.

U configureert certificaat beveiliging wanneer u het cluster maakt met behulp van de Azure Portal, Azure Resource Manager sjablonen of een zelfstandige JSON-sjabloon. U kunt een primair certificaat en een optioneel secundair certificaat opgeven dat wordt gebruikt voor certificaat overschakelingen. De primaire en secundaire certificaten die u opgeeft, moeten afwijken van de admin-client en alleen-lezen client certificaten die u opgeeft voor [client-naar-knoop punt beveiliging](../../service-fabric/service-fabric-cluster-security.md).

### <a name="client-to-node-security"></a>Beveiliging van client naar knoop punt
U kunt de beveiliging van client naar knoop punt configureren met behulp van client identiteiten. Om een vertrouwens relatie tussen een client en een cluster tot stand te brengen, moet u het cluster configureren om te weten welke client identiteiten het kan vertrouwen.

Service Fabric ondersteunt twee typen toegangs beheer voor clients die zijn verbonden met een Service Fabric cluster:

-   **Beheerder**: Volledige toegang tot beheer mogelijkheden, waaronder lees-en schrijf mogelijkheden.
-   **Gebruiker**: Alleen lees toegang tot beheer mogelijkheden (bijvoorbeeld query mogelijkheden) en de mogelijkheid om toepassingen en services op te lossen.

Met behulp van toegangs beheer kunnen cluster beheerders de toegang tot bepaalde typen cluster bewerkingen beperken. Dit maakt het cluster beter beveiligd.

#### <a name="client-to-node-certificate-security"></a>Client-naar-knoop punt certificaat beveiliging

U configureert certificaat beveiliging van client naar knoop punt wanneer u een cluster maakt via de Azure Portal, Resource Manager-sjablonen of een zelfstandige JSON-sjabloon. U moet een client certificaat voor de beheerder en/of een gebruikers certificaat voor de gebruiker opgeven. Zorg ervoor dat deze certificaten verschillen van de primaire en secundaire certificaten die u opgeeft voor beveiliging tussen knoop punten.

Clients die verbinding maken met het cluster met behulp van het beheerders certificaat, hebben volledige toegang tot beheer mogelijkheden. Clients die verbinding maken met het cluster met behulp van het client certificaat alleen-lezen gebruiker hebben alleen lees toegang tot beheer mogelijkheden. Met andere woorden, deze certificaten worden gebruikt voor op rollen gebaseerd toegangs beheer (RBAC).

Zie [een cluster instellen met behulp van een Azure Resource Manager sjabloon](../../service-fabric/service-fabric-cluster-creation-via-arm.md)voor meer informatie over het configureren van certificaat beveiliging in een cluster.

#### <a name="client-to-node-azure-active-directory-security"></a>Beveiliging van client naar knoop punt Azure Active Directory

Clusters die worden uitgevoerd op Azure, kunnen ook de toegang tot de beheer eindpunten beveiligen met behulp van Azure Active Directory (Azure AD). Zie [een cluster instellen met behulp van een Azure Resource Manager sjabloon](../../service-fabric/service-fabric-cluster-creation-via-arm.md)voor informatie over het maken van de benodigde Azure Active Directory artefacten, hoe u deze kunt vullen tijdens het maken van het cluster en hoe u verbinding maakt met deze clusters.

Azure Active Directory maakt het beheren van toegang tot toepassingen door organisaties (bekend als tenants) mogelijk. Er zijn toepassingen met een webgebaseerde aanmeldings GEBRUIKERSINTERFACE en toepassingen met een eigen client ervaring.

Een Service Fabric-cluster biedt verschillende toegangs punten voor de beheer functionaliteit, met inbegrip van de op het web gebaseerde Service Fabric Explorer en Visual Studio. Als gevolg hiervan, maakt u twee Azure Active Directory-toepassingen voor het beheren van toegang tot het cluster: een webtoepassing en een systeemeigen toepassing.

Voor Azure-clusters wordt u aangeraden Azure AD-beveiliging te gebruiken voor het verifiëren van clients en certificaten voor beveiliging tussen knoop punten.

Voor zelfstandige Windows Server-clusters met Windows Server 2012 R2 en Active Directory raden wij u aan Windows-beveiliging te gebruiken met door een groep beheerde service accounts (Gmsa's). Gebruik anders Windows-beveiliging met Windows-accounts.

## <a name="understand-monitoring-and-diagnostics-in-service-fabric"></a>Bewaking en diagnostische gegevens in Service Fabric begrijpen
[Bewaking en diagnose](../../service-fabric/service-fabric-diagnostics-overview.md) zijn essentieel voor het ontwikkelen, testen en implementeren van toepassingen en services in elke omgeving. Service Fabric oplossingen werken het beste wanneer u bewaking en diagnostische gegevens implementeert om ervoor te zorgen dat toepassingen en services werken zoals verwacht in een lokale ontwikkel omgeving of productie.

Vanuit het oogpunt van beveiliging zijn de belangrijkste doel stellingen van bewaking en diagnose:

-   Hardware-en infrastructuur problemen detecteren en vaststellen die mogelijk worden veroorzaakt door een beveiligings gebeurtenis.
-   Detecteer software-en app-problemen die een indicatie van inbreuk kunnen zijn (IoC).
-   Informatie over het gebruik van bronnen om onbedoelde denial of service te voor komen.

De werk stroom van controle en diagnostische gegevens bestaat uit drie stappen:

1.  **Genereren van gebeurtenissen**: Het genereren van gebeurtenissen omvat gebeurtenissen (logboeken, traceringen, aangepaste gebeurtenissen) op het niveau van de infra structuur (cluster) en het toepassings/service niveau. Lees meer over [gebeurtenissen op infrastructuur niveau](../../service-fabric/service-fabric-diagnostics-event-generation-infra.md) en [gebeurtenissen op toepassings niveau](../../service-fabric/service-fabric-diagnostics-event-generation-app.md) om te begrijpen wat er wordt weer gegeven en hoe u verdere instrumentatie kunt toevoegen.

2.  **Aggregatie van gebeurtenissen**: Gegenereerde gebeurtenissen moeten worden verzameld en geaggregeerd voordat ze kunnen worden weer gegeven. U wordt aangeraden [Azure Diagnostics](../../service-fabric/service-fabric-diagnostics-event-aggregation-wad.md) te gebruiken (vergelijkbaar met logboek verzameling op basis van een agent) of [Event flow](../../service-fabric/service-fabric-diagnostics-event-aggregation-eventflow.md) (logboek verzameling in het proces).

3.  **Analyse**: Gebeurtenissen moeten in een bepaalde indeling worden gevisualiseerd en toegankelijk om analyse en weer gave mogelijk te maken. Er zijn verschillende platformen voor de analyse en visualisatie van gegevens over bewaking en diagnostiek. U wordt aangeraden [Azure monitor logboeken](../../service-fabric/service-fabric-diagnostics-event-analysis-oms.md) en [Azure-toepassing inzichten](../../service-fabric/service-fabric-diagnostics-event-analysis-appinsights.md) uit te voeren, omdat ze goed zijn geïntegreerd met Service Fabric.

U kunt [Azure monitor](../../azure-monitor/overview.md) ook gebruiken om veel van de Azure-resources waarop een service Fabric-cluster is gebouwd, te bewaken.

Een watchdog is een afzonderlijke service die de status kan bekijken en laden tussen services, en de status rapporteren voor alles in de status model hiërarchie. Het gebruik van een watchdog kan helpen voor komen dat fouten die niet worden gedetecteerd op basis van de weer gave van één service. 

Watchdog zijn ook een goede plaats voor het hosten van code voor het uitvoeren van herstel acties zonder tussen komst van de gebruiker. Een voor beeld is het opschonen van logboek bestanden in opslag met bepaalde tijds intervallen. U vindt een voor beeld van een watchdog-service-implementatie in [Azure service Fabric watchdog](https://azure.microsoft.com/resources/samples/service-fabric-watchdog-service/)-voor beeld.

## <a name="secure-communication-by-using-certificates"></a>Communicatie beveiligen met behulp van certificaten
Met certificaten kunt u de communicatie tussen de verschillende knoop punten van uw zelfstandige Windows-cluster beveiligen. Door X. 509-certificaten te gebruiken, kunt u ook clients verifiëren die verbinding maken met dit cluster. Zo zorgt u ervoor dat alleen geautoriseerde gebruikers toegang hebben tot het cluster. U wordt aangeraden een certificaat op het cluster in te scha kelen wanneer u het maakt.

X. 509 digitale certificaten worden vaak gebruikt voor het verifiëren van clients en servers. Ze worden ook gebruikt voor het versleutelen en digitaal ondertekenen van berichten.

De volgende tabel geeft een lijst van de certificaten die u nodig hebt voor de installatie van het cluster:

|Instelling certificaat gegevens |Description|
|-------------------------------|-----------|
|ClusterCertificate|    Dit certificaat is vereist voor het beveiligen van de communicatie tussen de knoop punten in een cluster. U kunt twee cluster certificaten gebruiken: een primair certificaat en een secundaire voor upgrade.|
|ServerCertificate| Dit certificaat wordt weer gegeven aan de client wanneer deze probeert verbinding te maken met dit cluster. U kunt twee server certificaten gebruiken: een primair certificaat en een secundaire voor upgrade.|
|ClientCertificateThumbprints|  Dit is een set certificaten die op de geverifieerde clients moet worden geïnstalleerd.|
|ClientCertificateCommonNames|  Dit is de algemene naam van het eerste client certificaat voor CertificateCommonName. CertificateIssuerThumbprint is de vinger afdruk voor de verlener van dit certificaat.|
|ReverseProxyCertificate|   Dit is een optioneel certificaat dat u kunt opgeven om uw [omgekeerde proxy](../../service-fabric/service-fabric-reverseproxy.md)te beveiligen.|

Zie voor meer informatie over het beveiligen van certificaten, [een zelfstandige cluster in Windows beveiligen met behulp van X. 509-certificaten](../../service-fabric/service-fabric-windows-cluster-x509-security.md).

## <a name="understand-role-based-access-control"></a>Meer informatie over op rollen gebaseerd toegangs beheer
U geeft de gebruikers rollen beheerder en gebruiker op het moment van maken van het cluster op door afzonderlijke identiteiten (inclusief certificaten) op te geven. Zie [op rollen gebaseerd toegangs beheer voor service Fabric-clients](../../service-fabric/service-fabric-cluster-security-roles.md)voor meer informatie over de standaard instellingen voor toegangs beheer en het wijzigen van de standaard instellingen.

## <a name="secure-standalone-clusters-by-using-windows-security"></a>Zelfstandige clusters beveiligen met behulp van Windows-beveiliging
Als u ongeoorloofde toegang tot een Service Fabric cluster wilt voor komen, moet u het cluster beveiligen. Beveiliging is vooral belang rijk wanneer het cluster productie werkbelastingen uitvoert. U kunt de beveiliging van knoop punt-naar-knoop punt en client-naar-knoop punt configureren met behulp van Windows-beveiliging in het bestand ClusterConfig. JSON.

Als Service Fabric moet worden uitgevoerd onder een gMSA, configureert u de beveiliging van knoop punt naar knoop punt door [ClustergMSAIdentity](../../service-fabric/service-fabric-windows-cluster-windows-security.md)in te stellen. Als u vertrouwens relaties tussen knoop punten wilt maken, moet u ze op de hoogte stellen van elkaar.

Als u een computer groep binnen een Active Directory domein wilt gebruiken, configureert u de beveiliging van knoop punt naar knoop punt door ClusterIdentity in te stellen. Zie [een machine groep maken in Active Directory](https://msdn.microsoft.com/library/aa545347)voor meer informatie.

U kunt de beveiliging van client naar knoop punt configureren met behulp van ClientIdentities. U moet het cluster configureren om te herkennen welke client identiteiten het kan vertrouwen. U kunt vertrouwen op twee manieren instellen:

-   Geef de gebruikers van de domein groep op die verbinding kunnen maken.
-   Geef de gebruikers van het domein knooppunt op waarmee verbinding kan worden gemaakt.

## <a name="configure-application-security-in-service-fabric"></a>Toepassings beveiliging configureren in Service Fabric
### <a name="manage-secrets-in-service-fabric-applications"></a>Geheimen in Service Fabric toepassingen beheren
Geheimen kunnen gevoelige informatie zijn, zoals verbindings reeksen voor opslag, wacht woorden of andere waarden die niet in tekst zonder opmaak moeten worden verwerkt.

U kunt [Azure Key Vault](../../key-vault/key-vault-overview.md) gebruiken om sleutels en geheimen te beheren. Het gebruik van geheimen in een toepassing is echter niet afhankelijk van een specifiek Cloud platform. U kunt toepassingen implementeren in een cluster dat overal wordt gehost. Er zijn vier belang rijke stappen in deze stroom:

1.  Een certificaat voor gegevens versleuteling ophalen.
2.  Installeer het certificaat op uw cluster.
3.  Versleutel de geheime waarden bij het implementeren van een toepassing met het certificaat en Injecteer deze in het configuratie bestand instellingen. XML van een service.
4.  Lees versleutelde waarden uit Settings. XML door ze te ontsleutelen met hetzelfde coderings certificaat.

Zie [geheimen beheren in service Fabric-toepassingen](../../service-fabric/service-fabric-application-secret-management.md)voor meer informatie.

### <a name="configure-security-policies-for-an-application"></a>Beveiligings beleid voor een toepassing configureren
Met behulp van Azure Service Fabric beveiliging kunt u toepassingen die in het cluster worden uitgevoerd, beveiligen onder verschillende gebruikers accounts. Met Service Fabric beveiliging kunt u ook de bronnen beveiligen die toepassingen gebruiken op het moment van de implementatie onder de gebruikers accounts, bijvoorbeeld bestanden, directory's en certificaten. Dit maakt het uitvoeren van toepassingen, zelfs in een gedeelde gehoste omgeving, beter beveiligd.

Taken voor het configureren van beveiligings beleid zijn onder andere:

-   Het beleid voor een toegangs punt voor een service-instelling configureren
-   Power shell-opdrachten starten vanaf een invoer punt voor Setup
-   Console-omleiding gebruiken voor lokale fout opsporing
-   Een beleid configureren voor service code pakketten
-   Een beleid voor beveiligings toegang toewijzen voor HTTP-en HTTPS-eind punten

## <a name="secure-communication-for-services"></a>Beveiligde communicatie voor services
Beveiliging is een van de belangrijkste aspecten van de communicatie. Het Reliable Services-toepassings raamwerk bevat enkele vooraf ontwikkelde communicatie stacks en hulpprogram ma's die u kunt gebruiken om de beveiliging te verbeteren. Zie voor meer informatie [Secure service externe communicatie voor een service](../../service-fabric/service-fabric-reliable-services-secure-communication.md).

## <a name="next-steps"></a>Volgende stappen
- Zie [een service Fabric cluster maken met behulp van Azure Resource Manager](../../service-fabric/service-fabric-cluster-creation-via-arm.md) en [een service Fabric cluster maken met behulp van de Azure Portal](../../service-fabric/service-fabric-cluster-creation-via-portal.md)voor conceptuele informatie over de beveiliging van een cluster.
- Zie [service Fabric cluster beveiligings scenario's](../../service-fabric/service-fabric-cluster-security.md)voor meer informatie over de beveiliging van een cluster in service Fabric.
