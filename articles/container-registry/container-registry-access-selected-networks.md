---
title: Firewall regels voor services configureren
description: Configureer IP-regels voor toegang tot een Azure container Registry vanuit geselecteerde open bare IP-adressen of adresbereiken.
ms.topic: article
ms.date: 05/04/2020
ms.openlocfilehash: f6459061ca486b4bf229409e6ec1ed1bd808a474
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/08/2020
ms.locfileid: "82984614"
---
# <a name="configure-public-ip-network-rules"></a>Open bare IP-netwerk regels configureren

Een Azure-container register accepteert standaard verbindingen via Internet van hosts op elk netwerk. In dit artikel wordt uitgelegd hoe u het container register zo configureert dat alleen specifieke open bare IP-adressen of adresbereiken toegankelijk zijn. Vergelijk bare stappen die gebruikmaken van de Azure CLI en Azure Portal worden meegeleverd.

IP-netwerk regels worden geconfigureerd op het open bare register eindpunt. IP-netwerk regels zijn niet van toepassing op privé-eind punten die zijn geconfigureerd met een [persoonlijke koppeling](container-registry-private-link.md)

Het configureren van IP-toegangs regels is beschikbaar in de service tier van het **Premium** -container register. Zie [Azure container Registry-lagen](container-registry-skus.md)voor meer informatie over de service lagen en limieten voor het REGI ster.

## <a name="access-from-selected-public-network---cli"></a>Toegang vanuit geselecteerd openbaar netwerk-CLI

### <a name="change-default-network-access-to-registry"></a>De standaard netwerk toegang wijzigen in het REGI ster

Als u de toegang tot een geselecteerd openbaar netwerk wilt beperken, wijzigt u eerst de standaard actie om de toegang te weigeren. Vervang de naam van het REGI ster in de volgende [AZ ACR update][az-acr-update] -opdracht:

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

### <a name="add-network-rule-to-registry"></a>Netwerk regel toevoegen aan REGI ster

Gebruik de opdracht [AZ ACR Network-Rule add][az-acr-network-rule-add] om een netwerk regel toe te voegen aan uw REGI ster waarmee toegang vanaf een openbaar IP-adres of-bereik is toegestaan. Vervang bijvoorbeeld de naam van het container register en het open bare IP-adres van een virtuele machine in een virtueel netwerk.

```azurecli
az acr network-rule add \
  --name mycontainerregistry \
  --ip-address <public-IP-address>
```

> [!NOTE]
> Na het toevoegen van een regel duurt het enkele minuten voordat de regel van kracht wordt.

## <a name="access-from-selected-public-network---portal"></a>Toegang vanuit geselecteerd openbaar netwerk-Portal

1. Navigeer in de portal naar het container register.
1. Selecteer onder **instellingen**de optie **netwerken**.
1. Schakel op het tabblad **open bare toegang** het selectie vakje open bare toegang tot **geselecteerde netwerken**toe.
1. Voer onder **firewall**een openbaar IP-adres in, zoals het open bare IP-adres van een virtuele machine in een virtueel netwerk. Of voer een adres bereik in de CIDR-notatie in die het IP-adres van de virtuele machine bevat.
1. Selecteer **Opslaan**.

![Firewall regel voor container register configureren][acr-access-selected-networks]

> [!NOTE]
> Na het toevoegen van een regel duurt het enkele minuten voordat de regel van kracht wordt.

> [!TIP]
> Schakel eventueel toegang tot het REGI ster in vanaf een lokale client computer of een IP-adres bereik. U hebt het open bare IPv4-adres van de computer nodig om deze toegang toe te staan. U kunt dit adres vinden door te zoeken in ' wat is mijn IP-adres ' in een Internet browser. Het huidige client IPv4-adres wordt ook automatisch weer gegeven wanneer u Firewall-instellingen configureert op de pagina **netwerk** in de portal.

## <a name="disable-public-network-access"></a>Open bare netwerk toegang uitschakelen

Schakel het open bare eind punt uit in het REGI ster om het verkeer naar virtuele netwerken met een [persoonlijke koppeling](container-registry-private-link.md)te beperken. Als u het open bare eind punt uitschakelt, worden alle firewall configuraties overschreven.

### <a name="disable-public-access---portal"></a>Open bare toegang uitschakelen-Portal

1. Ga in de portal naar het container register en selecteer **instellingen > netwerk**.
1. Selecteer **uitgeschakeld**op het tabblad **open bare toegang** in **open bare toegang toestaan**. Selecteer vervolgens **Opslaan**.

![Open bare toegang uitschakelen][acr-access-disabled]

## <a name="restore-default-registry-access"></a>Standaard toegang tot het REGI ster herstellen

Als u het REGI ster wilt herstellen om standaard toegang toe te staan, werkt u de standaard actie bij. 

### <a name="restore-default-registry-access---portal"></a>Standaard toegang tot het REGI ster herstellen-Portal

1. Ga in de portal naar het container register en selecteer **instellingen > netwerk**.
1. Onder **firewall**selecteert u elk adres bereik en selecteert u vervolgens het pictogram verwijderen.
1. Selecteer **alle netwerken**in **open bare toegang toestaan**op het tabblad **open bare toegang** . Selecteer vervolgens **Opslaan**.

![Open bare toegang vanuit alle netwerken][acr-access-all-networks]

## <a name="next-steps"></a>Volgende stappen

* Als u de toegang tot een REGI ster wilt beperken met behulp van een persoonlijk eind punt in een virtueel netwerk, raadpleegt u [Azure private link configureren voor een Azure container Registry](container-registry-private-link.md).
* Zie [regels configureren voor toegang tot een Azure container Registry achter een firewall](container-registry-firewall-access-rules.md)als u toegangs regels voor het REGI ster wilt instellen van achter een firewall van een client.

[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-network-rule-add]: /cli/azure/acr/network-rule/#az-acr-network-rule-add
[az-acr-network-rule-remove]: /cli/azure/acr/network-rule/#az-acr-network-rule-remove
[az-acr-network-rule-list]: /cli/azure/acr/network-rule/#az-acr-network-rule-list
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-update]: /cli/azure/acr#az-acr-update
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com

[acr-access-selected-networks]: ./media/container-registry-access-selected-networks/acr-access-selected-networks.png
[acr-access-disabled]: ./media/container-registry-access-selected-networks/acr-access-disabled.png
[acr-access-all-networks]: ./media/container-registry-access-selected-networks/acr-access-all-networks.png
