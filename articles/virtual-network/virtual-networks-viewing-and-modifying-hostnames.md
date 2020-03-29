---
title: Hostnamen weergeven en wijzigen | Microsoft Documenten
description: Hostnamen voor virtuele Azure-machines, web- en werknemersrollen weergeven en wijzigen voor naamomzetting
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: tysonn
ms.assetid: c668cd8e-4e43-4d05-acc3-db64fa78d828
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: cce248e2906f4a36737388e8cc7124b1bb19fbae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71058677"
---
# <a name="viewing-and-modifying-hostnames"></a>Hostnamen weergeven en wijzigen
Als u wilt dat naar de hostnaam van uw roltypen wordt verwezen, moet u de waarde voor de hostnaam instellen in het serviceconfiguratiebestand voor elke rol. U doet dat door de gewenste hostnaam toe te voegen aan het **kenmerk vmName** van het **element Rol.** De waarde van het **kenmerk vmName** wordt gebruikt als basis voor de hostnaam van elke rolinstantie. Als **vmName** bijvoorbeeld *webrole* is en er drie exemplaren van die rol zijn, zijn de hostnamen van de instanties *webrole0,* *webrole1*en *webrole2*. U hoeft geen hostnaam voor virtuele machines op te geven in het configuratiebestand, omdat de hostnaam voor een virtuele machine wordt ingevuld op basis van de naam van de virtuele machine. Zie [Azure Service Configuration Schema (.cscfg File)](https://msdn.microsoft.com/library/azure/ee758710.aspx) voor meer informatie over het configureren van een Microsoft Azure-service.

## <a name="viewing-hostnames"></a>Hostnamen weergeven
U de hostnamen van virtuele machines en rolexemplaren in een cloudservice bekijken met behulp van een van de onderstaande hulpprogramma's.

### <a name="service-configuration-file"></a>Serviceconfiguratiebestand
U het serviceconfiguratiebestand voor een geïmplementeerde service downloaden vanuit het blad **configureren** van de service in de Azure-portal. U vervolgens zoeken naar het **kenmerk vmName** voor het element **Rolnaam** om de hostnaam weer te geven. Houd er rekening mee dat deze hostnaam wordt gebruikt als basis voor de hostnaam van elke rolinstantie. Als **vmName** bijvoorbeeld *webrole* is en er drie exemplaren van die rol zijn, zijn de hostnamen van de instanties *webrole0,* *webrole1*en *webrole2*.

### <a name="remote-desktop"></a>Extern bureaublad
Nadat u Verbindingen voor Extern bureaublad (Windows), Windows PowerShell -remoting (Windows) of SSH (Linux en Windows) hebt ingeschakeld op uw virtuele machines of rolinstanties, u de hostnaam op verschillende manieren bekijken vanaf een actieve Verbinding met Extern bureaublad:

* Typ hostname bij de opdrachtprompt of SSH-terminal.
* Typ ipconfig /all op de opdrachtprompt (alleen Windows).
* Bekijk de computernaam in de systeeminstellingen (alleen Windows).

### <a name="azure-service-management-rest-api"></a>Azure Service Management REST API
Volg de volgende instructies van een REST-client:

1. Zorg ervoor dat u een clientcertificaat hebt om verbinding te maken met de Azure-portal. Volg de stappen die worden aangeboden in [Hoe: Publicatie-instellingen en abonnementsgegevens downloaden en importeren](https://msdn.microsoft.com/library/dn385850.aspx). 
2. Stel een koptekstitem met de naam x-ms-versie in met een waarde van 2013-11-01.
3. Stuur een aanvraag in de\/volgende\<indeling: https:\>https: /management.core.windows.net/\<subscrition-id /services/hostedservices/ servicenaam\>?embed-detail=true
4. Zoek naar het **element HostName** voor elk **Element RoleInstance.**

> [!WARNING]
> U ook het interne domeinachtervoegsel voor uw cloudservice bekijken vanuit het REST-oproepantwoord door het element **InternalDnsSuffix te** controleren of door ipconfig /allen uit te voeren vanaf een opdrachtprompt in een Remote Desktop-sessie (Windows) of door kat /etc/resolv.conf.conf uit een SSH-terminal (Linux) te voeren.
> 
> 

## <a name="modifying-a-hostname"></a>Een hostnaam wijzigen
U de hostnaam voor elke virtuele machine of rolinstantie wijzigen door een gewijzigd serviceconfiguratiebestand te uploaden of door de naam van de computer te wijzigen vanuit een Extern bureaublad-sessie.

## <a name="next-steps"></a>Volgende stappen
[Naamomzetting (DNS)](virtual-networks-name-resolution-for-vms-and-role-instances.md)

[Azure Service-configuratieschema (.cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710.aspx)

[Azure Virtual Network Configuration Schema](https://go.microsoft.com/fwlink/?LinkId=248093)

[DNS-instellingen opgeven met netwerkconfiguratiebestanden](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md)

