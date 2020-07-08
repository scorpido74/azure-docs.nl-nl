---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 21dbec52dded5a195cc764741ab9e8d79737c549
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "67175887"
---
1. Meld u aan bij uw Azure-abonnement met de opdracht [az login](/cli/azure/) en volg de instructies op het scherm. Zie [Aan de slag met Azure CLI](/cli/azure/get-started-with-azure-cli) voor meer informatie over aanmelden.

   ```azurecli
   az login
   ```
2. Als u meer dan één Azure-abonnement hebt, worden alle abonnementen voor het account weergegeven.

   ```azurecli
   az account list --all
   ```
3. Geef het abonnement op dat u wilt gebruiken.

   ```azurecli
   az account set --subscription <replace_with_your_subscription_id>
   ```
