---
title: Wat is Azure Cloud Services | Microsoft Documenten
description: Meer informatie over wat Azure Cloud Services is.
services: cloud-services
author: tgore03
ms.service: multiple
ms.topic: article
ms.date: 04/19/2017
ms.author: tagore
ms.openlocfilehash: c531e02656c9f6342670024b2220386e789a2d98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75386847"
---
# <a name="overview-of-azure-cloud-services"></a>Overzicht van Azure Cloud Services
Azure Cloud Services is een voorbeeld van [een platform as a service](https://azure.microsoft.com/overview/what-is-paas/) (PaaS). Net als [Azure App Service](../app-service/overview.md)is deze technologie ontworpen om toepassingen te ondersteunen die schaalbaar, betrouwbaar en goedkoop zijn om te werken. Net zoals App Service wordt gehost op virtuele machines (VM's), is azure cloud services dat ook. U hebt echter meer controle over de VM's. U uw eigen software installeren op VM's die Azure Cloud Services gebruiken en u deze op afstand openen.

![Azure Cloud Services-diagram](./media/cloud-services-choose-me/diagram.png)

Meer controle betekent ook minder gebruiksgemak. Tenzij u de extra besturingselementopties nodig hebt, is het doorgaans sneller en eenvoudiger om een webtoepassing in de Web Apps-functie van App Service te gebruiken in vergelijking met Azure Cloud Services.

Er zijn twee typen Azure Cloud Services-rollen. Het enige verschil tussen de twee is hoe uw rol wordt gehost op de VM's:

* **Webrol:** implementeert en host uw app automatisch via IIS.

* **Functie van werknemer:** maakt geen gebruik van IIS en voert uw app zelfstandig uit.

Een eenvoudige toepassing kan bijvoorbeeld slechts één webrol gebruiken, die een website bedient. Een complexere toepassing kan een webrol gebruiken om binnenkomende aanvragen van gebruikers te verwerken en deze aanvragen vervolgens door te geven aan een werkrol voor verwerking. (Deze communicatie kan gebruik maken van [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) of Azure Queue [storage](../storage/common/storage-introduction.md).)

Zoals de vorige figuur suggereert, worden alle VM's in één toepassing uitgevoerd in dezelfde cloudservice. Gebruikers hebben toegang tot de toepassing via één openbaar IP-adres, waarbij aanvragen automatisch in evenwicht worden gebracht in de VM's van de toepassing. Het platform [schaalt en implementeert](cloud-services-how-to-scale-portal.md) de VM's in een Azure Cloud Services-toepassing op een manier die één punt van hardwarefout voorkomt.

Hoewel toepassingen in VM's worden uitgevoerd, is het belangrijk om te begrijpen dat Azure Cloud Services PaaS biedt, geen infrastructuur als een service (IaaS). Hier is een manier om erover na te denken. Met IaaS, zoals Azure Virtual Machines, maakt en configureert u eerst de omgeving waarin uw toepassing wordt uitgevoerd. Vervolgens implementeert u uw toepassing in deze omgeving. U bent verantwoordelijk voor het beheer van een groot deel van deze wereld, door dingen te doen, zoals het implementeren van nieuwe gepatchte versies van het besturingssysteem in elke VM. In PaaS daarentegen is het alsof de omgeving al bestaat. Het enige wat u hoeft te doen is uw toepassing implementeren. Het beheer van het platform waarop het draait, inclusief het implementeren van nieuwe versies van het besturingssysteem, wordt voor u afgehandeld.

## <a name="scaling-and-management"></a>Schalen en beheren
Met Azure Cloud Services maakt u geen virtuele machines. In plaats daarvan biedt u een configuratiebestand op dat Azure vertelt hoeveel van elk dat u wilt, zoals 'drie webrolexemplaren' en 'twee rolinstanties voor werknemers'. Het platform maakt ze dan voor u. U kiest nog steeds [welke grootte](cloud-services-sizes-specs.md) die back-VM's moeten zijn, maar u maakt ze niet expliciet zelf. Als uw toepassing een grotere belasting moet verwerken, u om meer VM's vragen en Azure maakt deze exemplaren. Als de belasting afneemt, u deze exemplaren afsluiten en stoppen met betalen.

Een Azure Cloud Services-toepassing wordt doorgaans beschikbaar gesteld aan gebruikers via een proces in twee stappen. Een ontwikkelaar [uploadt de toepassing](cloud-services-how-to-create-deploy-portal.md) eerst naar het faseringsgebied van het platform. Wanneer de ontwikkelaar klaar is om de toepassing live te maken, gebruiken ze de Azure-portal om fasering te ruilen met productie. Deze [switch tussen staging en productie](cloud-services-how-to-manage-portal.md#swap-deployments-to-promote-a-staged-deployment-to-production) kan worden gedaan zonder downtime, waardoor een draaiende toepassing kan worden geüpgraded naar een nieuwe versie zonder de gebruikers te storen.

## <a name="monitoring"></a>Bewaking
Azure Cloud Services biedt ook monitoring. Net als virtuele machines detecteert het een mislukte fysieke server en start de VM's die op die server op een nieuwe server werden uitgevoerd opnieuw. Maar Azure Cloud Services detecteert ook mislukte VM's en toepassingen, niet alleen hardwarefouten. In tegenstelling tot virtuele machines, het heeft een agent in elke web en werknemer rol, en dus het is in staat om nieuwe VM's en toepassingsinstanties te starten wanneer er fouten optreden.

Het PaaS-karakter van Azure Cloud Services heeft ook andere implicaties. Een van de belangrijkste is dat toepassingen die op deze technologie zijn gebouwd, moeten worden geschreven om correct te worden uitgevoerd wanneer een web- of werknemersrolinstantie mislukt. Om dit te bereiken, mag een Azure Cloud Services-toepassing de status in het bestandssysteem van zijn eigen VM's niet behouden. In tegenstelling tot VM's die met virtuele machines zijn gemaakt, zijn schrijfbewerkingen naar Azure Cloud Services VM's niet blijvend. Er gaat niets boven een Virtual Machines data schijf. In plaats daarvan moet een Azure Cloud Services-toepassing expliciet alle statusnaar Azure SQL Database, blobs, tabellen of een andere externe opslag schrijven. Door toepassingen op deze manier te bouwen, kunnen ze eenvoudiger worden opschaald en beter bestand zijn tegen storingen, wat beide belangrijke doelen van Azure Cloud Services zijn.

## <a name="next-steps"></a>Volgende stappen
* [Een cloudservice-app maken in .NET](cloud-services-dotnet-get-started.md) 
* [Een cloudservice-app maken in Node.js](cloud-services-nodejs-develop-deploy-app.md) 
* [Een cloudservice-app maken in PHP](../cloud-services-php-create-web-role.md) 
* [Een cloudservice-app maken in Python](cloud-services-python-ptvs.md)






