---
title: Azure DNS gebruiken met andere Azure-services
description: Ga in dit leerpad aan de slag met het gebruik van Azure DNS om namen voor andere Azure-services op te lossen
services: dns
documentationcenter: na
author: rohinkoul
manager: kumudD
tags: azure dns
ms.assetid: e9b5eb94-7984-4640-9930-564bb9e82b78
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 09/21/2016
ms.author: rohink
ms.openlocfilehash: fa2c1ced6405c967ca33562d6215b304b8507e5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76937242"
---
# <a name="how-azure-dns-works-with-other-azure-services"></a>Hoe Azure DNS werkt met andere Azure-services

Azure DNS is een gehoste DNS-beheer- en naamomzettingsservice. U het gebruiken om openbare DNS-namen te maken voor andere toepassingen en services die u implementeert in Azure. Het maken van een naam voor een Azure-service in uw aangepaste domein is eenvoudig. U voegt gewoon een record van het juiste type voor uw service.

* Voor dynamisch toegewezen IP-adressen u een DNS CNAME-record maken die wordt toegewezen aan de DNS-naam die Azure voor uw service heeft gemaakt. DNS-standaarden voorkomen dat u een CNAME-record gebruikt voor de zonetop. U in plaats daarvan een aliasrecord gebruiken. Zie [Zelfstudie: Een aliasrecord configureren om te verwijzen naar een Azure Public IP-adres](tutorial-alias-pip.md).
* Voor statisch toegewezen IP-adressen u een DNS A-record maken met behulp van een naam, die een *naakte domeinnaam* bevat op de zonetop.

In de volgende tabel worden de ondersteunde recordtypen beschreven die u gebruiken voor verschillende Azure-services. Zoals uit de tabel blijkt, ondersteunt Azure DNS alleen DNS-records voor netwerkbronnen die met internet te maken hebben. Azure DNS kan niet worden gebruikt voor naamomzetting van interne privéadressen.

| Azure-service | Netwerkinterface | Beschrijving |
| --- | --- | --- |
| Azure Application Gateway |[Front-end openbaar IP](dns-custom-domain.md#public-ip-address) |U een DNS A- of CNAME-record maken. |
| Azure Load Balancer |[Front-end openbaar IP](dns-custom-domain.md#public-ip-address) |U een DNS A- of CNAME-record maken. Load Balancer kan een IPv6-openbaar IP-adres hebben dat dynamisch is toegewezen. Maak een CNAME-record voor een IPv6-adres. |
| Azure Traffic Manager |Openbare naam |U een aliasrecord maken die wordt toegewezen aan de trafficmanager.net naam die is toegewezen aan uw Traffic Manager-profiel. Zie [Zelfstudie: Een aliasrecord configureren om apex-domeinnamen te ondersteunen met Traffic Manager](tutorial-alias-tm.md)voor meer informatie. |
| Azure Cloud Services |[Openbaar IP](dns-custom-domain.md#public-ip-address) |Voor statisch toegewezen IP-adressen u een DNS A-record maken. Voor dynamisch toegewezen IP-adressen moet u een CNAME-record maken die wordt toegewezen aan de *cloudapp.net* naam.|
| Azure App Service | [Extern IP-adres](dns-custom-domain.md#app-service-web-apps) |Voor externe IP-adressen u een DNS A-record maken. Anders moet u een CNAME-record maken die wordt toegewezen aan de azurewebsites.net naam. Zie [Een aangepaste domeinnaam toewijzen aan een Azure-app voor](../app-service/app-service-web-tutorial-custom-domain.md)meer informatie. |
| VM's van Azure Resource Manager |[Openbaar IP](dns-custom-domain.md#public-ip-address) |Vm's voor Resourcemanager kunnen openbare IP-adressen hebben. Een VM met een openbaar IP-adres kan ook achter een load balancer zitten. U een DNS A-, CNAME- of aliasrecord maken voor het openbare adres. U deze aangepaste naam gebruiken om de VIP op de load balancer te omzeilen. |
| Klassieke VM's |[Openbaar IP](dns-custom-domain.md#public-ip-address) |Klassieke VM's die zijn gemaakt met PowerShell of CLI kunnen worden geconfigureerd met een dynamisch of statisch (gereserveerd) virtueel adres. U respectievelijk een DNS CNAME- of Een-record maken. |
