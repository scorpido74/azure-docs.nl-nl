---
title: Omgekeerde opzoekzones configureren voor een SMTP-bannercontrole
titlesuffix: Azure Virtual Network
description: Beschrijft hoe u omgekeerde opzoekzones configureert voor een SMTP-bannercontrole in Azure
services: virtual-network
documentationcenter: virtual-network
author: genlin
manager: dcscontentpm
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 6c37ba61005c9936e6421d06369d1f52b93ac264
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201693"
---
# <a name="configure-reverse-lookup-zones-for-an-smtp-banner-check"></a>Omgekeerde opzoekzones configureren voor een SMTP-bannercontrole

In dit artikel wordt beschreven hoe u een omgekeerde zone in Azure DNS gebruikt en een Reverse DNS-record (PTR) maakt voor SMTP-bannercontrole.

## <a name="symptom"></a>Symptoom

Als u een SMTP-server host in Microsoft Azure, ontvangt u mogelijk het volgende foutbericht wanneer u een bericht verzendt of ontvangt van externe e-mailservers:

**554: Geen PTR-record**

## <a name="solution"></a>Oplossing

Voor een virtueel IP-adres in Azure worden de omgekeerde records gemaakt in domeinzones die eigendom zijn van Microsoft, niet in aangepaste domeinzones.

Als u PTR-records wilt configureren in zones die eigendom zijn van Microsoft, gebruikt u de eigenschap -ReverseFqdn op de PublicIpAddress-bron. Zie [Reverse DNS configureren voor services die worden gehost in Azure](../dns/dns-reverse-dns-for-azure-services.md)voor meer informatie.

Wanneer u de PTR-records configureert, moet u ervoor zorgen dat het IP-adres en de omgekeerde FQDN eigendom zijn van het abonnement. Als u een omgekeerde FQDN probeert in te stellen die niet tot het abonnement behoort, ontvangt u het volgende foutbericht:

    Set-AzPublicIpAddress : ReverseFqdn mail.contoso.com that PublicIPAddress ip01 is trying to use does not belong to subscription <Subscription ID>. One of the following conditions need to be met to establish ownership:
                        
    1) ReverseFqdn komt overeen met fqdn van een openbare ip-bron onder het abonnement;
    2) ReverseFqdn lost de fqdn (via CName-recordsketen) op van een openbare ip-bron onder het abonnement;
    3) Het besluit tot het ip-adres (via CName en A records chain) van een statische openbare ip-bron onder het abonnement.

Als u uw SMTP-banner handmatig wijzigt om aan onze standaard omgekeerde FQDN te voldoen, kan de externe e-mailserver nog steeds mislukken omdat de SMTP-bannerhost mogelijk overeenkomt met de MX-record voor het domein.
