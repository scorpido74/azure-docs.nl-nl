---
title: Meer informatie over azure service fabric-toepassingsbeveiliging
description: Een overzicht van hoe u microservices-applicaties veilig uitvoeren op Service Fabric. Leer hoe u services en opstartscript uitvoert onder verschillende beveiligingsaccounts, gebruikers verifieert en autoriseert, toepassingsgeheimen beheert, servicecommunicatie beveiligt, een API-gateway gebruikt en in rust toepassingsgegevens beveiligt.
ms.topic: conceptual
ms.date: 03/16/2018
ms.openlocfilehash: c97c5345a1a18cce8c44508542f12d3642d2b8f9
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461426"
---
# <a name="service-fabric-application-and-service-security"></a>Service Fabric-toepassing en servicebeveiliging
Een microservices-architectuur kan [veel voordelen opleveren.](service-fabric-overview-microservices.md) Het beheer van de beveiliging van microservices is echter een uitdaging en anders dan het beheren van traditionele monolithische applicatiebeveiliging. 

Met een monoliet wordt de toepassing meestal uitgevoerd op een of meer servers binnen een netwerk en is het eenvoudiger om de blootgestelde poorten en API's en IP-adres te identificeren. Er is vaak een perimeter of grens en een database te beschermen. Als dat systeem is gecompromitteerd als gevolg van een inbreuk op de beveiliging of aanval, is het waarschijnlijk dat alles binnen het systeem beschikbaar zal zijn voor de aanvaller. Met microservices is het systeem complexer.  Services zijn gedecentraliseerd en gedistribueerd over veel hosts en migreren van host naar host.  Met de juiste beveiliging beperkt u de bevoegdheden die een aanvaller kan krijgen en de hoeveelheid gegevens die beschikbaar zijn in één aanval door één service te schenden.  Communicatie is niet intern, maar gebeurt via een netwerk, en er zijn veel blootgestelde poorten en interacties tussen services. Weten wat deze serviceinteracties zijn en wanneer ze plaatsvinden, is cruciaal voor de beveiliging van uw toepassing.

Dit artikel is geen gids voor de beveiliging van microservices, er zijn veel van dergelijke bronnen online beschikbaar, maar beschrijft hoe verschillende aspecten van beveiliging kunnen worden bereikt in Service Fabric.

## <a name="authentication-and-authorization"></a>Verificatie en autorisatie
Het is vaak noodzakelijk dat resources en API's die door een service worden blootgesteld, worden beperkt tot bepaalde vertrouwde gebruikers of clients. Verificatie is het proces om de identiteit van een gebruiker betrouwbaar vast te stellen.  Autorisatie is het proces dat API's of services beschikbaar maakt voor sommige geverifieerde gebruikers, maar niet voor anderen.

### <a name="authentication"></a>Verificatie
De eerste stap naar het maken van vertrouwensbeslissingen op API-niveau is verificatie. Verificatie is het proces om de identiteit van een gebruiker betrouwbaar vast te stellen.  In microservicescenario's wordt verificatie meestal centraal behandeld. Als u een API-gateway gebruikt, u verificatie naar de gateway [verwijderen.](/azure/architecture/patterns/gateway-offloading) Als u deze aanpak gebruikt, moet u ervoor zorgen dat de afzonderlijke services niet rechtstreeks kunnen worden bereikt (zonder de API-gateway), tenzij er extra beveiliging is om berichten te verifiëren, ongeacht of ze afkomstig zijn van de gateway of niet.

Als services rechtstreeks kunnen worden geopend, kan een verificatieservice zoals Azure Active Directory of een speciale verificatiemicroservice die fungeert als een beveiligingstokenservice (STS) worden gebruikt om gebruikers te verifiëren. Vertrouwensbeslissingen worden gedeeld tussen services met beveiligingstokens of cookies. 

Voor ASP.NET Core is het primaire mechanisme voor [het verifiëren van gebruikers](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/) het ASP.NET Core Identity-lidmaatschapssysteem. ASP.NET Core Identity slaat gebruikersgegevens op (inclusief aanmeldingsinformatie, rollen en claims) op in een gegevensarchief dat is geconfigureerd door de ontwikkelaar. ASP.NET Core Identity ondersteunt tweestapsverificatie.  Externe verificatieproviders worden ook ondersteund, zodat gebruikers zich kunnen aanmelden met bestaande verificatieprocessen van providers zoals Microsoft, Google, Facebook of Twitter.

### <a name="authorization"></a>Autorisatie
Na verificatie moeten services gebruikerstoegang autoriseren of bepalen wat een gebruiker kan doen. Met dit proces kan een service API's beschikbaar maken voor sommige geverifieerde gebruikers, maar niet voor iedereen. Autorisatie is orthogonale en onafhankelijk van authenticatie, dat is het proces om vast te stellen wie een gebruiker is. Verificatie kan een of meer identiteiten maken voor de huidige gebruiker.

[ASP.NET Core-autorisatie](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/authorization-net-microservices-web-applications) kan worden uitgevoerd op basis van de rollen van gebruikers of op basis van aangepaste beleidsregels, waaronder het inspecteren van claims of andere heuristieken.

## <a name="restrict-and-secure-access-using-an-api-gateway"></a>Toegang beperken en beveiligen met een API-gateway
Cloudtoepassingen hebben meestal een gateway in de front-end nodig om een centraal ingangspunt te bieden voor gebruikers, apparaten of andere toepassingen. Een [API-gateway](/azure/architecture/microservices/gateway) zit tussen clients en services en is het toegangspunt tot alle services die uw toepassing levert. Het fungeert als een omgekeerde proxy, routering verzoeken van clients naar diensten. Het kan ook verschillende cross-cutting taken uitvoeren, zoals authenticatie en autorisatie, TLS-beëindiging en tariefbeperking. Als u geen gateway implementeert, moeten clients aanvragen rechtstreeks naar front-endservices verzenden.

In Service Fabric kan een gateway elke stateloze service zijn, zoals een [ASP.NET Core-toepassing](service-fabric-reliable-services-communication-aspnetcore.md)of een andere service die is ontworpen voor verkeersinformatie, zoals [Traefik,](https://docs.traefik.io/) [Event Hubs,](https://docs.microsoft.com/azure/event-hubs/) [IoT Hub](https://docs.microsoft.com/azure/iot-hub/)of Azure API [Management.](https://docs.microsoft.com/azure/api-management)

API Management integreert rechtstreeks met Service Fabric, zodat u API's publiceren met een uitgebreide set routeringsregels naar uw back-end Service Fabric-services.  U de toegang tot backendservices beveiligen, DOS-aanvallen voorkomen door beperking te gebruiken of API-sleutels, JWT-tokens, certificaten en andere referenties verifiëren. Lees voor meer informatie [het overzicht Service Fabric with Azure API Management](service-fabric-api-management-overview.md).

## <a name="manage-application-secrets"></a>Toepassingsgeheimen beheren
Geheimen kunnen gevoelige informatie zijn, zoals tekenreeksen voor opslagverbindingen, wachtwoorden of andere waarden die niet in platte tekst mogen worden verwerkt. In dit artikel wordt Azure Key Vault gebruikt om sleutels en geheimen te beheren. Het *gebruik van* geheimen in een toepassing is echter cloudplatform-agnostisch om toepassingen te kunnen implementeren in een cluster dat overal wordt gehost.

De aanbevolen manier om serviceconfiguratie-instellingen te beheren is via [serviceconfiguratiepakketten.][config-package] Configuratiepakketten zijn versie en updatable door managed rolling upgrades met health-validatie en automatische rollback. Dit heeft de voorkeur boven globale configuratie omdat dit de kans op een wereldwijde servicestoring vermindert. Versleutelde geheimen zijn geen uitzondering. Service Fabric heeft ingebouwde functies voor het versleutelen en ontsleutelen van waarden in een configuratiepakket Settings.xml-bestand met certificaatversleuteling.

Het volgende diagram illustreert de basisstroom voor geheim beheer in een Service Fabric-toepassing:

![overzicht van geheim beheer][overview]

Er zijn vier belangrijke stappen in deze stroom:

1. Verkrijg een certificaat voor gegevensvercijfering.
2. Installeer het certificaat in uw cluster.
3. Versleutel geheime waarden bij het implementeren van een toepassing met het certificaat en injecteer ze in het configuratiebestand Settings.xml van een service.
4. Lees versleutelde waarden uit Settings.xml door te decoderen met hetzelfde certificaat voor vercijfering. 

[Azure Key Vault][key-vault-get-started] wordt hier gebruikt als een veilige opslaglocatie voor certificaten en als een manier om certificaten te installeren op Service Fabric-clusters in Azure. Als u niet implementeert naar Azure, hoeft u Key Vault niet te gebruiken om geheimen in Service Fabric-toepassingen te beheren.

Zie [Toepassingsgeheimen beheren](service-fabric-application-secret-management.md)voor een voorbeeld .

## <a name="secure-the-hosting-environment"></a>Beveilig de hostingomgeving
Door Azure Service Fabric te gebruiken, u toepassingen beveiligen die in het cluster worden uitgevoerd onder verschillende gebruikersaccounts. Service Fabric helpt ook de resources te beveiligen die worden gebruikt door toepassingen op het moment van implementatie onder de gebruikersaccounts, bijvoorbeeld bestanden, mappen en certificaten. Dit maakt het uitvoeren van toepassingen, zelfs in een gedeelde gehoste omgeving, veiliger van elkaar.

In het toepassingsmanifest wordt de vereiste beveiligingsprincipals (gebruikers en groepen) de service(s) uitgevoerd en resources beveiligd.  Deze beveiligingsprincipals worden verwezen in beleidsregels, bijvoorbeeld het run-as-, endpoint-binding, pakketdeling of beveiligingstoegangsbeleid.  Beleidsregels worden vervolgens toegepast op serviceresources in de sectie **ServiceManifestImport** van het toepassingsmanifest.

Wanneer u principals declareert, u ook gebruikersgroepen definiëren en maken, zodat een of meer gebruikers aan elke groep kunnen worden toegevoegd om samen te worden beheerd. Dit is handig wanneer er meerdere gebruikers zijn voor verschillende serviceinvoerpunten en ze bepaalde gemeenschappelijke bevoegdheden moeten hebben die beschikbaar zijn op groepsniveau.

Standaard worden Service Fabric-toepassingen uitgevoerd onder het account waarop het Fabric.exe-proces wordt uitgevoerd. Service Fabric biedt ook de mogelijkheid om toepassingen uit te voeren onder een lokaal gebruikersaccount of lokaal systeemaccount, dat is opgegeven in het toepassingsmanifest. Zie [Een service uitvoeren als een lokaal gebruikersaccount of lokaal systeemaccount voor](service-fabric-application-runas-security.md)meer informatie.  U [ook een script voor het opstarten van een service uitvoeren als een lokale gebruiker of systeemaccount.](service-fabric-run-script-at-service-startup.md)

Wanneer u Service Fabric uitvoert op een windows-zelfstandige cluster, u een service uitvoeren onder [Active Directory-domeinaccounts](service-fabric-run-service-as-ad-user-or-group.md) of [door groepen beheerde serviceaccounts](service-fabric-run-service-as-gmsa.md).

## <a name="secure-containers"></a>Beveiligde containers
Service Fabric biedt een mechanisme voor services in een container om toegang te krijgen tot een certificaat dat is geïnstalleerd op de knooppunten in een Windows- of Linux-cluster (versie 5.7 of hoger). Dit PFX-certificaat kan worden gebruikt voor het verifiëren van de toepassing of service of veilige communicatie met andere diensten. Zie [Een certificaat importeren in een container](service-fabric-securing-containers.md)voor meer informatie.

Daarnaast ondersteunt Service Fabric ook gMSA (group Managed Service Accounts) voor Windows-containers. Zie [GMSA instellen voor Windows-containers voor](service-fabric-setup-gmsa-for-windows-containers.md)meer informatie.

## <a name="secure-service-communication"></a>Beveiligde servicecommunicatie
In Service Fabric wordt een service ergens in een Service Fabric-cluster uitgevoerd, meestal verdeeld over meerdere VM's. Service Fabric biedt verschillende opties voor het beveiligen van uw servicecommunicatie.

U HTTPS-eindpunten inschakelen in uw [ASP.NET Core- of](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service) Java-webservices.

U een veilige verbinding tot stand brengen tussen de omgekeerde proxy en services, waardoor een end-to-end secure kanaal mogelijk is. Verbinding maken met beveiligde services wordt alleen ondersteund wanneer reverse proxy is geconfigureerd om te luisteren op HTTPS. Lees Reverse proxy in Azure [Service Fabric](service-fabric-reverseproxy.md)voor informatie over het configureren van de omgekeerde proxy.  [Verbinding maken met een beveiligde service](service-fabric-reverseproxy-configure-secure-communication.md) beschrijft hoe u een veilige verbinding maken tussen de omgekeerde proxy en services.

Het application framework Reliable Services biedt een aantal vooraf gebouwde communicatiestacks en -tools die u gebruiken om de beveiliging te verbeteren. Meer informatie over het verbeteren van de beveiliging wanneer u serviceremoting (in [C#](service-fabric-reliable-services-secure-communication.md) of [Java)](service-fabric-reliable-services-secure-communication-java.md)gebruikt of [WCF](service-fabric-reliable-services-secure-communication-wcf.md)gebruikt.

## <a name="encrypt-application-data-at-rest"></a>Toepassingsgegevens in rust versleutelen
Elk [knooppunttype](service-fabric-cluster-nodetypes.md) in een Cluster Servicefabric dat in Azure wordt uitgevoerd, wordt ondersteund door een [virtuele machineschaalset.](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) U kunt met behulp van een Azure Resource Manager-sjabloon gegevensschijven koppelen aan de schaalsets die gezamenlijk het Service Fabric-cluster vormen.  Als uw services gegevens opslaan op een gekoppelde gegevensschijf, u [deze gegevensschijven versleutelen](../virtual-machine-scale-sets/virtual-machine-scale-sets-encrypt-disks-ps.md) om uw toepassingsgegevens te beschermen.

<!--TO DO: Enable BitLocker on Windows standalone clusters?
TO DO: Encrypt disks on Linux clusters?-->


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Volgende stappen
* [Een installatiescript uitvoeren bij het opstarten van de service](service-fabric-run-script-at-service-startup.md)
* [Resources opgeven in een servicemanifest](service-fabric-service-manifest-resources.md)
* [Een app implementeren](service-fabric-deploy-remove-applications.md)
* [Meer informatie over clusterbeveiliging](service-fabric-cluster-security.md)

<!-- Links -->
[key-vault-get-started]:../key-vault/general/overview.md
[config-package]: service-fabric-application-and-service-manifests.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md

<!-- Images -->
[overview]:./media/service-fabric-application-and-service-security/overview.png