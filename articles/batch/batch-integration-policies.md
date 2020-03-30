---
title: Integratie met Azure-beleid - Azure Batch | Microsoft Documenten
description: ''
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 28998df4-8693-431d-b6ad-974c2f8db5fb
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/24/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 9a306457f838fc79d12be3217d96cc8fb25c9c1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77618371"
---
# <a name="integration-with-azure-policy"></a>Integratie met Azure-beleid

Azure Policy is een service in Azure die u gebruikt om beleidsregels te maken, toe te wijzen en te beheren die regels afdwingen voor uw resources om ervoor te zorgen dat deze resources voldoen aan uw bedrijfsstandaarden en serviceniveauovereenkomsten. Azure Policy evalueert uw resources op niet-naleving van het beleid dat u toewijst. 

Azure Batch heeft twee ingebouwde extensies waarmee u de naleving van het beleid beheren. 

|**Naam**...|   **Beschrijving**|**Effect(en)**|  **Versie**|    **Bron**
|----------------|----------|----------|----------------|---------------|
|Diagnostische logboeken in Batch-accounts moeten zijn ingeschakeld|   Controle inschakelen van diagnostische logboeken. Hiermee u activiteitspaden opnieuw maken om te gebruiken voor onderzoeksdoeleinden; wanneer zich een beveiligingsincident voordoet of wanneer uw netwerk wordt gecompromitteerd|AuditIfNotExists, Uitgeschakeld|  2.0.0|  GitHub|
|Metrische waarschuwingsregels moeten worden geconfigureerd op Batch-accounts| Controleconfiguratie van metrische waarschuwingsregels voor batchaccount om de vereiste statistiek in te schakelen|   AuditIfNotExists, Uitgeschakeld| 1.0.0|  GitHub|

Beleidsdefinities beschrijven de voorwaarden waaraan moet worden voldaan. Een voorwaarde vergelijkt de eigenschap resource met een vereiste waarde. Resourceeigenschapvelden zijn toegankelijk met vooraf gedefinieerde aliassen. U gebruikt eigenschapsaliassen om toegang te krijgen tot specifieke eigenschappen voor een resourcetype. Met aliassen u beperken welke waarden of voorwaarden zijn toegestaan voor een eigenschap op een resource. Elke alias wordt toegewezen aan paden in verschillende API-versies voor een bepaald resourcetype. Tijdens beleidsevaluatie krijgt de beleidsengine het eigenschappenpad voor die API-versie.

De resources die batch vereist zijn: account, compute node, pool, job en taak. U gebruikt dus eigenschapaliassen om toegang te krijgen tot specifieke eigenschappen voor deze resources. Meer informatie over [aliassen](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

Gebruik de Azure-beleidsextensie voor Visual Studio Code om ervoor te zorgen dat u de huidige aliassen kent en uw resources en beleid bekijkt. Het kan worden geïnstalleerd op alle platforms die worden ondersteund door Visual Studio Code. Deze ondersteuning omvat Windows, Linux en macOS. Zie [installatierichtlijnen](https://docs.microsoft.com/azure/governance/policy/how-to/extension-for-vscode).



