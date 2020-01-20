---
title: Een Azure-toepassing gateway certificaat vernieuwen
description: Meer informatie over het vernieuwen van een certificaat dat is gekoppeld aan een Application Gateway-listener.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 8/15/2018
ms.author: victorh
ms.openlocfilehash: c7c27f00b9f8b4fdcd8f735f842edb8f66803c6a
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278006"
---
# <a name="renew-application-gateway-certificates"></a>Application Gateway certificaten vernieuwen

Op een bepaald moment moet u uw certificaten vernieuwen als u uw toepassings gateway voor SSL-versleuteling hebt geconfigureerd.

U kunt een certificaat dat is gekoppeld aan een listener vernieuwen met behulp van de Azure Portal, Azure PowerShell of Azure CLI:

## <a name="azure-portal"></a>Azure Portal

Als u een listener-certificaat wilt vernieuwen vanuit de portal, gaat u naar de gateway-listeners van uw toepassing. Klik op de listener met een certificaat dat moet worden vernieuwd en klik vervolgens op het **geselecteerde certificaat vernieuwen of bewerken**.

![Certificaat vernieuwen](media/renew-certificate/ssl-cert.png)

Upload uw nieuwe PFX-certificaat, geef het een naam, typ het wacht woord en klik vervolgens op **Opslaan**.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Als u uw certificaat wilt vernieuwen met behulp van Azure PowerShell, gebruikt u het volgende script:

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName <ResourceGroup> `
  -Name <AppGatewayName>

$password = ConvertTo-SecureString `
  -String "<password>" `
  -Force `
  -AsPlainText

set-AzApplicationGatewaySSLCertificate -Name <oldcertname> `
-ApplicationGateway $appgw -CertificateFile <newcertPath> -Password $password

Set-AzApplicationGateway -ApplicationGateway $appgw
```
## <a name="azure-cli"></a>Azure-CLI

```azurecli-interactive
az network application-gateway ssl-cert update \
  -n "<CertName>" \
  --gateway-name "<AppGatewayName>" \
  -g "ResourceGroupName>" \
  --cert-file <PathToCerFile> \
  --cert-password "<password>"
```

## <a name="next-steps"></a>Volgende stappen

Zie [SSL-offload configureren](application-gateway-ssl-portal.md) voor meer informatie over het configureren van SSL-offloading met Azure-toepassing gateway
