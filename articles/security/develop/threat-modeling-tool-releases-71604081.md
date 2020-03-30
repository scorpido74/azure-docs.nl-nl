---
title: Microsoft Threat Modeling Tool release 4/9/2019
titleSuffix: Azure
description: Het documenteren van de releasenotes voor het hulpprogramma voor bedreigingsmodellering
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 59d385ba7de5bf7bceae4dc8ddadbca813046094
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78269716"
---
# <a name="threat-modeling-tool-update-release-71604081---492019"></a>Threat Modeling Tool update release 7.1.60408.1 - 4/9/2019

Versie 7.1.60408.1 van de Microsoft Threat Modeling Tool (TMT) is uitgebracht op 9 april 2019 en bevat de volgende wijzigingen:

- Nieuwe stencils voor Azure Key Vault en Azure Traffic Manager
- TMT-versienummer wordt nu weergegeven op het startscherm
- Ondersteuningskoppelingen zijn bijgewerkt
- Opgeloste fouten

## <a name="feature-changes"></a>Functiewijzigingen

### <a name="new-stencils-for-azure-key-vault-and-azure-traffic-manager"></a>Nieuwe stencils voor Azure Key Vault en Azure Traffic Manager

![Azure Key Vault Stencil](./media/threat-modeling-tool-releases-71604081/tmt_keyvault_trafficmanager.PNG)

Nieuwe stencils en bedreigingen voor Azure Key Vault en Azure Traffic Manager zijn toegevoegd aan de Azure-stencilset. Bij het openen van modellen op basis van de Azure-stencilset wordt gebruikers gevraagd de sjabloon bij te werken die aan het model is gekoppeld. Het bijwerken van een model op basis van de Azure-stencilset kan ook handmatig worden gestart met de opdracht Sjabloon toepassen in het menu 'Bestand' en het nieuwste Azure Cloud Services.tb7-bestand opnieuw toepassen.

### <a name="tmt-version-number-is-now-shown-on-the-home-screen"></a>TMT-versienummer wordt nu weergegeven op het startscherm

De clientversie van de Threat Modeling Tool wordt nu weergegeven op het startscherm van de toepassing van voor het gemak van toegang.

![Azure Key Vault Stencil](./media/threat-modeling-tool-releases-71604081/tmt_version.PNG)

### <a name="support-links-have-been-updated"></a>Ondersteuningskoppelingen zijn bijgewerkt

Alle ondersteuningskoppelingen in de tool zijn [tmtextsupport@microsoft.com](mailto:tmtextsupport@microsoft.com) bijgewerkt om gebruikers te leiden naar in plaats van een MSDN-forum.

## <a name="system-requirements"></a>Systeemvereisten

- Ondersteunde besturingssystemen
  - [Microsoft Windows 10 Anniversary Update](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) of hoger
- .NET-versie vereist
  - [.Net 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) of hoger
- Aanvullende vereisten
  - Er is een internetverbinding vereist om updates van de tool en sjablonen te ontvangen.

## <a name="documentation-and-feedback"></a>Documentatie en feedback

- Documentatie voor de Threat Modeling Tool bevindt zich op [docs.microsoft.com](threat-modeling-tool.md)en bevat informatie [over het gebruik van de tool.](threat-modeling-tool-getting-started.md)

## <a name="next-steps"></a>Volgende stappen

Download de nieuwste versie van de [Microsoft Threat Modeling Tool.](https://aka.ms/threatmodelingtool)
