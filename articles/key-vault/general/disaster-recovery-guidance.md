---
title: Wat u moet doen als de onderbreking van een Azure-service invloed heeft op Azure Key Vault | Microsoft Docs
description: Ontdek wat u moet doen als de onderbreking van een Azure-service invloed heeft op Azure Key Vault.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/28/2020
ms.author: sudbalas
ms.openlocfilehash: 35814f34550ac7bf4ad85a96d0838df62fe63be6
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2020
ms.locfileid: "89073179"
---
# <a name="azure-key-vault-availability-and-redundancy"></a>Beschikbaarheid en redundantie in Azure Key Vault

Azure Key Vault bevat meerdere lagen van redundantie om ervoor te zorgen dat uw sleutels en geheimen beschikbaar blijven voor uw toepassing, zelfs als afzonderlijke onderdelen van de service niet werken.

De inhoud van uw sleutelkluis wordt gerepliceerd binnen de regio en naar een secundaire regio op ten minste 150 mijl afstand, maar binnen dezelfde geografische locatie voor het behoud van de hoge duurzaamheid van uw sleutels en geheimen. Raadpleeg het document [Azure-gekoppelde regio's](../../best-practices-availability-paired-regions.md) voor meer informatie over specifieke regioparen.

Als afzonderlijke onderdelen van de sleutelkluis-service niet meer werken, nemen alternatieve onderdelen in de regio het over om uw aanvraag te verwerken om ervoor te zorgen dat de functionaliteit niet wordt verminderd. U hoeft geen actie te ondernemen om dit proces te starten. Dit gebeurt automatisch en is transparant voor u.

In het zeldzame geval dat een volledige Azure-regio niet beschikbaar is, worden de aanvragen die u van Azure Key Vault in die regio hebt gemaakt, automatisch doorgestuurd (*failed over*) naar een secundaire regio. Wanneer de primaire regio weer beschikbaar is, worden de aanvragen teruggestuurd (*failed back*) naar de primaire regio. Nogmaals: u hoeft geen actie te ondernemen, omdat dit automatisch gebeurt.

Dankzij dit ontwerp met hoge beschikbaarheid heeft Azure Key Vault geen downtime nodig voor onderhoudsactiviteiten.

U moet rekening houden met enkele waarschuwingen:

* In het geval van een failover van een regio kan het enkele minuten duren voordat een failover wordt uitgevoerd voor de service. Aanvragen die gedurende deze tijd worden ingediend vóór de failover, kunnen mislukken.
* Tijdens de failover staat uw sleutelkluis in de modus alleen-lezen. Aanvragen die in deze modus worden ondersteund, zijn:
  * Certificaten vermelden
  * Certificaten ophalen
  * Geheimen vermelden
  * Geheimen ophalen
  * Sleutels vermelden
  * (Eigenschappen van) sleutels ophalen
  * Versleutelen
  * Ontsleutelen
  * Wrap
  * Unwrap
  * Verifiëren
  * Teken
  * Backup
* Tijdens de failover kunt u geen wijzigingen aanbrengen aan eigenschappen van de sleutelkluis. U kunt de configuraties en instellingen van het toegangsbeleid of de firewall niet wijzigen.
* Nadat een failback is uitgevoerd voor een failover, zijn alle aanvraagtypen (inclusief lees- *en* schrijfaanvragen) beschikbaar.

