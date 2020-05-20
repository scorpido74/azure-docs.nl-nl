---
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: dacurwin
ms.openlocfilehash: b8941bb4fa77cb0008cb7271681e972bc21d6588
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83659542"
---
U kunt nu [persoonlijke eind punten](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) gebruiken om een back-up te maken van uw gegevens op een veilige manier van servers in een virtueel netwerk naar uw Recovery Services kluis. Het persoonlijke eind punt gebruikt een IP-adres van de VNET-Address ruimte voor uw kluis. Het netwerk verkeer tussen uw resources binnen het virtuele netwerk en de kluis wordt verplaatst naar het virtuele netwerk en een privé-koppeling in het micro soft backbone-netwerk. Dit elimineert de bloot stelling van het open bare Internet. Privé-eind punten kunnen worden gebruikt voor het maken van back-ups en het herstellen van uw SQL-en SAP HANA-data bases die worden uitgevoerd in uw Azure-Vm's. Het kan ook worden gebruikt voor uw on-premises servers met behulp van de MARS-agent.

Voor Azure VM Backup is geen verbinding met Internet vereist en hiervoor zijn geen persoonlijke eind punten vereist om netwerk isolatie toe te staan.

Lees [hier](https://docs.microsoft.com/azure/backup/private-endpoints)meer over privé-eind punten voor Azure backup.
