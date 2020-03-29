---
title: Hoe Cloud Foundry integreert met Azure | Microsoft Documenten
description: Beschrijft hoe Cloud Foundry Azure-services kan gebruiken om de Enterprise-ervaring te verbeteren
services: virtual-machines-linux
documentationcenter: ''
author: ningk
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2018
ms.author: ningk
ms.openlocfilehash: 04ef72f7ec70b370305395ae8de8180f4594b43b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277350"
---
# <a name="integrate-cloud-foundry-with-azure"></a>Cloud Foundry integreren met Azure

[Cloud Foundry](https://docs.cloudfoundry.org/) is een PaaS-platform dat bovenop het IaaS-platform van cloudproviders draait. Het biedt consistente ervaring met het implementeren van toepassingen in cloudproviders. Het kan ook worden geïntegreerd met verschillende Azure-services, met HA, schaalbaarheid en kostenbesparingen van bedrijfskwaliteit.
Er zijn [6 subsystemen van Cloud Foundry](https://docs.cloudfoundry.org/concepts/architecture/), die flexibel online kunnen worden geschaald, waaronder: Routing, Authentication, Application life-cycle management, Service management, Messaging en Monitoring. Voor elk van de subsystemen u Cloud Foundry configureren om correspondent Azure-service te gebruiken. 

![Cloud Foundry op Azure-integratiearchitectuur](media/CFOnAzureEcosystem-colored.png)

## <a name="1-high-availability-and-scalability"></a>1. Hoge beschikbaarheid en schaalbaarheid
### <a name="managed-disk"></a>Managed Disk
Bosh gebruikt Azure CPI (Cloud Provider Interface) voor schijfmaken en verwijderen van routines. Standaard worden onbeheerde schijven gebruikt. Het vereist dat de klant handmatig opslagaccounts maakt en vervolgens de accounts in CF-manifestbestanden configureert. Dit komt door de beperking van het aantal schijven per opslagaccount.
Managed [Disk](https://azure.microsoft.com/services/managed-disks/) is nu beschikbaar, biedt beheerde veilige en betrouwbare schijfopslag voor virtuele machines. Klant hoeft niet langer om te gaan met de opslag account voor schaal en HA. Azure rangschikt schijven automatisch. Of het nu gaat om een nieuwe of een bestaande implementatie, de Azure CPI verwerkt de creatie of migratie van de beheerde schijf tijdens een CF-implementatie. Het wordt ondersteund met PCF 1.11. U ook de open-source Cloud Foundry [Managed Disk-richtlijnen](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/managed-disks) voor referentie bekijken. 
### <a name="availability-zone-"></a>Beschikbaarheidszone *
Als cloud-native applicatieplatform is Cloud Foundry ontworpen met [vier niveaus van hoge beschikbaarheid.](https://docs.pivotal.io/pivotalcf/2-1/concepts/high-availability.html) Terwijl de eerste drie niveaus van softwarefouten door cf-systeem zelf kunnen worden afgehandeld, wordt platformfouttolerantie geleverd door cloudproviders. De belangrijkste CF-componenten moeten worden beschermd met de HA-oplossing van een cloudprovider. Dit omvat GoRouters, Diego Brains, CF database en service tegels. [Azure Availability Set](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/deploy-cloudfoundry-with-availability-sets) wordt standaard gebruikt voor fouttolerantie tussen clusters in een datacenter.
Het goede nieuwe is dat [Azure Availability Zone](https://docs.microsoft.com/azure/availability-zones/az-overview ) nu wordt vrijgegeven, waardoor de fouttolerantie naar een hoger niveau wordt getild, met redundantie met een lage latentie in datacenters.
Azure Availability Zone bereikt HA door een set VM's in 2+ datacenters te plaatsen, elke set VM's is redundant voor andere sets. Als één Streek neer is, zijn de andere reeksen nog levend, geïsoleerd van de ramp.
> [!NOTE] 
> Azure Availability Zone wordt nog niet aan alle regio's aangeboden, raadpleeg de laatste [aankondiging voor de lijst met ondersteunde regio's.](https://docs.microsoft.com/azure/availability-zones/az-overview) Voor Open Source Cloud Foundry controleert u [azure availability zone voor open source Cloud Foundry guidance](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/availability-zone).

## <a name="2-network-routing"></a>2. Netwerkroutering
Azure basic load balancer wordt standaard gebruikt voor inkomende CF API/apps-aanvragen en stuurt deze door naar de Gorouters. CF-componenten zoals Diego Brain, MySQL, ERT kunnen ook de load balancer gebruiken om het verkeer voor HA in evenwicht te brengen. Azure biedt ook een reeks volledig beheerde oplossingen voor het balanceren van taken. Als u op zoek bent naar TLS-beëindiging ('SSL offload') of per HTTP/HTTPS-aanvraagtoepassingslaagverwerking, u Application Gateway overwegen. Voor hoge beschikbaarheid en schaalbaarheid laadverdeling op laag 4, overweeg dan standaard load balancer.
### <a name="azure-application-gateway-"></a>Azure Application Gateway *
[Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) biedt verschillende functies voor het balanceren van laag 7, waaronder SSL-offloading, end-to-end SSL, Web Application Firewall, sessieaffiniteit op basis van cookies en meer. U [Application Gateway configureren in Open Source Cloud Foundry.](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/application-gateway) Voor PCF controleert u de [PCF 2.1-releasenotes](https://docs.pivotal.io/pivotalcf/2-1/pcf-release-notes/opsmanager-rn.html#azure-application-gateway) voor POC-tests.

### <a name="azure-standard-load-balancer-"></a>Azure Standard Load Balancer *
Azure Load Balancer is een Layer 4 load balancer. Het wordt gebruikt om het verkeer te verdelen over instanties van services in een load-balanced set. De standaardversie biedt [geavanceerde functies](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) bovenop de basisversie. Bijvoorbeeld 1. De backend pool max limiet wordt verhoogd van 100 naar 1000 VM's.  2. De eindpunten ondersteunen nu meerdere beschikbaarheidssets in plaats van enkele beschikbaarheidsset.  3. Extra functies zoals HA-poorten, rijkere monitoringgegevens, enzovoort. Als u naar Azure Availability Zone gaat, is standaard load balancer vereist. Voor een nieuwe implementatie raden we u aan om te beginnen met Azure Standard Load Balancer. 

## <a name="3-authentication"></a>3. Verificatie 
[Cloud Foundry User Account and Authentication](https://docs.cloudfoundry.org/concepts/architecture/uaa.html) is de centrale identity management service voor CF en de verschillende componenten. [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) is de multi-tenant, cloudgebaseerde directory- en identiteitsbeheerservice van Microsoft. UAA wordt standaard gebruikt voor Cloud Foundry-verificatie. Als geavanceerde optie ondersteunt UAA ook Azure AD als een externe gebruikerswinkel. Azure AD-gebruikers hebben toegang tot Cloud Foundry met hun LDAP-identiteit, zonder Cloud Foundry-account. Voer deze stappen uit om [de Azure AD voor UAA in PCF](https://docs.pivotal.io/p-identity/1-6/azure/index.html)te configureren.

## <a name="4-data-storage-for-cloud-foundry-runtime-system"></a>4. Gegevensopslag voor Cloud Foundry Runtime System
Cloud Foundry biedt een grote uitbreidbaarheid om Azure blobstore of Azure MySQL/PostgreSQL-services te gebruiken voor toepassingsruntimesysteemopslag.
### <a name="azure-blobstore-for-cloud-foundry-cloud-controller-blobstore"></a>Azure Blobstore voor Blobstore cloudfoundry cloudcontroller
De Blobstore voor cloudcontroller is een essentieel gegevensarchief voor buildpacks, druppels, pakketten en resourcepools. Nfs-server wordt standaard gebruikt voor de blobstore van cloudcontroller. Gebruik Azure Blob Storage als extern archief om één storingspunt te voorkomen. Bekijk de [Cloud Foundry-documentatie](https://docs.cloudfoundry.org/deploying/common/cc-blobstore-config.html) voor achtergronden en [opties in Pivotal Cloud Foundry.](https://docs.pivotal.io/pivotalcf/2-0/customizing/azure.html)

### <a name="mysqlpostgresql-as-cloud-foundry-elastic-run-time-database-"></a>MySQL/PostgreSQL als Cloud Foundry Elastic Run Time Database *
CF Elastic Runtime vereist twee belangrijke systeemdatabases:
#### <a name="ccdb"></a>CCDB (CCDB) 
De Cloud Controller-database.  Cloud Controller biedt REST API-eindpunten voor clients om toegang te krijgen tot het systeem. CCDB slaat tabellen op voor organisaties, ruimten, services, gebruikersrollen en meer voor Cloud controller.
#### <a name="uaadb"></a>UAADB UAADB 
De database voor gebruikersaccount en verificatie. Het slaat de gebruikersverificatie gerelateerde gegevens op, bijvoorbeeld versleutelde gebruikersnamen en wachtwoorden.

Standaard kan een lokale systeemdatabase (MySQL) worden gebruikt. Voor HA en op schaal u gebruikmaken van door Azure beheerde MySQL- of PostgreSQL-services. Hier is de instructie van het inschakelen van [Azure MySQL/PostgreSQL voor CCDB, UAADB en andere systeemdatabases met Open Source Cloud Foundry.](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/configure-cf-external-databases-using-azure-mysql-postgres-service)

## <a name="5-open-service-broker"></a>5. Open Service Broker
Azure-servicebroker biedt een consistente interface om de toegang van de toepassing tot Azure-services te beheren. Het nieuwe [Open Service Broker voor Azure-project](https://github.com/Azure/open-service-broker-azure) biedt een eenvoudige manier om services te leveren aan toepassingen in Cloud Foundry, OpenShift en Kubernetes. Zie de [tegel Azure Open Service Broker voor PCF](https://pivotal.io/platform/services-marketplace/data-management/microsoft-azure) voor implementatie-instructies op PCF.

## <a name="6-metrics-and-logging"></a>6. Statistieken en logboekregistratie
De Azure Log Analytics Nozzle is een Cloud Foundry-component, die statistieken van de [cloudfoundry-loggregator-firehose](https://docs.cloudfoundry.org/loggregator/architecture.html) doorstuurt naar [Azure Monitor-logboeken.](https://azure.microsoft.com/services/log-analytics/) Met de Nozzle u uw CF-systeemstatus- en prestatiestatistieken verzamelen, bekijken en analyseren voor meerdere implementaties.
Klik [hier](https://docs.microsoft.com/azure/cloudfoundry/cloudfoundry-oms-nozzle) voor meer informatie over het implementeren van de Azure Log Analytics Nozzle naar zowel de Open Source- als de Pivotal Cloud Foundry-omgeving en vervolgens toegang tot de gegevens van de Azure Monitor-logboekenconsole. 
> [!NOTE]
> Vanaf PCF 2.0 worden BOSH-statusstatistieken voor VM's standaard doorgestuurd naar de Loggregator Firehose en worden ze geïntegreerd in azure monitor-logboekenconsole.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="7-cost-saving"></a>7. Kostenbesparing
### <a name="cost-saving-for-devtest-environments"></a>Kostenbesparing voor Dev/Test-omgevingen
#### <a name="b-series-"></a>B-serie: *
Terwijl F en D VM-serie werden vaak aanbevolen voor Pivotal Cloud Foundry productie-omgeving, de nieuwe "burstable" [B-serie](https://azure.microsoft.com/blog/introducing-b-series-our-new-burstable-vm-size/) brengt nieuwe opties. De B-serie burstable VM's zijn ideaal voor workloads die niet continu de volledige prestaties van de CPU nodig hebben, zoals webservers, kleine databases en ontwikkel- en testomgevingen. Deze workloads hebben doorgaans burstable prestatievereisten. Het is $ 0,012 / uur (B1) in vergelijking met $ 0,05 / uur (F1), zie de volledige lijst van [VM-formaten](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general) en [prijzen](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) voor meer informatie. 
#### <a name="managed-standard-disk"></a>Beheerde standaardschijf: 
Premium schijven werden aanbevolen voor betrouwbare prestaties in de productie.  Met [Managed Disk](https://azure.microsoft.com/services/managed-disks/)kan standaardopslag ook vergelijkbare betrouwbaarheid bieden, met verschillende prestaties. Voor werkbelasting die niet prestatiegevoelig is, zoals dev/Test of niet-kritieke omgeving, bieden beheerde standaardschijven een alternatieve optie met lagere kosten.  
### <a name="cost-saving-in-general"></a>Kostenbesparing in het algemeen 
#### <a name="significant-vm-cost-saving-with-azure-reservations"></a>Aanzienlijke VM-kostenbesparing met Azure-reserveringen: 
Vandaag alle CF VM's worden gefactureerd met behulp van "on-demand" prijzen, hoewel de omgevingen meestal blijven voor onbepaalde tijd. Nu u vm-capaciteit reserveren op een termijn van 1 of 3 jaar en kortingen van 45-65% krijgen. Kortingen worden toegepast in het factureringssysteem, zonder wijzigingen in uw omgeving. Zie Hoe [Azure-reserveringen werken](https://azure.microsoft.com/pricing/reserved-vm-instances/)voor meer informatie . 
#### <a name="managed-premium-disk-with-smaller-sizes"></a>Beheerde premiumschijf met kleinere grootte: 
Beheerde schijven ondersteunen kleinere schijfformaten, bijvoorbeeld P4(32 GB) en P6(64 GB) voor zowel premium als standaard schijven. Als u kleine workloads hebt, u kosten besparen bij het migreren van standaard premium schijven naar beheerde premium schijven.
#### <a name="use-azure-first-party-services"></a>Azure First Party Services gebruiken: 
Als u gebruikmaakt van de first party-service van Azure, worden de beheerkosten op lange termijn verlaagd, naast HA en betrouwbaarheid die in bovenstaande secties worden genoemd. 

Pivotal heeft een [Small Footprint ERT](https://docs.pivotal.io/pivotalcf/2-0/customizing/small-footprint.html) gelanceerd voor PCF-klanten, de componenten zijn co-locatie in slechts 4 VM's, die tot 2500 toepassingsexemplaren uitvoeren. De proefversie is nu beschikbaar via [Azure Market Place](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).

## <a name="next-steps"></a>Volgende stappen
Azure-integratiefuncties zijn eerst beschikbaar met [Open Source Cloud Foundry,](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/)voordat deze beschikbaar is op Pivotal Cloud Foundry. Functies gemarkeerd met * zijn nog steeds niet beschikbaar via PCF. Cloud Foundry-integratie met Azure Stack wordt ook niet in dit document behandeld.
Neem voor PCF-ondersteuning voor de functies die zijn gemarkeerd met *- of Cloud Foundry-integratie met Azure Stack, contact op met uw Pivotal- en Microsoft-accountmanager voor de laatste status. 

