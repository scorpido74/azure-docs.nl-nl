---
title: bestand opnemen
description: bestand opnemen
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 10/12/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b079ede0845de3794507691bc3c252930e2a6604
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91977159"
---
1. Open [Azure Portal](https://portal.azure.com). Navigeer naar de virtuele machine waarmee u verbinding wilt maken en selecteer vervolgens **Verbinding maken**. Selecteer **Bastion** in de vervolgkeuzelijst.

   :::image type="content" source="./media/bastion-vm-rdp/connect-vm.png" alt-text="Bastion selecteren" border="false":::

1. Nadat u Bastion in de vervolgkeuzelijst hebt geselecteerd, wordt er een zijbalk met drie tabbladen weergegeven: RDP, SSH en Bastion. Omdat Bastion is ingericht voor het virtuele netwerk, is het tabblad Bastion standaard actief. Selecteer **Azure Bastion gebruiken**.

   :::image type="content" source="./media/bastion-vm-rdp/use-bastion.png" alt-text="Bastion selecteren" border="false":::

1. Voer op de pagina **Verbinding maken met behulp van Azure Bastion** de gebruikersnaam en het wachtwoord in voor uw virtuele machine en selecteer vervolgens **Verbinding maken**.

   :::image type="content" source="./media/bastion-vm-rdp/host.png" alt-text="Bastion selecteren" border="false":::

1. De RDP-verbinding met deze virtuele machine via Bastion wordt rechtstreeks geopend in de Azure Portal (via HTML5) met behulp van poort 443 en de Bastion-service.

   :::image type="content" source="./media/bastion-vm-rdp/connection.png" alt-text="Bastion selecteren" border="false":::
