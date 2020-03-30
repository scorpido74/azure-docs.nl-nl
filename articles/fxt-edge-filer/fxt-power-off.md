---
title: Een Microsoft Azure FXT Edge Filer-eenheid afsluiten
description: Procedures voor het opstarten en veilig afsluiten van een Azure FXT Edge Filer-knooppunt
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: rohogue
ms.openlocfilehash: 91445c4219abaa67385105f52515f52c87e2cf06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72255997"
---
# <a name="how-to-safely-power-off-azure-fxt-edge-filer-hardware"></a>Azure FXT Edge Filer-hardware veilig uitschakelen

Hoewel u de fysieke aan/uit-knop gebruiken om een individueel knooppunt in te schakelen, moet u het apparaat onder normale omstandigheden niet gebruiken om het apparaat uit te schakelen.

Nadat een Azure FXT Edge Filer-knooppunt in gebruik is als onderdeel van een cluster, moet u de software van het clusterconfiguratiepaneel gebruiken om de hardware af te sluiten. 

> [!NOTE] 
> Gebruik altijd de Software voor het Configuratiescherm om een Azure FXT Edge Filer af te sluiten om mogelijk gegevensverlies of beschadiging te voorkomen. Gebruik de fysieke aan/uit-knop niet voor afsluiten, tenzij u daartoe wordt geïnstrueerd door de klantenservice en ondersteuning van Microsoft.
> 
> Los in een elektrisch noodgeval de stroomkabels los of gebruik het elektriciteitsloskoppelmechanisme van uw datacenter.

## <a name="shut-down-a-node-from-the-control-panel"></a>Een knooppunt afsluiten vanuit het Configuratiescherm

Volg deze instructies om een Azure FXT Edge Filer-knooppunt veilig uit te schakelen:

1. Meld u aan bij het configuratiescherm van het cluster. (Routebeschrijving in [De pagina's Instellingen openen)](fxt-cluster-create.md#open-the-settings-pages)
1. Klik op het tabblad **Instellingen** en laad vervolgens de pagina **Cluster** > **FXT-knooppunten.**
1. Zoek in de lijst met clusterknooppunten de lijst met knooppunten die u wilt afsluiten. Klik op de knop **Aan-uit** in de kolom **Handelingen.** 
1. Wacht even. Het knooppunt wordt uitgeschakeld en zichzelf uitgeschakeld.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de status-LED's en andere indicatoren in [de hardwarestatus van Monitor Azure FXT Edge Filer](fxt-monitor.md).
* Lees meer over Azure FXT Edge Filer-voedingen in [Connect-stroomkabels](fxt-network-power.md#connect-power-cables).
