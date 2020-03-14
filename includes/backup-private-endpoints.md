---
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: dacurwin
ms.openlocfilehash: a3bae0ce3e6ebcf64936d0ca4af4fb702e5ea404
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/12/2020
ms.locfileid: "79137021"
---
U kunt nu [persoonlijke eind punten](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) gebruiken om een back-up te maken van uw gegevens op een veilige manier van servers in een virtueel netwerk naar uw Recovery Services kluis. Het persoonlijke eind punt gebruikt een IP-adres van de VNET-Address ruimte voor uw kluis. Het netwerk verkeer tussen uw resources binnen het virtuele netwerk en de kluis wordt verplaatst naar het virtuele netwerk en een privé-koppeling in het micro soft backbone-netwerk. Dit elimineert de bloot stelling van het open bare Internet. Privé-eind punten kunnen worden gebruikt voor het maken van back-ups en het herstellen van uw SQL-en SAP HANA-data bases die worden uitgevoerd in uw Azure-Vm's. Het kan ook worden gebruikt voor uw on-premises servers met behulp van de MARS-agent.

Voor Azure VM Backup is geen verbinding met Internet vereist en hiervoor zijn geen persoonlijke eind punten vereist om netwerk isolatie toe te staan.

>[!NOTE]
> Deze functie is momenteel in eerste gebruik. Vul [deze enquête](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUQk5EQ1QxRzVOWDNDS1Y1Q0xLTkdLQ0U0RC4u) in en stuur ons een e-mail naar azbackupnetsec@microsoft.com als u geïnteresseerd bent in het gebruik van privé-eind punten voor Azure backup. De mogelijkheid om deze functie te gebruiken, is onderworpen aan de goed keuring van de Azure Backup service.
