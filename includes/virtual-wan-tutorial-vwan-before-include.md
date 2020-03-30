---
title: bestand opnemen
description: bestand opnemen
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 09/12/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 945d701a2a7dffc259c601b4dab9fa1333ccc066
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74896616"
---
Controleer voordat u uw configuratie start of u aan de volgende criteria voldoet:

* Als u al een virtueel netwerk hebt waarmee u verbinding wilt maken, controleert u of geen van de subnetten van uw on-premises netwerk hiermee overlapt. Uw virtuele netwerk vereist geen gatewaysubnet en kan geen virtuele netwerkgateways hebben. Als u geen virtueel netwerk hebt, u er een maken met behulp van de stappen in dit artikel.
* Zorg dat u een IP-adresbereik krijgt voor uw hubregio. De hub is een virtueel netwerk en het adresbereik dat u opgeeft voor het hubgebied kan niet overlappen met een bestaand virtueel netwerk waarmee u verbinding maakt. Het kan ook niet overlappen met de adresbereiken waarmee u on-premises verbinding maakt. Als u niet bekend bent met de IP-adresbereiken in uw on-premises netwerkconfiguratie, coördineert u met iemand die deze gegevens voor u kan verstrekken.
* Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.