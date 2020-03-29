---
title: bestand opnemen
description: bestand opnemen
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/19/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: cdcbe993bd1100b2060a1f8d38eb82ac97121c0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74851622"
---
Er worden kosten in rekening gebracht voor Azure Storage op basis van uw opslagaccountgebruik. Alle objecten in een opslagaccount worden samen gefactureerd als een groep. 

De opslagkosten worden berekend op basis van de volgende factoren: 

* **Regio** verwijst naar de geografische regio waarin uw account is gebaseerd.
* **Accounttype** verwijst naar het type opslagaccount dat u gebruikt. 
* **De toegangslaag** verwijst naar het gegevensgebruikspatroon dat u hebt opgegeven voor uw v2- of Blob-opslagaccount voor algemene doeleinden.
* **Opslagcapaciteit verwijst** naar hoeveel van uw opslagaccounttoewijzing u gebruikt om gegevens op te slaan.
* **Replicatie** bepaalt hoeveel kopieën van uw gegevens tegelijk worden bewaard en op welke locaties.
* **Transacties** verwijzen naar alle lees- en schrijfbewerkingen naar Azure Storage.
* **Gegevensuitgang** verwijst naar gegevens die worden overgedragen vanuit een Azure-gebied. Wanneer de gegevens in uw opslagaccount worden geopend door een toepassing die niet in dezelfde regio wordt uitgevoerd, worden er kosten in rekening gebracht voor gegevens die worden uitgevoerd. Zie [Wat is een Azure-brongroep voor](https://docs.microsoft.com/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group)informatie over het gebruik van resourcegroepen om uw gegevens en services in dezelfde regio te groeperen om de kosten voor uitgaande bestanden te beperken. 

Op de pagina [Prijzen voor Azure Storage](https://azure.microsoft.com/pricing/details/storage/) vindt u gedetailleerde prijsinformatie op basis van accounttype, opslagcapaciteit, replicatie en transacties. In [Prijsinformatie over gegevensoverdracht](https://azure.microsoft.com/pricing/details/data-transfers/) vindt u gedetailleerde informatie over de prijzen voor uitgaande gegevens. Met de [Prijscalculator van Azure Storage](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) kunt u een schatting maken van uw kosten.

