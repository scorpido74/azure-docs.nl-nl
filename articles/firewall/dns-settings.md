---
title: DNS-instellingen Azure Firewall (preview-versie)
description: U kunt Azure Firewall configureren met DNS-server-en DNS-proxy-instellingen.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 9c7182205df8d276bece4758d6d4430864883d32
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85610639"
---
# <a name="azure-firewall-dns-settings-preview"></a>DNS-instellingen Azure Firewall (preview-versie)

> [!IMPORTANT]
> Azure Firewall zijn DNS-instellingen momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

U kunt een aangepaste DNS-server configureren en DNS-proxy inschakelen voor Azure Firewall. U kunt deze instellingen configureren wanneer u de firewall of hoger op de pagina **DNS-instellingen** implementeert.

## <a name="dns-servers"></a>DNS-servers

Een DNS-server onderhoudt en verhelpt domein namen naar IP-adressen. Azure Firewall maakt standaard gebruik van Azure DNS voor naam omzetting. Met de **DNS-server** instelling kunt u uw eigen DNS-servers configureren voor Azure firewall naam omzetting. U kunt één of meerdere servers configureren.

### <a name="configure-custom-dns-servers-preview"></a>Aangepaste DNS-servers configureren (preview-versie)

1. Selecteer onder Azure Firewall **instellingen**de optie **DNS-instellingen**.
2. Onder **DNS-servers**kunt u bestaande DNS-servers die eerder zijn opgegeven in uw Virtual Network typen of toevoegen.
3. Selecteer **Opslaan**.
4. De firewall stuurt nu DNS-verkeer naar de opgegeven DNS-server (s) voor naam omzetting.

:::image type="content" source="media/dns-settings/dns-servers.png" alt-text="DNS-servers":::

## <a name="dns-proxy-preview"></a>DNS-proxy (preview-versie)

U kunt Azure Firewall configureren om te fungeren als een DNS-proxy. Een DNS-proxy fungeert als een intermediair voor DNS-aanvragen van virtuele client machines naar een DNS-server. Als u een aangepaste DNS-server configureert, moet u DNS-proxy inschakelen om te voor komen dat DNS-omzetting niet overeenkomen en FQDN-filtering inschakelen in netwerk regels.

Als u DNS-proxy niet inschakelt, kunnen DNS-aanvragen van de client op een ander tijdstip naar een DNS-server worden getransporteerd of een andere reactie retour neren vergeleken met de firewall. De DNS-proxy plaatst Azure Firewall in het pad van de client aanvragen om inconsistentie te voor komen.

De DNS-proxy configuratie vereist drie stappen:
1. Schakel DNS-proxy in Azure Firewall DNS-instellingen in.
2. Configureer eventueel uw aangepaste DNS-server of gebruik de standaard waarde.
3. Ten slotte moet u het privé-IP-adres van de Azure Firewall configureren als een aangepast DNS-adres in de instellingen van de DNS-server van uw virtuele netwerk. Dit zorgt ervoor dat DNS-verkeer wordt omgeleid naar Azure Firewall.

### <a name="configure-dns-proxy-preview"></a>DNS-proxy configureren (preview-versie)

Als u een DNS-proxy wilt configureren, moet u de instelling van de DNS-servers voor het virtuele netwerk configureren voor gebruik van het privé IP-adres van de firewall Schakel vervolgens DNS-proxy in Azure Firewall **DNS-instellingen**voor beleid.

#### <a name="configure-virtual-network-dns-servers"></a>DNS-servers voor het virtuele netwerk configureren

1. Selecteer het virtuele netwerk waarin het DNS-verkeer wordt doorgestuurd via de Azure Firewall.
2. Selecteer onder **Instellingen** de optie **DNS-servers**.
3. Selecteer **aangepast** onder **DNS-servers**.
4. Voer het privé IP-adres van de firewall in.
5. Selecteer **Opslaan**.

#### <a name="enable-dns-proxy-preview"></a>DNS-proxy inschakelen (preview-versie)

1. Selecteer uw Azure Firewall.
2. Selecteer **DNS-instellingen**onder **instellingen**.
3. **DNS-proxy** is standaard uitgeschakeld. Wanneer deze functie is ingeschakeld, luistert de firewall op poort 53 en stuurt DNS-aanvragen door naar de geconfigureerde DNS-servers.
4. Controleer de configuratie van de **DNS-servers** om ervoor te zorgen dat de instellingen geschikt zijn voor uw omgeving.
5. Selecteer **Opslaan**.

:::image type="content" source="media/dns-settings/dns-proxy.png" alt-text="DNS-proxy":::

## <a name="next-steps"></a>Volgende stappen

[FQDN-filtering in netwerk regels](fqdn-filtering-network-rules.md)