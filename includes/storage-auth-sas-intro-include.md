---
title: bestand opnemen
description: bestand opnemen
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/20/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: aec1faa4de1149f08fb6fbc1cc5bf3aa2ab6becd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75371770"
---
Met een sas (shared access signature) u beperkte toegang verlenen tot containers en blobs in uw opslagaccount. Wanneer u een SAS maakt, geeft u de beperkingen op, waaronder welke Azure Storage-resources een client mag openen, welke machtigingen ze op deze resources hebben en hoe lang de SAS geldig is.

Elke SAS is ondertekend met een sleutel. U een SAS op twee manieren ondertekenen:

- Met een sleutel die is gemaakt met Azure Active Directory-referenties (Azure AD). Een SAS die is ondertekend met Azure AD-referenties is een *SAS voor gebruikersdelegatie.*
- Met de opslagaccountsleutel. Zowel een *service SAS* als een *account SAS* worden ondertekend met de opslagaccountsleutel.

Een gebruikersdelegatie SAS biedt superieure beveiliging van een SAS die is ondertekend met de opslagaccountsleutel. Microsoft raadt aan om indien mogelijk een SAS voor gebruikersdelegatie te gebruiken. Zie [Beperkte toegang tot gegevens met gedeelde toegangshandtekeningen (SAS) verlenen](../articles/storage/common/storage-sas-overview.md)voor meer informatie.
