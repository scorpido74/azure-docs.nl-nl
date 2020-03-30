---
title: Firewall-instellingen voor Azure Lab Services
description: Meer informatie over het bepalen van het openbare IP-adres en het bereik van virtuele apparaten in een lab, zodat informatie kan worden toegevoegd aan firewallregels.
author: emaher
ms.author: enewman
ms.date: 02/14/2020
ms.topic: article
ms.service: lab-services
ms.openlocfilehash: fbd45af0c9b94f04fdaad9d9b5c8214a91a8db91
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77443449"
---
# <a name="firewall-settings-for-azure-lab-services"></a>Firewall-instellingen voor Azure Lab Services

Elke organisatie of school zal zijn eigen netwerk opzetten op een manier die het beste bij hun behoeften past.  Soms omvat dat het instellen van firewallregels die RdP-verbindingen (Remote Desktop Protocol) of Secure Shell (ssh) blokkeren met machines buiten hun eigen netwerk.  Omdat Azure Lab Services in de openbare cloud wordt uitgevoerd, is er misschien een extra configuratie nodig om studenten toegang te geven tot hun VM wanneer ze verbinding maken vanuit het campusnetwerk.

Elk lab maakt gebruik van één openbaar IP-adres en meerdere poorten.  Alle VM's, zowel de sjabloon-VM als de VM's voor studenten, gebruiken dit openbare IP-adres.  Het openbare IP-adres zal niet veranderen voor de levensduur van het lab.  Elke vm heeft echter een ander poortnummer.  De poortnummers variëren van 49152 tot 65535.  De combinatie van openbaar IP-adres en poortnummer wordt gebruikt om cursusleiders en studenten te verbinden met de juiste VM.  Dit artikel gaat over het vinden van het specifieke openbare IP-adres dat door een lab wordt gebruikt.  Die informatie kan worden gebruikt om inkomende en uitgaande firewallregels bij te werken, zodat studenten toegang hebben tot hun VM's.

>[!IMPORTANT]
>Elk lab heeft een ander ip-adres.

## <a name="find-public-ip-for-a-lab"></a>Openbare IP voor een lab zoeken

De openbare IP-adressen voor elk lab worden vermeld op de pagina **Alle labs** van het Lab Services lab-account.  Voor een routebeschrijving hoe u de pagina **Alle labs kunt** vinden, ziet u hoe u labs in een [labaccount beheren.](how-to-manage-lab-accounts.md#view-and-manage-labs-in-the-lab-account)  

> [!div class="mx-imgBorder"]
> ![Alle labs pagina](../media/how-to-configure-firewall-settings/all-labs-properties.png)

>[!NOTE]
>U ziet het openbare IP-adres niet als de sjabloonmachine voor uw lab nog niet is gepubliceerd.

## <a name="conclusion"></a>Conclusie

Nu weten we het openbare IP-adres voor het lab.  Inkomende en uitgaande regels kunnen worden gemaakt voor de firewall van de organisatie voor het openbare IP-adres en het poortbereik 49152-65535.  Zodra de regels zijn bijgewerkt, kunnen studenten toegang krijgen tot hun VM's zonder dat de netwerkfirewall de toegang blokkeert.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

- [Toestaan dat de labmaker de lablocatie kiest](allow-lab-creator-pick-lab-location.md)
- [Verbind het netwerk van uw lab met een virtueel netwerk van collega's](how-to-connect-peer-virtual-network.md)
- [Een gedeelde afbeeldingsgalerie aan een lab koppelen](how-to-attach-detach-shared-image-gallery.md)
- [Een gebruiker toevoegen als eigenaar van een lab](how-to-add-user-lab-owner.md)
- [Firewall-instellingen voor een lab weergeven](how-to-configure-firewall-settings.md)
- [Andere instellingen voor een lab configureren](how-to-configure-lab-accounts.md)
