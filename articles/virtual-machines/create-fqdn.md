---
title: FQDN maken voor een virtuele machine in de Azure Portal
description: Meer informatie over het maken van een FQDN-naam (Fully Qualified Domain Name) voor een virtuele machine in de Azure Portal.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/03/2020
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 08d5e20bf0755a71e70a0e446cf96d33bb42ad59
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93351883"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>Een Fully Qualified Domain Name maken in de Azure Portal voor een Linux-VM

Wanneer u een virtuele machine (VM) maakt in de [Azure Portal](https://portal.azure.com), wordt er automatisch een open bare IP-resource voor de virtuele machine gemaakt. U gebruikt dit IP-adres om op afstand toegang te krijgen tot de virtuele machine. Hoewel de portal geen [Fully Qualified Domain name](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)of FQDN maakt, kunt u één keer toevoegen wanneer de virtuele machine is gemaakt. In dit artikel worden de stappen beschreven voor het maken van een DNS-naam of FQDN. 

## <a name="create-a-fqdn"></a>Een FQDN maken
In dit artikel wordt ervan uitgegaan dat u al een virtuele machine hebt gemaakt. Als dat nodig is, kunt u een virtuele [Linux](./linux/quick-create-portal.md) -of [Windows](./windows/quick-create-portal.md) -machine maken in de portal. Volg deze stappen als uw virtuele machine actief is:


1. Selecteer uw virtuele machine in de portal. Selecteer onder **DNS-naam** **configureren**.
2. Geef de DNS-naam op en selecteer vervolgens **Opslaan** boven aan de pagina.
3. Als u wilt terugkeren naar de Blade VM-overzicht, sluit u de Blade **configuratie** door de **X** in de rechter bovenhoek te selecteren. 
4. Controleer of de *DNS-naam* nu correct wordt weer gegeven.
   



## <a name="next-steps"></a>Volgende stappen
Nu uw VM een open bare IP-en DNS-naam heeft, kunt u algemene toepassings raamwerken of-services implementeren, zoals nginx, MongoDB en docker.

U kunt ook meer lezen over het [gebruik van Resource Manager](../azure-resource-manager/management/overview.md) voor tips over het bouwen van uw Azure-implementaties.

