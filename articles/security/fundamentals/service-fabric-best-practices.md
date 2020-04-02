---
title: Aanbevolen procedures voor Azure Service Fabric-beveiliging
description: In dit artikel vindt u een reeks aanbevolen procedures voor Azure Service Fabric-beveiliging.
author: unifycloud
ms.author: tomsh
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.date: 01/16/2019
ms.openlocfilehash: befe8945468d220a04ec7f0b515f22159cb72b0f
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549231"
---
# <a name="azure-service-fabric-security-best-practices"></a>Best practices voor Azure Service Fabric-beveiliging
Het implementeren van een toepassing op Azure is snel, eenvoudig en kosteneffectief. Before you deploy your cloud application into production, review our list of essential and recommended best practices for implementing secure clusters in your application.

Azure Service Fabric is een gedistribueerde systemen platform waarmee u gemakkelijk pakket, implementeren en beheren van schaalbare en betrouwbare microservices. Service Fabric biedt ook een oplossing voor de grote uitdaging van het ontwikkelen en beheren van cloudtoepassingen. Ontwikkelaars en beheerders kunnen complexe infrastructuurproblemen voorkomen en zich concentreren op het implementeren van bedrijfsspecifieke, veeleisende werkbelastingen die schaalbaar, betrouwbaar en beheerbaar zijn.

Voor elke best practice leggen we uit:

-   Wat de beste praktijk is.
-   Waarom u de beste praktijken moet implementeren.
-   Wat kan er gebeuren als u de beste praktijken niet implementeert.
-   Hoe u leren om de beste praktijken te implementeren.

We raden de volgende aanbevolen procedures voor Azure Service Fabric-beveiliging aan:

-   Gebruik Azure Resource Manager-sjablonen en de PowerShell-module ServiceFabric om beveiligde clusters te maken.
-   X.509-certificaten gebruiken.
-   Beveiligingsbeleid configureren.
-   Implementeer de beveiligingsconfiguratie Betrouwbare Actoren.
-   TLS configureren voor Azure Service Fabric.
-   Gebruik netwerkisolatie en -beveiliging met Azure Service Fabric.
-   Configureer Azure Key Vault voor beveiliging.
-   Gebruikers toewijzen aan rollen.


## <a name="best-practices-for-securing-your-clusters"></a>Aanbevolen procedures voor het beveiligen van uw clusters

Gebruik altijd een beveiligd cluster:
-   Implementeer clusterbeveiliging met behulp van certificaten.
-   Clienttoegang (beheerder en alleen-lezen) bieden met Azure Active Directory (Azure AD).

Gebruik geautomatiseerde implementaties:
-   Gebruik scripts om de geheimen te genereren, implementeren en rollen.
-   Sla de geheimen op in Azure Key Vault en gebruik Azure AD voor alle andere clienttoegang.
-   Verificatie vereisen voor menselijke toegang tot de geheimen.

Houd bovendien rekening met de volgende configuratieopties:
-   Maak perimeternetwerken (ook wel gedemilitariseerde zones, DMZ's en afgeschermde subnetten genoemd) met behulp van Azure Network Security Groups (NSG's).
-   Toegang tot virtuele clustermachines (VM's) of beheer uw cluster met jumpservers met Extern bureaublad-verbinding.

Uw clusters moeten worden beveiligd om te voorkomen dat onbevoegde gebruikers verbinding maken, vooral wanneer een cluster in productie wordt uitgevoerd. Hoewel het mogelijk is om een onbeveiligd cluster te maken, kunnen anonieme gebruikers verbinding maken met uw cluster als het cluster beheereindpunten blootstelt aan het openbare internet.

Er zijn drie [scenario's](../../service-fabric/service-fabric-cluster-security.md) voor het implementeren van clusterbeveiliging met behulp van verschillende technologieën:

-   Beveiliging van knooppunten: met dit scenario wordt de communicatie tussen de VM's en de computers in het cluster beveiligd. Deze vorm van beveiliging zorgt ervoor dat alleen computers die gemachtigd zijn om deel te nemen aan het cluster toepassingen en services in het cluster kunnen hosten.
In dit scenario kunnen de clusters die op Azure worden uitgevoerd of zelfstandige clusters die op Windows worden uitgevoerd, certificaatbeveiliging of [Windows-beveiliging](../../service-fabric/service-fabric-windows-cluster-x509-security.md) voor Windows Server-machines gebruiken. [Windows security](../../service-fabric/service-fabric-windows-cluster-windows-security.md)
-   Client-to-node beveiliging: Dit scenario beveiligt de communicatie tussen een Service Fabric-client en de afzonderlijke knooppunten in het cluster.
-   RBAC (Role-Based Access Control): dit scenario maakt gebruik van afzonderlijke identiteiten (certificaten, Azure AD, enzovoort) voor elke beheerder en gebruikersclientrol die toegang heeft tot het cluster. U geeft de rolidentiteiten op wanneer u het cluster maakt.

>[!NOTE]
>**Beveiligingsaanbeveling voor Azure-clusters:** Gebruik Azure AD-beveiliging om clients en certificaten te verifiëren voor node-to-nodebeveiliging.

Zie Instellingen configureren voor [een zelfstandig Windows-cluster](../../service-fabric/service-fabric-cluster-manifest.md)als u een zelfstandig Windows-cluster wilt configureren.

Gebruik Azure Resource Manager-sjablonen en de PowerShell-module Servicefabric om een beveiligd cluster te maken.
Zie Een cluster van servicestructuur maken voor stapsgewijze instructies voor het maken van een beveiligd cluster voor servicestructuur met Azure Resource [Manager-sjablonen.](../../service-fabric/service-fabric-cluster-creation-via-arm.md)

Gebruik de sjabloon Azure Resource Manager:
-   Pas uw cluster aan met behulp van de sjabloon om beheerde opslag voor virtuele VM-harde schijven (VHD's) te configureren.
-   Stimuleer wijzigingen in uw resourcegroep door de sjabloon te gebruiken voor eenvoudig configuratiebeheer en controle.

Behandel uw clusterconfiguratie als code:
-   Wees grondig bij het controleren van uw implementatieconfiguraties.
-   Vermijd het gebruik van impliciete opdrachten om uw resources rechtstreeks te wijzigen.

Veel aspecten van de levenscyclus van de [Service Fabric-toepassing](../../service-fabric/service-fabric-application-lifecycle.md) kunnen worden geautomatiseerd. De [PowerShell-module servicefabric](../../service-fabric/service-fabric-deploy-remove-applications.md#upload-the-application-package) automatiseert veelvoorkomende taken voor het implementeren, upgraden, verwijderen en testen van Azure Service Fabric-toepassingen. Beheerde API's en HTTP-API's voor toepassingsbeheer zijn ook beschikbaar.

## <a name="use-x509-certificates"></a>X.509-certificaten gebruiken
Beveilig uw clusters altijd met X.509-certificaten of Windows-beveiliging. Beveiliging is alleen geconfigureerd op het moment van het maken van clusteren. Het is niet mogelijk om beveiliging in te schakelen nadat het cluster is gemaakt.

Als u een [clustercertificaat](../../service-fabric/service-fabric-windows-cluster-x509-security.md)wilt opgeven, stelt u de waarde van de eigenschap **ClusterCredentialType** in op X509. Als u een servercertificaat voor externe verbindingen wilt opgeven, stelt u de eigenschap **ServerCredentialType** in op X509.

Volg bovendien deze praktijken:
-   Maak de certificaten voor productieclusters met behulp van een correct geconfigureerde Windows Server-certificaatservice. U de certificaten ook verkrijgen bij een erkende certificaatinstantie (CA).
-   Gebruik nooit een tijdelijk of testcertificaat voor productieclusters als het certificaat is gemaakt met MakeCert.exe of een soortgelijk hulpmiddel.
-   Gebruik een zelfondertekend certificaat voor testclusters, maar niet voor productieclusters.

Als het cluster onveilig is, kan iedereen anoniem verbinding maken met het cluster en beheerbewerkingen uitvoeren. Beveilig daarom altijd productieclusters met X.509-certificaten of Windows-beveiliging.

Zie [Certificaten toevoegen of verwijderen voor een cluster servicefabric](../../service-fabric/service-fabric-cluster-security-update-certs-azure.md)voor meer informatie over het gebruik van X.509-certificaten .

## <a name="configure-security-policies"></a>Beveiligingsbeleid configureren
Service Fabric beveiligt ook de bronnen die worden gebruikt door toepassingen. Bronnen zoals bestanden, mappen en certificaten worden opgeslagen onder de gebruikersaccounts wanneer de toepassing wordt geïmplementeerd. Deze functie maakt het uitvoeren van toepassingen veiliger van elkaar, zelfs in een gedeelde gehoste omgeving.

-   Gebruik een Active Directory-domeingroep of -gebruiker: voer de service uit onder de referenties voor een Active Directory-gebruiker of groepsaccount. Zorg ervoor dat u Active Directory on-premises binnen uw domein gebruikt en niet in Azure Active Directory. Toegang tot andere bronnen in het domein die machtigingen hebben gekregen met behulp van een domeingebruiker of -groep. Bronnen zoals bestandsshares.

-   Een beveiligingstoegangsbeleid toewijzen voor HTTP- en HTTPS-eindpunten: Geef de eigenschap **SecurityAccessPolicy** op om een **RunAs-beleid** toe te passen op een service wanneer het servicemanifest eindpuntbronnen met HTTP declareert. Poorten die zijn toegewezen aan de HTTP-eindpunten zijn correct door de toegang beheerde lijsten voor het RunAs-gebruikersaccount waarop de service wordt uitgevoerd. Wanneer het beleid niet is ingesteld, heeft http.sys geen toegang tot de service en u fouten krijgen met oproepen van de client.

Zie [Beveiligingsbeleid configureren voor uw toepassing voor](../../service-fabric/service-fabric-application-runas-security.md)meer informatie over het gebruik van beveiligingsbeleid in een cluster van Servicefabric.

## <a name="implement-the-reliable-actors-security-configuration"></a>De beveiligingsconfiguratie van Betrouwbare Actoren implementeren
Service Fabric Reliable Actors is een implementatie van het actor design patroon. Zoals met elk software ontwerppatroon, de beslissing om een specifiek patroon te gebruiken is gebaseerd op de vraag of een software probleem past bij het patroon.

Gebruik in het algemeen het ontwerppatroon van actors om oplossingen te helpen modelleren voor de volgende softwareproblemen of beveiligingsscenario's:
-   Uw probleemruimte omvat een groot aantal (duizenden of meer) kleine, onafhankelijke en geïsoleerde eenheden van staat en logica.
-   U werkt met objecten met één thread die geen significante interactie van externe componenten vereisen, inclusief querystatus over een reeks actoren.
-   Uw actor-exemplaren blokkeren bellers met onvoorspelbare vertragingen niet door I/O-bewerkingen uit te geven.

In Service Fabric worden actoren geïmplementeerd in het toepassingskader betrouwbare actoren. Dit framework is gebaseerd op het actorpatroon en is gebouwd bovenop [Service Fabric Reliable Services.](../../service-fabric/service-fabric-reliable-services-introduction.md) Elke betrouwbare actor service die u schrijft is een verdeelde stateful betrouwbare service.

Elke actor wordt gedefinieerd als een instantie van een actortype, identiek aan de manier waarop een .NET-object een instantie van een .NET-type is. Een **actortype** dat de functionaliteit van een rekenmachine implementeert, kan bijvoorbeeld veel actoren van dat type hebben die over verschillende knooppunten over een cluster zijn verdeeld. Elk van de gedistribueerde acteurs wordt uniek gekenmerkt door een actor-id.

[Replicator-beveiligingsconfiguraties](../../service-fabric/service-fabric-reliable-actors-kvsactorstateprovider-configuration.md) worden gebruikt om het communicatiekanaal te beveiligen dat wordt gebruikt tijdens de replicatie. Deze configuratie voorkomt dat services elkaars replicatieverkeer kunnen zien en zorgt ervoor dat hoog beschikbare gegevens veilig zijn. Standaard voorkomt een lege beveiligingsconfiguratiesectie replicatiebeveiliging.
Replicatorconfiguraties configureren de replicator die verantwoordelijk is voor het maken van de status Actor State Provider zeer betrouwbaar.

## <a name="configure-tls-for-azure-service-fabric"></a>TLS configureren voor Azure Service Fabric
Het serververificatieproces [verifieert](../../service-fabric/service-fabric-cluster-creation-via-arm.md) de eindpunten voor clusterbeheer naar een beheerclient. De beheerclient erkent dan dat hij in gesprek is met het echte cluster. Dit certificaat biedt ook een [TLS](../../service-fabric/service-fabric-cluster-creation-via-arm.md) voor de HTTPS-beheer-API en voor Service Fabric Explorer via HTTPS.
U hebt voor uw cluster een aangepaste domeinnaam nodig. Wanneer u een certificaat aanvraagt bij een certificaatautoriteit, moet de onderwerpnaam van het certificaat overeenkomen met de aangepaste domeinnaam die u voor uw cluster gebruikt.

Als u TLS voor een toepassing wilt configureren, moet u eerst een SSL/TLS-certificaat aanvragen dat is ondertekend door een CA. De CA is een vertrouwde derde partij die certificaten uitgeeft voor TLS-beveiligingsdoeleinden. Als u nog geen SSL/TLS-certificaat hebt, moet u er een aanvragen bij een bedrijf dat SSL/TLS-certificaten verkoopt.

Het certificaat moet voldoen aan de volgende vereisten voor SSL/TLS-certificaten in Azure:
-   Het certificaat moet een privésleutel bevatten.

-   Het certificaat moet worden gemaakt voor sleuteluitwisseling en kan worden geëxporteerd naar een bestand voor het uitwisselen van persoonlijke gegevens (.pfx).

-   De onderwerpnaam van het certificaat moet overeenkomen met de domeinnaam die wordt gebruikt om toegang te krijgen tot uw cloudservice.

    - Een aangepaste domeinnaam aanschaffen om te gebruiken voor toegang tot uw cloudservice.
    - Vraag een certificaat aan van een CA met een onderwerpnaam die overeenkomt met de aangepaste domeinnaam van uw service. Als uw aangepaste domeinnaam bijvoorbeeld __contoso__**.com**is, moet het certificaat van uw CA de onderwerpnaam **.contoso.com** of __www__**.contoso.com**hebben.

    >[!NOTE]
    >U geen SSL/TLS-certificaat verkrijgen van een CA voor het __cloudapp__**.net-domein.**

-   Het certificaat moet minimaal 2.048-bits versleuteling gebruiken.

Het HTTP-protocol is onveilig en onderhevig aan afluisteraanvallen. Gegevens die via HTTP worden verzonden, worden als platte tekst van de webbrowser naar de webserver of tussen andere eindpunten verzonden. Aanvallers kunnen gevoelige gegevens die via HTTP worden verzonden, zoals creditcardgegevens en accountaanmeldingen onderscheppen en bekijken. Wanneer gegevens via HTTPS via een browser worden verzonden of verzonden, zorgt SSL ervoor dat gevoelige informatie wordt versleuteld en beveiligd tegen onderschepping.

Zie [TLS configureren voor een toepassing in Azure voor](../../cloud-services/cloud-services-configure-ssl-certificate-portal.md)meer informatie over het gebruik van SSL/TLS-certificaten.

## <a name="use-network-isolation-and-security-with-azure-service-fabric"></a>Netwerkisolatie en -beveiliging gebruiken met Azure Service Fabric
Stel een beveiligd cluster met 3 nodetype in met de [sjabloon Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) als voorbeeld. Beheer het inkomende en uitgaande netwerkverkeer met behulp van de sjabloon en netwerkbeveiligingsgroepen.

De sjabloon heeft een NSG voor elk van de virtuele machineschaalsets en wordt gebruikt om het verkeer in en uit de set te regelen. De regels zijn standaard geconfigureerd om al het verkeer mogelijk te maken dat nodig is voor de systeemservices en de toepassingspoorten die in de sjabloon zijn opgegeven. Bekijk deze regels en breng eventuele wijzigingen aan die aan uw behoeften voldoen, inclusief het toevoegen van nieuwe regels voor uw toepassingen.

Zie [Algemene netwerkscenario's voor Azure Service Fabric voor](../../service-fabric/service-fabric-patterns-networking.md)meer informatie.

## <a name="set-up-azure-key-vault-for-security"></a>Azure Key Vault instellen voor beveiliging
Service Fabric gebruikt certificaten om verificatie en versleuteling te bieden voor het beveiligen van een cluster en de bijbehorende toepassingen.

Service Fabric gebruikt X.509-certificaten om een cluster te beveiligen en beveiligingsfuncties voor toepassingen te bieden. U gebruikt Azure Key Vault om certificaten te [beheren](../../service-fabric/service-fabric-cluster-security-update-certs-azure.md) voor Service Fabric-clusters in Azure. De Azure-bronprovider die de clusters maakt, haalt de certificaten uit een sleutelkluis. De provider installeert vervolgens de certificaten op de VM's wanneer het cluster wordt geïmplementeerd in Azure.

Er bestaat een certificaatrelatie tussen [Azure Key Vault,](../../key-vault/key-vault-secure-your-key-vault.md)het cluster Servicefabric en de resourceprovider die de certificaten gebruikt. Wanneer het cluster wordt gemaakt, wordt informatie over de certificaatrelatie opgeslagen in een sleutelkluis.

Er zijn twee basisstappen om een sleutelkluis in te stellen:
1. Maak een resourcegroep speciaal voor uw sleutelkluis.

    We raden u aan de sleutelkluis in een eigen brongroep te plaatsen. Met deze actie u voorkomen dat uw sleutels en geheimen verloren gaan als andere brongroepen worden verwijderd, zoals opslag, rekenkracht of de groep die uw cluster bevat. De brongroep die uw sleutelkluis bevat, moet zich in dezelfde regio bevinden als het cluster dat het wordt gebruikt.

2. Maak een sleutelkluis in de nieuwe resourcegroep.

    De sleutelkluis moet zijn ingeschakeld voor implementatie. De compute resource provider kan vervolgens de certificaten uit de kluis halen en installeren op de VM-exemplaren.

Zie [Wat is Azure Key Vault?](../../key-vault/key-vault-overview.md)

## <a name="assign-users-to-roles"></a>Gebruikers toewijzen aan rollen
Nadat u de toepassingen hebt gemaakt om uw cluster weer te geven, wijst u uw gebruikers toe aan de rollen die worden ondersteund door Service Fabric: alleen-lezen en beheerder. U deze rollen toewijzen via de Azure-portal.

>[!NOTE]
> Zie [Role-Based Access Control voor Service Fabric-clients voor](../../service-fabric/service-fabric-cluster-security-roles.md)meer informatie over het gebruik van rollen in Service Fabric.

Azure Service Fabric ondersteunt twee typen toegangscontrole voor clients die zijn verbonden met een [Cluster van Servicefabric:](../../service-fabric/service-fabric-cluster-creation-via-arm.md)beheerder en gebruiker. De clusterbeheerder kan toegangsbeheer gebruiken om de toegang tot bepaalde clusterbewerkingen voor verschillende groepen gebruikers te beperken. Toegangscontrole maakt het cluster veiliger.

## <a name="next-steps"></a>Volgende stappen

- [Checklist voor servicefabricbeveiliging](service-fabric-checklist.md)
- Stel uw [servicefabric-ontwikkelomgeving in.](../../service-fabric/service-fabric-get-started.md)
- Meer informatie over [ondersteuningsopties voor Service Fabric](../../service-fabric/service-fabric-support.md).
