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
ms.openlocfilehash: 2d0e171807985deaebe8faa625d6b767c2a3efd7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86218442"
---
De volgende configuratie is gebruikt voor de volgende stappen:

- Computer: Ubuntu Server 18,04
- Afhankelijkheden: strongSwan


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
