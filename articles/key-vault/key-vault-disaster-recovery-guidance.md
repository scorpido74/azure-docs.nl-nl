---
title: Wat te doen in het geval van een onderbreking van de Azure-service die van invloed is op de Azure Key Vault-Azure Key Vault | Microsoft Docs
description: Meer informatie over wat u moet doen in het geval van een onderbreking van de Azure-service die van invloed is op Azure Key Vault.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 530ae2b795b4d94802e9f0d3420f7b3af86936ad
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78184635"
---
# <a name="azure-key-vault-availability-and-redundancy"></a>Beschik baarheid en redundantie Azure Key Vault

Azure Key Vault bevat meerdere lagen van redundantie om ervoor te zorgen dat uw sleutels en geheimen beschikbaar blijven voor uw toepassing, zelfs als afzonderlijke onderdelen van de service niet werken.

De inhoud van uw sleutel kluis wordt gerepliceerd binnen de regio en naar een secundaire regio die ten minste 150 mijlen bevindt, maar binnen dezelfde geografie. Dit houdt een hoge duurzaamheid van uw sleutels en geheimen bij. Zie het document met [gekoppelde regio's in azure](../best-practices-availability-paired-regions.md) voor meer informatie over specifieke regio paren.

Als afzonderlijke onderdelen van de Key kluis-service mislukken, nemen alternatieve onderdelen in de regio deel aan om uw aanvraag te leveren om ervoor te zorgen dat de functionaliteit niet wordt verminderd. U hoeft geen actie te ondernemen om dit te activeren. Dit gebeurt automatisch en is transparant voor u.

In zeldzame gevallen dat een volledige Azure-regio niet beschikbaar is, worden de aanvragen die u van Azure Key Vault in die regio hebt gemaakt, automatisch doorgestuurdnaar een secundaire regio. Wanneer de primaire regio weer beschikbaar is, worden aanvragen*teruggestuurd naar*de primaire regio. U hoeft geen actie te ondernemen, omdat dit automatisch gebeurt.

Dankzij dit ontwerp met hoge Beschik baarheid heeft Azure Key Vault geen uitval tijd nodig voor onderhouds activiteiten.

Er zijn enkele voor behoud van wat u moet weten:

* In het geval van een failover van een regio kan het enkele minuten duren voordat een failover wordt uitgevoerd voor de service. Aanvragen die gedurende deze tijd zijn gemaakt, kunnen mislukken totdat de failover is voltooid.
* Nadat een failover is voltooid, wordt de sleutel kluis in de modus alleen-lezen. Aanvragen die in deze modus worden ondersteund, zijn:
  * Sleutel kluizen weer geven
  * Eigenschappen van sleutel kluizen ophalen
  * Geheimen vermelden
  * Geheimen ophalen
  * Lijst met sleutels
  * Get (eigenschappen van) sleutels
  * Versleutelen
  * Ontsleutelen
  * Verpakt
  * Uitpakken
  * Verifiëren
  * Aanmelden
  * Back-up maken
* Nadat een failover is mislukt, zijn alle aanvraag typen (inclusief Lees- *en* schrijf aanvragen) beschikbaar.

