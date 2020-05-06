---
title: Windows Power shell gebruiken om verbinding te maken met Azure Data Box Gateway apparaat en te beheren
description: Hierin wordt beschreven hoe u verbinding maakt met Data Box Gateway en vervolgens beheert via de Windows Power shell-interface.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 08/02/2019
ms.author: alkohli
ms.openlocfilehash: 2413e53e9613aaa1140b083cb24fe709eb1b067d
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82561769"
---
# <a name="manage-an-azure-data-box-gateway-device-via-windows-powershell"></a>Een Azure Data Box Gateway-apparaat beheren via Windows Power shell

Met Azure Data Box Gateway oplossing kunt u gegevens via het netwerk naar Azure verzenden. In dit artikel worden enkele van de configuratie-en beheer taken voor uw Data Box Gateway-apparaat beschreven. U kunt de Azure Portal, de lokale webgebruikersinterface of de Windows Power shell-interface gebruiken om uw apparaat te beheren.

Dit artikel is gericht op de taken die u gebruikt om de Power shell-interface te gebruiken. 

Dit artikel bevat de volgende procedures:

- Verbinding maken met de Power shell-interface
- Een ondersteunings pakket maken
- Certificaat uploaden
- Opstarten in niet-DHCP-omgeving
- Apparaatgegevens weer geven

## <a name="connect-to-the-powershell-interface"></a>Verbinding maken met de Power shell-interface

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Een ondersteunings pakket maken

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Certificaat uploaden

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

## <a name="boot-up-in-non-dhcp-environment"></a>Opstarten in niet-DHCP-omgeving

[!INCLUDE [Boot up in non-DHCP environment](../../includes/data-box-edge-gateway-boot-non-dhcp.md)]

## <a name="view-device-information"></a>Apparaatgegevens weer geven

[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="next-steps"></a>Volgende stappen

- Implementeer [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) in de Azure Portal.
