---
title: Uitgaand verkeer afsluiten
description: Meer informatie over hoe u integreren met Azure Firewall om uitgaand verkeer te beveiligen vanuit een App Service-omgeving.
author: ccompy
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.topic: article
ms.date: 03/31/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 3dadb57c6358623974de1a27e1601d99b28fee32
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80584319"
---
# <a name="locking-down-an-app-service-environment"></a>Een app-serviceomgeving vergrendelen

De App Service Environment (ASE) heeft een aantal externe afhankelijkheden waartoe het toegang nodig heeft om goed te kunnen functioneren. De ASE woont in de klant Azure Virtual Network (VNet). Klanten moeten toestaan dat de ASE afhankelijkheid verkeer, dat is een probleem voor klanten die willen vergrendelen alle uitgang en hun VNet.

Er zijn een aantal binnenkomende eindpunten die worden gebruikt om een ASE te beheren. Het binnenkomende beheerverkeer kan niet via een firewall-apparaat worden verzonden. De bronadressen voor dit verkeer zijn bekend en worden gepubliceerd in het document Voor het beheer van adressen van de [App Service Environment.](https://docs.microsoft.com/azure/app-service/environment/management-addresses) Er is ook een Service Tag genaamd AppServiceManagement die kan worden gebruikt met Network Security Groups (NSGs) om inkomend verkeer te beveiligen.

De ASE-uitgaande afhankelijkheden worden bijna volledig gedefinieerd met FQDN's, die geen statische adressen achter zich hebben. Het ontbreken van statische adressen betekent dat netwerkbeveiligingsgroepen niet kunnen worden gebruikt om het uitgaande verkeer van een ASE af te sluiten. De adressen veranderen vaak genoeg dat men geen regels kan instellen op basis van de huidige resolutie en die niet gebruiken om NSG's te maken. 

De oplossing voor het beveiligen van uitgaande adressen ligt in het gebruik van een firewall-apparaat dat uitgaand verkeer op basis van domeinnamen kan beheren. Azure Firewall kan uitgaand HTTP- en HTTPS-verkeer beperken op basis van de FQDN van de bestemming.  

## <a name="system-architecture"></a>Systeemarchitectuur

Het implementeren van een ASE met uitgaand verkeer dat door een firewall-apparaat gaat, vereist het wijzigen van routes op het ASE-subnet. Routes werken op IP-niveau. Als u niet voorzichtig bent met het definiëren van uw routes, u TCP-antwoordverkeer naar de bron vanaf een ander adres dwingen. Wanneer uw antwoordadres verschilt van het adres waarnaar is verzonden, wordt het probleem asymmetrische routering genoemd en wordt het TCP gebroken.

Er moeten routes zijn gedefinieerd zodat inkomend verkeer naar de ASE kan terugantwoorden op dezelfde manier als het verkeer binnenkwam. Routes moeten worden gedefinieerd voor binnenkomende beheeraanvragen en voor binnenkomende aanvraagaanvragen.

Het verkeer van en naar een ASE moet zich houden aan de volgende verdragen

* Het verkeer naar Azure SQL, Storage en Event Hub wordt niet ondersteund met het gebruik van een firewall-apparaat. Dit verkeer moet rechtstreeks naar deze diensten worden verzonden. De manier om dat te laten gebeuren is door serviceeindpunten voor deze drie services te configureren. 
* Routetabelregels moeten worden gedefinieerd die binnenkomend beheerverkeer terugsturen van waar het vandaan komt.
* Routetabelregels moeten worden gedefinieerd die binnenkomend toepassingsverkeer terugsturen van waar het vandaan komt. 
* Al het andere verkeer dat de ASE verlaat, kan met een routetabelregel naar uw firewall-apparaat worden verzonden.

![ASE met Azure Firewall-verbindingsstroom][5]

## <a name="locking-down-inbound-management-traffic"></a>Binnenkomend beheerverkeer vergrendelen

Als uw ASE-subnet er nog geen NSG aan heeft toegewezen, maakt u er een. Stel binnen de NSG de eerste regel in om verkeer toe te staan vanaf de ServiceTag met de naam AppServiceManagement op poorten 454, 455. De regel om toegang toe te staan vanaf de AppServiceManagement-tag is het enige dat vereist is van openbare IP's om uw ASE te beheren. De adressen achter die servicetag worden alleen gebruikt om de Azure App-service te beheren. Het beheerverkeer dat door deze verbindingen stroomt, wordt versleuteld en beveiligd met verificatiecertificaten. Typisch verkeer op dit kanaal omvat zaken als door klanten geïnitieerde opdrachten en statussondes. 

Ases die via de portal worden gemaakt met een nieuw subnet worden gemaakt met een NSG die de allow regel voor de AppServiceManagement tag bevat.  

Uw ASE moet ook binnenkomende aanvragen toestaan van de tag Load Balancer op poort 16001. De verzoeken van de Load Balancer op poort 16001 zijn keep alive checks tussen de Load Balancer en de ASE front ends. Als poort 16001 is geblokkeerd, wordt uw ASE ongezond.

## <a name="configuring-azure-firewall-with-your-ase"></a>Azure Firewall configureren met uw ASE 

De stappen om uitgangen van uw bestaande ASE met Azure Firewall te vergrendelen zijn:

1. Serviceeindpunten inschakelen voor SQL, Storage en Event Hub op uw ASE-subnet. Als u serviceeindpunten wilt inschakelen, gaat u naar de > subnetten van de netwerkportal en selecteert u Microsoft.EventHub, Microsoft.SQL en Microsoft.Storage in de vervolgkeuzelijst Service-eindpunten. Wanneer serviceeindpunten zijn ingeschakeld voor Azure SQL, moeten alle Azure SQL-afhankelijkheden die uw apps hebben, ook worden geconfigureerd met serviceeindpunten. 

   ![serviceeindpunten selecteren][2]
  
1. Maak een subnet met de naam AzureFirewallSubnet in het VNet waar uw ASE bestaat. Volg de aanwijzingen in de [Azure Firewall-documentatie](https://docs.microsoft.com/azure/firewall/) om uw Azure Firewall te maken.

1. Selecteer in de >-regelverzameling van de toepassingsregel Azure Firewall > Regels de optie Toepassingsregelverzameling toevoegen. Geef een naam, prioriteit en stel Toestaan in. Geef in de sectie FQDN-tags een naam op, stel de bronadressen in op * en selecteer de FQDN-tag van de app-serviceomgeving en de Windows Update. 
   
   ![Toepassingsregel toevoegen][1]
   
1. Selecteer netwerkregelverzameling toevoegen in de Azure Firewall UI >-regels > netwerkregelverzameling. Geef een naam, prioriteit en stel Toestaan in. Geef in de sectie Regels onder IP-adressen een naam op, selecteer een ptocol van **Any**, set * op Bron- en bestemmingsadressen en stel de poorten in op 123. Met deze regel kan het systeem kloksynchronisatie uitvoeren met NTP. Maak een andere regel op dezelfde manier om 12000 te poortom te helpen triage eventuele systeemproblemen. 

   ![NTP-netwerkregel toevoegen][3]
   
1. Selecteer netwerkregelverzameling toevoegen in de Azure Firewall UI >-regels > netwerkregelverzameling. Geef een naam, prioriteit en stel Toestaan in. Geef in de sectie Regels onder Servicetags een naam op, selecteer een protocol van **Any**, set * op Bronadressen, selecteer een servicetag van AzureMonitor en stel de poorten in op 80, 443. Met deze regel kan het systeem Azure Monitor voorzien van informatie over status- en statistieken.

   ![Ntp-servicetagnetwerkregel toevoegen][6]
   
1. Maak een routetabel met de beheeradressen van adressen voor het beheer van [de app-serviceomgeving]( https://docs.microsoft.com/azure/app-service/environment/management-addresses) met een volgende internethop. De routetabelvermeldingen zijn nodig om asymmetrische routeproblemen te voorkomen. Voeg routes toe voor de ip-adresafhankelijkheden die hieronder worden vermeld in de afhankelijkheid van het IP-adres met een volgende internethop. Voeg een route voor virtueel toestel toe aan uw routetabel voor 0.0.0.0/0 met de volgende hop als privé-IP-adres van Azure Firewall. 

   ![Een routetabel maken][4]
   
1. Wijs de routetabel die u hebt gemaakt toe aan uw ASE-subnet.

#### <a name="deploying-your-ase-behind-a-firewall"></a>Uw ASE implementeren achter een firewall

De stappen om uw ASE achter een firewall te implementeren zijn hetzelfde als het configureren van uw bestaande ASE met een Azure Firewall, behalve dat u uw ASE-subnet moet maken en vervolgens de vorige stappen moet volgen. Als u uw ASE wilt maken in een bestaand subnet, moet u een resourcemanagersjabloon gebruiken zoals beschreven in het document voor [Het maken van uw ASE met een resourcebeheersjabloon.](https://docs.microsoft.com/azure/app-service/environment/create-from-template)

## <a name="application-traffic"></a>Toepassingsverkeer 

Met de bovenstaande stappen kan uw ASE probleemloos werken. U moet nog steeds dingen configureren om aan uw toepassingsbehoeften te voldoen. Er zijn twee problemen voor toepassingen in een ASE die is geconfigureerd met Azure Firewall.  

- Toepassingsafhankelijkheden moeten worden toegevoegd aan de Azure Firewall of de routetabel. 
- Er moeten routes worden gemaakt voor het toepassingsverkeer om asymmetrische routeringsproblemen te voorkomen

Als uw toepassingen afhankelijkheden hebben, moeten ze worden toegevoegd aan uw Azure Firewall. Maak toepassingsregels om HTTP/HTTPS-verkeer en netwerkregels voor al het andere toe te staan. 

Als u weet van welk adresbereik het verkeer van uw aanvraagaanvraag afkomstig is, u dat toevoegen aan de routetabel die is toegewezen aan uw ASE-subnet. Als het adresbereik groot of niet is opgegeven, u een netwerktoestel zoals de Toepassingsgateway gebruiken om u één adres te geven dat u aan uw routetabel toevoegen. Lees [Uw ILB ASE integreren met een Application Gateway voor](https://docs.microsoft.com/azure/app-service/environment/integrate-with-application-gateway) meer informatie over het configureren van een Application Gateway met uw ILB ASE

Dit gebruik van de Application Gateway is slechts één voorbeeld van het configureren van uw systeem. Als u dit pad hebt gevolgd, moet u een route toevoegen aan de ase-subnetroutetabel, zodat het antwoordverkeer dat naar de toepassingsgateway wordt verzonden, er rechtstreeks naartoe zou gaan. 

## <a name="logging"></a>Logboekregistratie 

Azure Firewall kan logboeken verzenden naar Azure Storage-, Event Hub- of Azure Monitor-logboeken. Als u uw app wilt integreren met een ondersteunde bestemming, gaat u naar de Azure Firewall-portal > diagnostische logboeken en schakelt u de logboeken in voor de gewenste bestemming. Als u integreert met Azure Monitor-logboeken, u logboekregistratie zien voor verkeer dat naar Azure Firewall wordt verzonden. Als u het verkeer wilt zien dat wordt geweigerd, opent u de werkruimteportal log-analyse > logboeken en voert u een query in zoals 

    AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
 
Het integreren van uw Azure Firewall met Azure Monitor-logboeken is handig wanneer u voor het eerst een toepassing laat werken wanneer u niet op de hoogte bent van alle toepassingsafhankelijkheden. Meer informatie over Azure Monitor-logboeken [vindt u in Azure Monitor.](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)
 
## <a name="dependencies"></a>Afhankelijkheden

De volgende informatie is alleen vereist als u een ander firewalltoestel dan Azure Firewall wilt configureren. 

- Services-serviceeindpuntservices moeten worden geconfigureerd met serviceeindpunten.
- Afhankelijkheden van IP-adressen zijn voor niet-HTTP/S-verkeer (zowel TCP- als UDP-verkeer)
- FQDN HTTP/HTTPS-eindpunten kunnen in uw firewallapparaat worden geplaatst.
- JokerHTTP/HTTPS-eindpunten zijn afhankelijkheden die kunnen variëren met uw ASE op basis van een aantal kwalificatietoernooien. 
- Linux-afhankelijkheden zijn alleen een punt van zorg als u Linux-apps implementeert in uw ASE. Als u geen Linux-apps implementeert in uw ASE, hoeven deze adressen niet aan uw firewall te worden toegevoegd. 

#### <a name="service-endpoint-capable-dependencies"></a>Afhankelijke afhankelijkheden met serviceeindpunt 

| Eindpunt |
|----------|
| Azure SQL |
| Azure Storage |
| Azure Event Hub |

#### <a name="ip-address-dependencies"></a>Afhankelijkheden van IP-adres

| Eindpunt | Details |
|----------| ----- |
| \*:123 | NTP klok te controleren. Verkeer wordt gecontroleerd op meerdere eindpunten op poort 123 |
| \*:12000 | Deze poort wordt gebruikt voor een aantal systeemmonitoring. Als geblokkeerd, dan zullen sommige problemen moeilijker te triage, maar uw ASE zal blijven werken |
| 40.77.24.27:80 | Nodig om ase-problemen te monitoren en te waarschuwen |
| 40.77.24.27:443 | Nodig om ase-problemen te monitoren en te waarschuwen |
| 13.90.249.229:80 | Nodig om ase-problemen te monitoren en te waarschuwen |
| 13.90.249.229:443 | Nodig om ase-problemen te monitoren en te waarschuwen |
| 104.45.230.69:80 | Nodig om ase-problemen te monitoren en te waarschuwen |
| 104.45.230.69:443 | Nodig om ase-problemen te monitoren en te waarschuwen |
| 13.82.184.151:80 | Nodig om ase-problemen te monitoren en te waarschuwen |
| 13.82.184.151:443 | Nodig om ase-problemen te monitoren en te waarschuwen |

Met een Azure Firewall krijgt u automatisch alles hieronder geconfigureerd met de FQDN-tags. 

#### <a name="fqdn-httphttps-dependencies"></a>FQDN HTTP/HTTPS-afhankelijkheden 

| Eindpunt |
|----------|
|graph.microsoft.com:443 |
|login.live.com:443 |
|login.windows.com:443 |
|login.windows.net:443 |
|login.microsoftonline.com:443 |
|client.wns.windows.com:443 |
|definitionupdates.microsoft.com:443 |
|go.microsoft.com:80 |
|go.microsoft.com:443 |
|www.microsoft.com:80 |
|www.microsoft.com:443 |
|wdcpalt.microsoft.com:443 |
|wdcp.microsoft.com:443 |
|ocsp.msocsp.com:443 |
|mscrl.microsoft.com:443 |
|mscrl.microsoft.com:80 |
|crl.microsoft.com:443 |
|crl.microsoft.com:80 |
|www.thawte.com:443 |
|crl3.digicert.com:80 |
|ocsp.digicert.com:80 |
|csc3-2009-2.crl.verisign.com:80 |
|crl.verisign.com:80 |
|ocsp.verisign.com:80 |
|cacerts.digicert.com:80 |
|azperfcounters1.blob.core.windows.net:443 |
|azurewatsonanalysis-prod.core.windows.net:443 |
|global.metrics.nsatc.net:80 |
|global.metrics.nsatc.net:443 |
|az-prod.metrics.nsatc.net:443 |
|antares.metrics.nsatc.net:443 |
|azglobal-black.azglobal.metrics.nsatc.net:443 |
|azglobal-red.azglobal.metrics.nsatc.net:443 |
|antares-black.antares.metrics.nsatc.net:443 |
|antares-red.antares.metrics.nsatc.net:443 |
|maupdateaccount.blob.core.windows.net:443 |
|clientconfig.passport.net:443 |
|packages.microsoft.com:443 |
|schemas.microsoft.com:80 |
|schemas.microsoft.com:443 |
|management.core.windows.net:443 |
|management.core.windows.net:80 |
|management.azure.com:443 |
|www.msftconnecttest.com:80 |
|shavamanifestcdnprod1.azureedge.net:443 |
|validation-v2.sls.microsoft.com:443 |
|flighting.cp.wd.microsoft.com:443 |
|dmd.metaservices.microsoft.com:80 |
|admin.core.windows.net:443 |
|prod.warmpath.msftcloudes.com:443 |
|prod.warmpath.msftcloudes.com:80 |
|azureprofileruploads.blob.core.windows.net:443 |
|azureprofileruploads2.blob.core.windows.net:443 |
|azureprofileruploads3.blob.core.windows.net:443 |
|azureprofileruploads4.blob.core.windows.net:443 |
|azureprofileruploads5.blob.core.windows.net:443 |
|azperfmerges.blob.core.windows.net:443 |
|azprofileruploads1.blob.core.windows.net:443 |
|azprofileruploads10.blob.core.windows.net:443 |
|azprofileruploads2.blob.core.windows.net:443 |
|azprofileruploads3.blob.core.windows.net:443 |
|azprofileruploads4.blob.core.windows.net:443 |
|azprofileruploads6.blob.core.windows.net:443 |
|azprofileruploads7.blob.core.windows.net:443 |
|azprofileruploads8.blob.core.windows.net:443 | 
|azprofileruploads9.blob.core.windows.net:443 |
|azureprofilerfrontdoor.cloudapp.net:443 |
|settings-win.data.microsoft.com:443 |
|maupdateaccount2.blob.core.windows.net:443 |
|maupdateaccount3.blob.core.windows.net:443 |
|dc.services.visualstudio.com:443 |
|gmstorageprodsn1.blob.core.windows.net:443 |
|gmstorageprodsn1.file.core.windows.net:443 |
|gmstorageprodsn1.queue.core.windows.net:443 |
|gmstorageprodsn1.table.core.windows.net:443 |
|rteventservice.trafficmanager.net:443 |
|ctldl.windowsupdate.com:80 |
|ctldl.windowsupdate.com:443 |

#### <a name="wildcard-httphttps-dependencies"></a>Afhankelijkheden van Jokerteken HTTP/HTTPS 

| Eindpunt |
|----------|
|gr-Prod-\*.cloudapp.net:443 |
| \*.management.azure.com:443 |
| \*.update.microsoft.com:443 |
| \*.windowsupdate.microsoft.com:443 |
| \*.identity.azure.net:443 |
| \*.ctldl.windowsupdate.com:80 |
| \*.ctldl.windowsupdate.com:443 |

#### <a name="linux-dependencies"></a>Linux-afhankelijkheden 

| Eindpunt |
|----------|
|wawsinfraprodbay063.blob.core.windows.net:443 |
|registry-1.docker.io:443 |
|auth.docker.io:443 |
|production.cloudflare.docker.com:443 |
|download.docker.com:443 |
|us.archive.ubuntu.com:80 |
|download.mono-project.com:80 |
|packages.treasuredata.com:80|
|security.ubuntu.com:80 |
| \*.cdn.mscr.io:443 |
|mcr.microsoft.com:443 |
|packages.fluentbit.io:80 |
|packages.fluentbit.io:443 |
|apt-mo.trafficmanager.net:80 |
|apt-mo.trafficmanager.net:443 |
|azure.archive.ubuntu.com:80 |
|azure.archive.ubuntu.com:443 |
|changelogs.ubuntu.com:80 |
|13.74.252.37:11371 |
|13.75.127.55:11371 |
|13.76.190.189:11371 |
|13.80.10.205:11371 |
|13.91.48.226:11371 |
|40.76.35.62:11371 |
|104.215.95.108:11371 |

## <a name="us-gov-dependencies"></a>Amerikaanse Gov afhankelijkheden

Volg de instructies in het gedeelte Azure Firewall configureren met het gedeelte [ASE](https://docs.microsoft.com/azure/app-service/environment/firewall-integration#configuring-azure-firewall-with-your-ase) van dit document om een Azure Firewall met uw ASE te configureren voor ASE's in amerikaanse gov-regio's.

Als u een ander apparaat dan Azure Firewall in US Gov wilt gebruiken 

* Services-serviceeindpuntservices moeten worden geconfigureerd met serviceeindpunten.
* FQDN HTTP/HTTPS-eindpunten kunnen in uw firewallapparaat worden geplaatst.
* JokerHTTP/HTTPS-eindpunten zijn afhankelijkheden die kunnen variëren met uw ASE op basis van een aantal kwalificatietoernooien.

Linux is niet beschikbaar in de Amerikaanse Gov regio's en is dus niet vermeld als een optionele configuratie.

#### <a name="service-endpoint-capable-dependencies"></a>Afhankelijke afhankelijkheden met serviceeindpunt ####

| Eindpunt |
|----------|
| Azure SQL |
| Azure Storage |
| Azure Event Hub |

#### <a name="ip-address-dependencies"></a>Afhankelijkheden van IP-adres

| Eindpunt | Details |
|----------| ----- |
| \*:123 | NTP klok te controleren. Verkeer wordt gecontroleerd op meerdere eindpunten op poort 123 |
| \*:12000 | Deze poort wordt gebruikt voor een aantal systeemmonitoring. Als geblokkeerd, dan zullen sommige problemen moeilijker te triage, maar uw ASE zal blijven werken |
| 40.77.24.27:80 | Nodig om ase-problemen te monitoren en te waarschuwen |
| 40.77.24.27:443 | Nodig om ase-problemen te monitoren en te waarschuwen |
| 13.90.249.229:80 | Nodig om ase-problemen te monitoren en te waarschuwen |
| 13.90.249.229:443 | Nodig om ase-problemen te monitoren en te waarschuwen |
| 104.45.230.69:80 | Nodig om ase-problemen te monitoren en te waarschuwen |
| 104.45.230.69:443 | Nodig om ase-problemen te monitoren en te waarschuwen |
| 13.82.184.151:80 | Nodig om ase-problemen te monitoren en te waarschuwen |
| 13.82.184.151:443 | Nodig om ase-problemen te monitoren en te waarschuwen |

#### <a name="dependencies"></a>Afhankelijkheden ####

| Eindpunt |
|----------|
| \*.ctldl.windowsupdate.com:80 |
| \*.management.usgovcloudapi.net:80 |
| \*.update.microsoft.com:80 |
|admin.core.usgovcloudapi.net:80 |
|azperfmerges.blob.core.windows.net:80 |
|azperfmerges.blob.core.windows.net:80 |
|azprofileruploads1.blob.core.windows.net:80 |
|azprofileruploads10.blob.core.windows.net:80 |
|azprofileruploads2.blob.core.windows.net:80 |
|azprofileruploads3.blob.core.windows.net:80 |
|azprofileruploads4.blob.core.windows.net:80 |
|azprofileruploads5.blob.core.windows.net:80 |
|azprofileruploads6.blob.core.windows.net:80 |
|azprofileruploads7.blob.core.windows.net:80 |
|azprofileruploads8.blob.core.windows.net:80 |
|azprofileruploads9.blob.core.windows.net:80 |
|azureprofilerfrontdoor.cloudapp.net:80 |
|azurewatsonanalysis.usgovcloudapp.net:80 |
|cacerts.digicert.com:80 |
|client.wns.windows.com:80 |
|crl.microsoft.com:80 |
|crl.verisign.com:80 |
|crl3.digicert.com:80 |
|csc3-2009-2.crl.verisign.com:80 |
|ctldl.windowsupdate.com:80 |
|definitionupdates.microsoft.com:80 |
|download.windowsupdate.com:80 |
|fairfax.warmpath.usgovcloudapi.net:80 |
|flighting.cp.wd.microsoft.com:80 |
|gcwsprodgmdm2billing.queue.core.usgovcloudapi.net:80 |
|gcwsprodgmdm2billing.table.core.usgovcloudapi.net:80 |
|global.metrics.nsatc.net:80 |
|go.microsoft.com:80 |
|gr-gcws-prod-bd3.usgovcloudapp.net:80 |
|gr-gcws-prod-bn1.usgovcloudapp.net:80 |
|gr-gcws-prod-dd3.usgovcloudapp.net:80 |
|gr-gcws-prod-dm2.usgovcloudapp.net:80 |
|gr-gcws-prod-phx20.usgovcloudapp.net:80 |
|gr-gcws-prod-sn5.usgovcloudapp.net:80 |
|login.live.com:80 |
|login.microsoftonline.us:80 |
|management.core.usgovcloudapi.net:80 |
|management.usgovcloudapi.net:80 |
|maupdateaccountff.blob.core.usgovcloudapi.net:80 |
|mscrl.microsoft.com
|ocsp.digicert.0 |
|ocsp.msocsp.co|
|ocsp.verisign.0 |
|rteventse.trafficmanager.net:80 |
|settings-n.data.microsoft.com:80 |
|shavamafestcdnprod1.azureedge.net:80 |
|shavanifestcdnprod1.azureedge.net:80 |
|v10ortex-win.data.microsoft.com:80 |
|wp.microsoft.com:80 |
|dcpalt.microsoft.com:80 |
|www.microsoft.com:80 |
|www.msftconnecttest.com:80 |
|www.thawte.com:80 |
|\*ctldl.windowsupdate.com:443 |
|\*.management.usgovcloudapi.net:443 |
|\*.update.microsoft.com:443 |
|admin.core.usgovcloudapi.net:443 |
|azperfmerges.blob.core.windows.net:443 |
|azperfmerges.blob.core.windows.net:443 |
|azprofileruploads1.blob.core.windows.net:443 |
|azprofileruploads10.blob.core.windows.net:443 |
|azprofileruploads2.blob.core.windows.net:443 |
|azprofileruploads3.blob.core.windows.net:443 |
|azprofileruploads4.blob.core.windows.net:443 |
|azprofileruploads5.blob.core.windows.net:443 |
|azprofileruploads6.blob.core.windows.net:443 |
|azprofileruploads7.blob.core.windows.net:443 |
|azprofileruploads8.blob.core.windows.net:443 |
|azprofileruploads9.blob.core.windows.net:443 |
|azureprofilerfrontdoor.cloudapp.net:443 |
|azurewatsonanalysis.usgovcloudapp.net:443 |
|cacerts.digicert.com:443 |
|client.wns.windows.com:443 |
|crl.microsoft.com:443 |
|crl.verisign.com:443 |
|crl3.digicert.com:443 |
|csc3-2009-2.crl.verisign.com:443 |
|ctldl.windowsupdate.com:443 |
|definitionupdates.microsoft.com:443 |
|download.windowsupdate.com:443 |
|fairfax.warmpath.usgovcloudapi.net:443 |
|flighting.cp.wd.microsoft.com:443 |
|gcwsprodgmdm2billing.queue.core.usgovcloudapi.net:443 |
|gcwsprodgmdm2billing.table.core.usgovcloudapi.net:443 |
|global.metrics.nsatc.net:443 |
|go.microsoft.com:443 |
|gr-gcws-prod-bd3.usgovcloudapp.net:443 |
|gr-gcws-prod-bn1.usgovcloudapp.net:443 |
|gr-gcws-prod-dd3.usgovcloudapp.net:443 |
|gr-gcws-prod-dm2.usgovcloudapp.net:443 |
|gr-gcws-prod-phx20.usgovcloudapp.net:443 |
|gr-gcws-prod-sn5.usgovcloudapp.net:443 |
|login.live.com:443 |
|login.microsoftonline.us:443 |
|management.core.usgovcloudapi.net:443 |
|management.usgovcloudapi.net:443 |
|maupdateaccountff.blob.core.usgovcloudapi.net:443 |
|mscrl.microsoft.com:443 |
|ocsp.digicert.com:443 |
|ocsp.msocsp.com:443 |
|ocsp.verisign.com:443 |
|rteventservice.trafficmanager.net:443 |
|settings-win.data.microsoft.com:443 |
|shavamanifestcdnprod1.azureedge.net:443 |
|shavamanifestcdnprod1.azureedge.net:443 |
|v10.vortex-win.data.microsoft.com:443 |
|wdcp.microsoft.com:443 |
|wdcpalt.microsoft.com:443 |
|www.microsoft.com:443 |
|www.msftconnecttest.com:443 |
|www.thawte.com:443 |

<!--Image references-->
[1]: ./media/firewall-integration/firewall-apprule.png
[2]: ./media/firewall-integration/firewall-serviceendpoints.png
[3]: ./media/firewall-integration/firewall-ntprule.png
[4]: ./media/firewall-integration/firewall-routetable.png
[5]: ./media/firewall-integration/firewall-topology.png
[6]: ./media/firewall-integration/firewall-ntprule-monitor.png
