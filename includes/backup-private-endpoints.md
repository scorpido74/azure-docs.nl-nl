---
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: dacurwin
ms.openlocfilehash: 024f70a21b36bffa662ba6f568adc8efbd0a909d
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81539391"
---
U nu [privéeindpunten](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) gebruiken om veilig een back-up van uw gegevens te maken, van servers in een virtueel netwerk naar uw Vault recovery services. Het privéeindpunt maakt gebruik van een IP van de VNET-adresruimte voor uw kluis. Het netwerkverkeer tussen uw bronnen binnen het virtuele netwerk en de kluis reist via uw virtuele netwerk en een privékoppeling op het Microsoft-backbonenetwerk. Dit elimineert blootstelling van het openbare internet. Private Endpoints kunnen worden gebruikt voor het maken van back-ups en het herstellen van uw SQL- en SAP HANA-databases die in uw Azure VM's worden uitgevoerd. Het kan ook worden gebruikt voor uw on-premises servers met behulp van de MARS-agent.

Azure VM-back-up vereist geen internetverbinding en vereist daarom geen privéeindpunten om netwerkisolatie toe te staan.

>[!NOTE]
> Deze functie is momenteel in beperkte beschikbaarheid en is beschikbaar in West Central US, South Central US, West US 2 en East US (beschikbaarheid in andere Azure-regio's om te volgen). Vul [deze enquête](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUQk5EQ1QxRzVOWDNDS1Y1Q0xLTkdLQ0U0RC4u) in azbackupnetsec@microsoft.com en stuur ons een e-mail als u geïnteresseerd bent in het gebruik van privéeindpunten voor Azure Backup. De mogelijkheid om deze functie te gebruiken is onder voorbehoud van goedkeuring van de Azure Backup-service.
