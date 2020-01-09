---
title: Detectie van Azure Service Layer Threat-Azure Security Center
description: In dit onderwerp vindt u de waarschuwingen van de Azure-service laag die beschikbaar zijn in Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: conceptual
ms.date: 08/25/2019
ms.author: memildin
ms.openlocfilehash: d4033989830323856ac14ed06eea7df74806f128
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/06/2020
ms.locfileid: "75665696"
---
# <a name="threat-detection-for-the-azure-service-layer-in-azure-security-center"></a>Detectie van dreigingen voor de service laag van Azure in Azure Security Center

Dit onderwerp bevat de beschik bare waarschuwingen voor Azure Security Center bij het bewaken van de volgende Azure-service lagen:

* [Azure-netwerklaag](#network-layer)
* [Azure Management Layer (Azure Resource Manager) (preview-versie)](#management-layer)
* [Azure Key Vault](#azure-keyvault)

## Azure-netwerklaag<a name="network-layer"></a>

Security Center Network-laag analyse zijn gebaseerd op voorbeeld [gegevens van IPFIX](https://en.wikipedia.org/wiki/IP_Flow_Information_Export), die pakket headers zijn die worden verzameld door Azure core-routers. Op basis van deze gegevensfeed gebruikt Security Center machine learning modellen om kwaad aardige verkeers activiteiten te identificeren en te markeren. Security Center gebruikt de micro soft Threat Intelligence-data base ook om IP-adressen te verrijken.

Sommige netwerk configuraties kunnen Security Center beperken van het genereren van waarschuwingen over verdachte netwerk activiteit. Zorg ervoor dat de volgende handelingen worden Security Center om netwerk waarschuwingen te genereren:

- De virtuele machine heeft een openbaar IP-adres (of bevindt zich op een load balancer met een openbaar IP-adres).

- Het netwerk verkeer van de virtuele machine wordt niet geblokkeerd door een externe ID-oplossing.

- De virtuele machine is toegewezen aan hetzelfde IP-adres voor het hele uur waarin de verdachte communicatie heeft plaatsgevonden. Dit geldt ook voor virtuele machines die zijn gemaakt als onderdeel van een beheerde service (bijvoorbeeld AKS, Databricks).

Zie de [naslag tabel met waarschuwingen](alerts-reference.md#alerts-azurenetlayer)voor een overzicht van de waarschuwingen van de Azure-netwerklaag.

Zie voor meer informatie over hoe Security Center netwerk signalen kunt gebruiken voor het Toep assen van bedreigings beveiliging de [heuristische DNS-detecties in Security Center](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/).


## Azure Management Layer (Azure Resource Manager) (preview-versie)<a name ="management-layer"></a>

De beveiligingslaag van Security Center is gebaseerd op Azure Resource Manager momenteel als preview-versie beschikbaar is.

Security Center biedt een extra beveiligingslaag door gebruik te maken van Azure Resource Manager-gebeurtenissen. dit wordt beschouwd als het controle vlak voor Azure. Door de Azure Resource Manager-records te analyseren, Security Center een ongebruikelijke of mogelijk schadelijke bewerking in de Azure-abonnements omgeving detecteert.

Zie de [naslag tabel met waarschuwingen](alerts-reference.md#alerts-azureresourceman)voor een lijst met waarschuwingen voor Azure Resource Manager (preview).



>[!NOTE]
> Verschillende van de voor gaande analyses worden mogelijk gemaakt door Microsoft Cloud App Security. Als u van deze analyses wilt profiteren, moet u een Cloud App Security-licentie activeren. Als u een licentie voor Cloud App Security hebt, zijn deze waarschuwingen standaard ingeschakeld. Ze uitschakelen:
>
> 1. Selecteer **beveiligings beleid**op de blade **Security Center** . Selecteer **Instellingen bewerken**voor het abonnement dat u wilt wijzigen.
> 2. Selecteer **bedreigingen detectie**.
> 3. Schakel onder **integraties inschakelen**het selectie vakje **Microsoft Cloud app Security toegang tot mijn gegevens toestaan**uit en selecteer **Opslaan**.

>[!NOTE]
>Security Center klant gegevens met betrekking tot beveiliging worden opgeslagen in dezelfde geografische regio als de resource. Als micro soft Security Center nog niet heeft geïmplementeerd in de geografische regio van de resource, worden de gegevens opgeslagen in de Verenigde Staten. Als Cloud App Security is ingeschakeld, wordt deze informatie opgeslagen in overeenstemming met de geolocatie regels van Cloud App Security. Zie [gegevens opslag voor niet-regionale Services](https://azuredatacentermap.azurewebsites.net/)voor meer informatie.

## Azure Key Vault (preview-versie)<a name="azure-keyvault"></a>

Azure Key Vault is een Cloud service die versleutelings sleutels en geheimen beveiligt, zoals certificaten, verbindings reeksen en wacht woorden. 

Azure Security Center omvat Azure-systeem eigen, geavanceerde beveiliging tegen bedreigingen voor Azure Key Vault, waarmee u een extra laag van beveiligings informatie kunt leveren. Security Center detecteert ongebruikelijke en mogelijk schadelijke pogingen om Key Vault accounts te openen of misbruik te maken. Deze beveiligingslaag biedt u de mogelijkheid bedreigingen te verhelpen zonder een beveiligings expert en zonder de nood zaak om beveiligings bewakings systemen van derden te beheren.  

Als er afwijkende activiteiten optreden, worden in Security Center waarschuwingen weer gegeven en worden ze optioneel verzonden via e-mail naar abonnements beheerders. Deze waarschuwingen omvatten de details van de verdachte activiteit en aanbevelingen voor het onderzoeken en oplossen van bedreigingen. 

> [!NOTE]
> Deze service is momenteel niet beschikbaar in azure Government en soevereine Cloud regio's.

Zie de [naslag tabel met waarschuwingen](alerts-reference.md#alerts-azurekv)voor een overzicht van de waarschuwingen voor Azure Key Vault.
