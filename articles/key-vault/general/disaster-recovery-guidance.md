---
title: Wat te doen in het geval van een Azure-serviceonderbreking die van invloed is op Azure Key Vault - Azure Key Vault | Microsoft Documenten
description: Meer informatie over wat u moet doen in het geval van een Azure-serviceonderbreking die van invloed is op Azure Key Vault.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 96929dcbe3d51589b0c3c0df89671dadb20e37cc
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422947"
---
# <a name="azure-key-vault-availability-and-redundancy"></a>Beschikbaarheid en redundantie van Azure Key Vault

Azure Key Vault beschikt over meerdere redundantielagen om ervoor te zorgen dat uw sleutels en geheimen beschikbaar blijven voor uw toepassing, zelfs als afzonderlijke onderdelen van de service mislukken.

De inhoud van uw sleutelkluis wordt gerepliceerd binnen de regio en naar een secundaire regio op ten minste 150 mijl afstand, maar binnen dezelfde geografie. Dit behoudt een hoge duurzaamheid van uw sleutels en geheimen. Zie het document [met gekoppelde azure-regio's](../../best-practices-availability-paired-regions.md) voor meer informatie over specifieke regioparen.

Als afzonderlijke onderdelen binnen de key vault-service mislukken, stappen alternatieve componenten binnen de regio in om uw verzoek te dienen om ervoor te zorgen dat er geen verslechtering van de functionaliteit is. U hoeft geen actie te ondernemen om dit te activeren. Het gebeurt automatisch en zal transparant zijn voor jou.

In het zeldzame geval dat een hele Azure-regio niet beschikbaar is, worden de aanvragen die u maakt voor Azure Key Vault in die regio automatisch doorgestuurd *(mislukt)* naar een secundaire regio. Wanneer de primaire regio weer beschikbaar is, worden aanvragen terugdoorgestuurd *(mislukt)* naar de primaire regio. Nogmaals, u hoeft geen actie te ondernemen omdat dit automatisch gebeurt.

Door dit ontwerp met hoge beschikbaarheid vereist Azure Key Vault geen downtime voor onderhoudsactiviteiten.

Er zijn een paar kanttekeningen bewust te zijn van:

* In het geval van een failover in de regio kan het enkele minuten duren voordat de service is mislukt. Aanvragen die in deze periode worden uitgevoerd, kunnen mislukken totdat de failover is voltooid.
* Nadat een failover is voltooid, bevindt uw sleutelkluis zich in de alleen-lezen modus. Aanvragen die in deze modus worden ondersteund, zijn:
  * Lijstsleutelkluizen
  * Eigenschappen van sleutelkluizen ophalen
  * Lijstgeheimen
  * Krijg geheimen
  * Lijsttoetsen
  * Ophalen (eigenschappen van) sleutels
  * Versleutelen
  * Ontsleutelen
  * Wrap
  * Uitpakken
  * Verifiëren
  * Teken
  * Backup
* Nadat een failover is mislukt, zijn alle aanvraagtypen (inclusief *lees- en* schrijfverzoeken) beschikbaar.

