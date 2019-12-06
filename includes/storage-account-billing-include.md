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
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851622"
---
U wordt gefactureerd voor Azure Storage op basis van het gebruik van uw opslag account. Alle objecten in een opslagaccount worden samen gefactureerd als een groep. 

Opslag kosten worden berekend op basis van de volgende factoren: 

* **Regio** verwijst naar de geografische regio waarin uw account is gebaseerd.
* Het **account type** verwijst naar het type opslag account dat u gebruikt. 
* De **toegangs laag** verwijst naar het gegevens gebruik patroon dat u hebt opgegeven voor het opslag account voor algemeen gebruik v2 of Blob Storage.
* Opslag **capaciteit** verwijst naar het gedeelte van het opslag account dat u gebruikt om gegevens op te slaan.
* **Replicatie** bepaalt hoeveel kopieën van uw gegevens tegelijk worden bewaard en op welke locaties.
* **Trans acties** verwijzen naar alle Lees-en schrijf bewerkingen die worden Azure Storage.
* **Gegevens** uitvoer verwijst naar gegevens die vanuit een Azure-regio worden overgedragen. Wanneer de gegevens in uw opslag account worden geopend door een toepassing die niet wordt uitgevoerd in dezelfde regio, worden er kosten in rekening gebracht voor het uitvallen van gegevens. Zie [Wat is een Azure-resource groep?](https://docs.microsoft.com/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group)voor informatie over het gebruik van resource groepen om uw gegevens en services in dezelfde regio te groeperen om de uitstaande kosten te beperken. 

Op de pagina [Prijzen voor Azure Storage](https://azure.microsoft.com/pricing/details/storage/) vindt u gedetailleerde prijsinformatie op basis van accounttype, opslagcapaciteit, replicatie en transacties. In [Prijsinformatie over gegevensoverdracht](https://azure.microsoft.com/pricing/details/data-transfers/) vindt u gedetailleerde informatie over de prijzen voor uitgaande gegevens. Met de [Prijscalculator van Azure Storage](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) kunt u een schatting maken van uw kosten.

