---
title: Beheeradressen
description: Zoek de beheeradressen die worden gebruikt om een App-serviceomgeving te beheren. Configureer ze in een routetabel om asymmetrische routeproblemen te voorkomen.
author: ccompy
ms.assetid: a7738a24-89ef-43d3-bff1-77f43d5a3952
ms.topic: article
ms.date: 11/13/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 7d7f97552e8faadee1af928a9ce4e1eea2df476e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74687119"
---
# <a name="app-service-environment-management-addresses"></a>Adressen voor beheer van de app-serviceomgeving

De App Service Environment (ASE) is een enkele tenant implementatie van de Azure App Service die wordt uitgevoerd in uw Azure Virtual Network (VNet).  Hoewel de ASE wel wordt uitgevoerd in uw VNet, moet deze nog steeds toegankelijk zijn vanaf een aantal speciale IP-adressen die door de Azure App Service worden gebruikt om de service te beheren.  In het geval van een ASE doorkruist het beheerverkeer het door de gebruiker gecontroleerde netwerk. Als dit verkeer wordt geblokkeerd of verkeerd wordt omgeleid, wordt de ASE opgeschort. Lees [netwerkoverwegingen en de app-serviceomgeving][networking]voor meer informatie over de afhankelijkheid van ASE-netwerken. Voor algemene informatie over de ASE u beginnen met [Inleiding tot de App-serviceomgeving.][intro]

Alle ASEs hebben een openbare VIP die het beheer verkeer komt in. Het inkomende beheerverkeer van deze adressen komt van havens 454 en 455 op de openbare VIP van uw ASE. In dit document worden de bronadressen van de App-service voor beheerverkeer naar de ASE weergegeven. Deze adressen staan ook in de IP-servicetag met de naam AppServiceManagement.

De onderstaande adressen kunnen worden geconfigureerd in een routetabel om asymmetrische routeproblemen met het beheerverkeer te voorkomen. Routes werken op het ip-niveau en hebben geen bewustzijn van de verkeersrichting of dat het verkeer deel uitmaakt van een TCP-antwoordbericht. Als het antwoordadres voor een TCP-verzoek anders is dan het adres waarnaar is verzonden, hebt u een asymmetrisch routeprobleem. Om asymmetrische routeproblemen met uw ASE-beheerverkeer te voorkomen, moet u ervoor zorgen dat antwoorden worden teruggestuurd vanaf hetzelfde adres waarnaar ze zijn verzonden. Lees Uw ASE configureren [met geforceerde tunneling][forcedtunnel] voor meer informatie over het configureren van uw ASE om te werken in een omgeving waarin uitgaand verkeer op locatie wordt verzonden

## <a name="list-of-management-addresses"></a>Lijst met beheeradressen ##

| Regio | Adressen |
|--------|-----------|
| Alle openbare regio's | 13.64.115.203, 13.66.140.0, 13.67.8.128, 13.69.64.128, 13.69.227.128, 13.70.73.128, 13.71.170.64, 13.71.194.129, 13.75.34.192, 13.75.127.117, 13.77.50.128, 13.78.109.0, 13.89.171.0, 13.94.141.115, 13.94.143.126, 13.94.149.179, 20.36.106.128, 20.36.114.64, 23.100.226.236, 23.102.135.246, 23.102.188.65, 40.69.106.128, 40.70.146.128, 40.71.13.64, 40.74.100.64, 40.78.194.128, 40.79.130.64, 40.79.178.128, 40.83.120.64, 40.83.121.56, 40.83.125.161, 40.90.240.166, 40.91.126.196, 40.112.242.192, 40.119.4.111, 40.124.47.188, 51.140.146.64, 51.140.210.128, 52.151.25.45, 52.162.80.89, 52.162.106.192, 52.165.152.214, 52.165.153.122, 52.165.154.193, 52.165.158.140, 52.174.22.21, 52.178.177.147, 52.178.184.149, 52.178.190.65, 52.178.195.197, 52.187.56.50, 52.187.59.251, 52.187.63.19, 52.187.63.37, 52.224.105.172, 52.225.177.153, 52.231.18.64, 52.231.146.128, 65.52.14.230, 65.52.172.237, 65.52.193.203, 70.37.57.58, 70.37.89.222, 104.43.242.137, 104.44.129.141, 104.44.129.243, 104.44.129.255, 104.44.134.255, 104.208.54.11, 104.211.81.64, 104.211.146.128, 104.214.49.0, 157.55.176.93, 157.55.208.185, 191.233.203.64, 191.236.154.88 |
| Microsoft Azure-overheid | 23.97.29.209, 13.72.53.37, 13.72.180.105, 23.97.0.17, 23.97.16.184 |

## <a name="configuring-a-network-security-group"></a>Een netwerkbeveiligingsgroep configureren

Met netwerkbeveiligingsgroepen hoeft u zich geen zorgen te maken over de afzonderlijke adressen of uw eigen configuratie te behouden. Er is een IP-servicetag met de naam AppServiceManagement die up-to-date wordt gehouden met alle adressen. Als u deze IP-servicetag in uw NSG wilt gebruiken, gaat u naar de portal, opent u de gebruikersinterface van netwerkbeveiligingsgroepen en selecteert u Binnenkomende beveiligingsregels. Als u een reeds bestaande regel hebt voor het binnenkomende beheerverkeer, bewerkt u deze. Als deze NSG niet is gemaakt met uw ASE of als deze allemaal nieuw is, selecteert u **Toevoegen**. Selecteer **servicetag**onder de vervolgkeuzelijst Bron .  Selecteer **AppServiceManagement**onder de bronservicetag . Stel de bronpoortbereiken in \*op , Bestemming op **Elke**, Doelpoortbereiken op **454-455,** Protocol bij **TCP**en Actie om het mogelijk te **maken**. Als u de regel maakt, moet u de prioriteit instellen. 

![een NSG maken met de servicetag][1]

## <a name="configuring-a-route-table"></a>Een routetabel configureren

De beheeradressen kunnen worden geplaatst in een routetabel met een volgende hop van internet om ervoor te zorgen dat al het inkomende beheerverkeer in staat is om terug te gaan via hetzelfde pad. Deze routes zijn nodig bij het configureren van gedwongen tunneling. Als u de routetabel wilt maken, u de portal, PowerShell of Azure CLI gebruiken.  De opdrachten voor het maken van een routetabel met Azure CLI vanuit een PowerShell-prompt staan hieronder. 

    $rg = "resource group name"
    $rt = "route table name"
    $location = "azure location"
    $managementAddresses = "13.64.115.203", "13.66.140.0", "13.67.8.128", "13.69.64.128", "13.69.227.128", "13.70.73.128", "13.71.170.64", "13.71.194.129", "13.75.34.192", "13.75.127.117", "13.77.50.128", "13.78.109.0", "13.89.171.0", "13.94.141.115", "13.94.143.126", "13.94.149.179", "20.36.106.128", "20.36.114.64", "23.100.226.236", "23.102.135.246", "23.102.188.65", "40.69.106.128", "40.70.146.128", "40.71.13.64", "40.74.100.64", "40.78.194.128", "40.79.130.64", "40.79.178.128", "40.83.120.64", "40.83.121.56", "40.83.125.161", "40.90.240.166", "40.91.126.196", "40.112.242.192", "40.119.4.111", "40.124.47.188", "51.140.146.64", "51.140.210.128", "52.151.25.45", "52.162.80.89", "52.162.106.192", "52.165.152.214", "52.165.153.122", "52.165.154.193", "52.165.158.140", "52.174.22.21", "52.178.177.147", "52.178.184.149", "52.178.190.65", "52.178.195.197", "52.187.56.50", "52.187.59.251", "52.187.63.19", "52.187.63.37", "52.224.105.172", "52.225.177.153", "52.231.18.64", "52.231.146.128", "65.52.14.230", "65.52.172.237", "65.52.193.203", "70.37.57.58", "70.37.89.222", "104.43.242.137", "104.44.129.141", "104.44.129.243", "104.44.129.255", "104.44.134.255", "104.208.54.11", "104.211.81.64", "104.211.146.128", "104.214.49.0", "157.55.176.93", "157.55.208.185", "191.233.203.64", "191.236.154.88"

    az network route-table create --name $rt --resource-group $rg --location $location
    foreach ($ip in $managementAddresses) {
        az network route-table route create -g $rg --route-table-name $rt -n $ip --next-hop-type Internet --address-prefix ($ip + "/32")
    }

Nadat uw routetabel is gemaakt, moet u deze instellen op uw ASE-subnet.  

## <a name="get-your-management-addresses-from-api"></a>Haal uw beheeradressen uit API ##

U de beheeradressen die overeenkomen met uw ASE vermelden met de volgende API-aanroep.

    get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01

De API retourneert een JSON-document met alle binnenkomende adressen voor uw ASE. De lijst met adressen bevat de beheeradressen, de VIP die wordt gebruikt door uw ASE en het ASE-subnetadresbereik zelf.  

Als u de API met de [armclient wilt aanroepen,](https://github.com/projectkudu/ARMClient) gebruikt u de volgende opdrachten, maar vervangt u de naam van uw abonnements-id, resourcegroep en ASE.  

    armclient login
    armclient get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01


<!--IMAGES-->
[1]: ./media/management-addresses/managementaddr-nsg.png

<!-- LINKS -->
[networking]: ./network-info.md
[intro]: ./intro.md
[forcedtunnel]: ./forced-tunnel-support.md
