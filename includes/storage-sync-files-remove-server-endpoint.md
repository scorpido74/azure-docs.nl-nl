---
title: bestand opnemen
description: bestand opnemen
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/31/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: be3ce42ccdb9bedd02b8dead2426ac629fa12ef2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "72391618"
---
Nee: het verwijderen van een servereindpunt is niet zoals het opnieuw opstarten van een server! Het verwijderen en opnieuw maken van het servereindpunt is bijna nooit een geschikte oplossing voor het oplossen van problemen met synchronisatie, cloudtiering of andere aspecten van Azure File Sync. Het verwijderen van een servereindpunt is een destructieve bewerking. Dit kan leiden tot gegevensverlies in het geval dat er gelaagde bestanden buiten de naamruimte van het servereindpunt bestaan. Zie [Waarom bestaan er gelaagde bestanden buiten de naamruimte van het servereindpunt](../articles/storage/files/storage-files-faq.md#afs-tiered-files-out-of-endpoint) voor meer informatie. Of het kan leiden tot ontoegankelijke bestanden voor gelaagde bestanden die binnen de naamruimte van het servereindpunt bestaan. Deze problemen worden niet opgelost wanneer het eindpunt van de server opnieuw wordt gemaakt. Er kunnen gelaagde bestanden bestaan in de naamruimte van uw servereindpunt, zelfs als er nooit cloudlagen zijn ingeschakeld. Daarom u aanbevelen het eindpunt van de server niet te verwijderen, tenzij u azure file sync met deze specifieke map wilt gebruiken of expliciet zijn geïnstrueerd om dit te doen door een Microsoft-technicus. Zie [Een servereindpunt verwijderen](../articles/storage/files/storage-sync-files-server-endpoint.md#remove-a-server-endpoint)voor meer informatie over het verwijderen van servereindpunten.    
