---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/14/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: ddab6927044ce638e50ff3ad79aa3c35d046c820
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/27/2020
ms.locfileid: "69520831"
---
De volgende configuratie is gebruikt voor de volgende stappen:

  | | |
  |---|---|
  |Computer| Ubuntu Server 18.04|
  |Afhankelijkheden| strongSwan |


Gebruik de volgende opdrachten om de vereiste strongSwan-configuratie te installeren:

```
sudo apt install strongswan
```

```
sudo apt install strongswan-pki
```

```
sudo apt install libstrongswan-extra-plugins
```

Gebruik de volgende opdracht om de Azure-opdracht regel interface te installeren:

```
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

[Aanvullende instructies voor het installeren van de Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-apt?view=azure-cli-latest)
