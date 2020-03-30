---
title: Azure Analysis Services hoge beschikbaarheid | Microsoft Documenten
description: In dit artikel wordt beschreven hoe Azure Analysis Services een hoge beschikbaarheid biedt tijdens serviceonderbreking.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 2e750dce804ea93f3d3068ffd36bc7a73a50906a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73573363"
---
# <a name="analysis-services-high-availability"></a>Analysis Services hoge beschikbaarheid

In dit artikel wordt beschreven dat de hoge beschikbaarheid voor Azure Analysis Services-servers hoog beschikbaar is. 

## <a name="assuring-high-availability-during-a-service-disruption"></a>Zorgen voor een hoge beschikbaarheid tijdens een serviceonderbreking

Hoewel dit zeldzaam is, kan een Azure-datacenter een storing hebben. Wanneer er een storing optreedt, veroorzaakt dit een bedrijfsonderbreking die een paar minuten kan duren of uren kan duren. Hoge beschikbaarheid wordt meestal bereikt met serverredundantie. Met Azure Analysis Services u redundantie bereiken door extra secundaire servers in een of meer regio's te maken. Bij het maken van redundante servers, om ervoor te zorgen dat de gegevens en metagegevens op die servers gesynchroniseerd zijn met de server in een regio die offline is gegaan, u:

* Implementeer modellen naar redundante servers in andere regio's. Deze methode vereist het verwerken van gegevens op zowel uw primaire server als redundante servers parallel, zodat alle servers gesynchroniseerd zijn.

* [Back-ups](analysis-services-backup.md) van databases van uw primaire server en herstellen op redundante servers. U bijvoorbeeld nachtelijke back-ups naar Azure-opslag automatiseren en herstellen naar andere redundante servers in andere regio's. 

Als uw primaire server in beide gevallen een storing ondervindt, moet u de verbindingstekenreeksen wijzigen bij het rapporteren van clients om verbinding te maken met de server in een ander regionaal datacenter. Deze wijziging moet worden beschouwd als een laatste redmiddel en alleen als er een catastrofale regionale datacenterstoring optreedt. Het is waarschijnlijker dat een datacenterstoring bij het hosten van uw primaire server weer online komt voordat u verbindingen op alle clients bijwerken. 

Om te voorkomen dat de verbindingstekenreeksen voor rapportageclients moeten worden gewijzigd, u een [serveralias](analysis-services-server-alias.md) voor uw primaire server maken. Als de primaire server uitvalt, u de alias wijzigen die naar een redundante server in een andere regio moet worden aanwijs. U alias naar servernaam automatiseren door een endpoint statuscontrole op de primaire server te coderen. Als de statuscontrole mislukt, kan hetzelfde eindpunt rechtstreeks naar een redundante server in een andere regio. 

## <a name="related-information"></a>Gerelateerde informatie

[Back-up maken en herstellen](analysis-services-backup.md)   
[Azure-analyseservices beheren](analysis-services-manage.md)   
[Namen van aliasservers](analysis-services-server-alias.md) 

