---
title: Windows PowerShell gebruiken om verbinding te maken met en het beheer van Azure Data Box Gateway-apparaat
description: Beschrijft hoe u verbinding maken met en vervolgens gegevensboxgateway beheren via de Windows PowerShell-interface.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 08/02/2019
ms.author: alkohli
ms.openlocfilehash: 6c9f3455a07001a8d1b9b8a1e84f2af3392b5690
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260213"
---
# <a name="manage-an-azure-data-box-gateway-device-via-windows-powershell"></a>Een Azure Data Box Gateway-apparaat beheren via Windows PowerShell

Met de Azure Data Box Gateway-oplossing u gegevens via het netwerk naar Azure verzenden. In dit artikel worden enkele configuratie- en beheertaken voor uw Data Box Gateway-apparaat beschreven. U de Azure-portal, de lokale web-gebruikersinterface of de Windows PowerShell-interface gebruiken om uw apparaat te beheren.

Dit artikel richt zich op de taken die u uitvoert met de PowerShell-interface.

Dit artikel bevat de volgende procedures:

- Verbinding maken met de PowerShell-interface
- Een ondersteuningspakket maken
- Certificaat uploaden
- Opstarten in niet-DHCP-omgeving
- Apparaatgegevens weergeven

## <a name="connect-to-the-powershell-interface"></a>Verbinding maken met de PowerShell-interface

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Een ondersteuningspakket maken

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Certificaat uploaden

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

## <a name="boot-up-in-non-dhcp-environment"></a>Opstarten in niet-DHCP-omgeving

[!INCLUDE [Boot up in non-DHCP environment](../../includes/data-box-edge-gateway-boot-non-dhcp.md)]

## <a name="view-device-information"></a>Apparaatgegevens weergeven

[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="next-steps"></a>Volgende stappen

- Implementeer [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) in de Azure Portal.
