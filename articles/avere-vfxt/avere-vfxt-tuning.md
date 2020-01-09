---
title: AVERE vFXT cluster tuning-Azure
description: Overzicht van aangepaste instellingen voor het optimaliseren van de prestaties in avere vFXT voor Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: rohogue
ms.openlocfilehash: 8e25b3408482d9be9cb870df338ba0e53af52507
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75414321"
---
# <a name="cluster-tuning"></a>Cluster afstemmen

De meeste vFXT-clusters kunnen profiteren van aangepaste instellingen voor prestaties. Deze instellingen helpen het cluster optimaal te laten werken met uw specifieke werk stroom, gegevensset en hulpprogram ma's.

Deze aanpassing moet worden uitgevoerd naast een ondersteunings medewerker, omdat hiervoor doorgaans functies moeten worden geconfigureerd die niet beschikbaar zijn via het configuratie scherm van AVERE.

In deze sectie wordt een deel van de aangepaste tuning beschreven die kan worden uitgevoerd.

<!-- 
[ xxx keep or not? \/ research this xxx ]

> [!TIP]
> The VDBench utility can be helpful in generating I/O workloads to test a vFXT cluster. Read [Measuring vFXT Performance](vdbench.md) to learn more.

-->

## <a name="general-optimizations"></a>Algemene optimalisaties

Deze wijzigingen kunnen worden aanbevolen op basis van de kenmerken van de gegevensset of de stijl van de werk stroom.

* Als de werk belasting write-Heavy is, verg root u de grootte van de schrijf cache van de standaard waarde van 20%.
* Als de gegevensset veel kleine bestanden omvat, verhoogt u de limiet voor het aantal bestanden van de cluster cache.
* Als voor het werk het kopiëren of verplaatsen van gegevens tussen twee opslag plaatsen is vereist, past u het aantal threads aan dat wordt gebruikt voor het verplaatsen van gegevens:
  * Als u de snelheid wilt verhogen, kunt u het aantal gebruikte parallelle threads verhogen.
  * Als het opslag volume van de back-end overbelast wordt, moet u mogelijk het aantal gebruikte parallelle threads verlagen.
* Als het cluster gegevens in de cache opslaat voor een kern bestand die gebruikmaakt van NFSv4 Acl's, schakelt u in cache opslaan in de toegangs modus in om bestands autorisatie voor bepaalde clients te stroom lijnen.

## <a name="cloud-nas-or-cloud-gateway-optimizations"></a>Cloud-NAS-of Cloud gateway-optimalisaties

Als u wilt profiteren van hogere gegevens snelheden tussen het vFXT-cluster en de Cloud opslag in een Cloud-NAS of Gateway scenario (waarbij het vFXT-cluster toegang biedt tot een Cloud container), is het raadzaam om de instellingen te wijzigen, zoals deze voor meer informatie gegevens naar het opslag volume agressief pushen vanuit de cache:

* Verhoog het aantal TCP-verbindingen tussen het cluster en de opslag container

## <a name="cloud-bursting-or-hybrid-wan-optimizations"></a>Cloud bursting of hybride WAN-optimalisaties

In een scenario met een hoofdletter gevoelig scenario of een WAN-optimalisatie voor hybride opslag (waarbij het vFXT-cluster integratie biedt tussen de Cloud en on-premises hardwarematige opslag), kunnen deze wijzigingen nuttig zijn:

* Verhoog het aantal TCP-verbindingen dat is toegestaan tussen het cluster en de kern bestand
* Schakel de WAN-optimalisatie-instelling voor de externe kern bestand in (deze instelling kan worden gebruikt voor een externe on-premises bestand of een Cloud kern bestand in een andere Azure-regio.)
* De grootte van de TCP-socket buffer verhogen (afhankelijk van de behoeften van de werk belasting en de prestaties)
* Schakel de instelling ' altijd door sturen ' in om overbodige bestanden in cache te verminderen (afhankelijk van de behoeften van de werk belasting en de prestaties)

## <a name="help-optimizing-your-avere-vfxt-for-azure"></a>Help uw avere vFXT voor Azure te optimaliseren

Gebruik de procedure die wordt beschreven in [hulp vragen aan uw systeem](avere-vfxt-open-ticket.md) om contact op te nemen met de ondersteunings medewerkers over deze optimalisaties.
