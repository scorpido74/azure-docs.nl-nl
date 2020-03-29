---
title: Een Azure Application Gateway-certificaat vernieuwen
description: Meer informatie over het vernieuwen van een certificaat dat is gekoppeld aan een serverlistener voor de toepassingsgateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 8/15/2018
ms.author: victorh
ms.openlocfilehash: c7c27f00b9f8b4fdcd8f735f842edb8f66803c6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278006"
---
# <a name="renew-application-gateway-certificates"></a>Toepassingsgatewaycertificaten vernieuwen

Op een gegeven moment moet u uw certificaten vernieuwen als u de toepassingsgateway voor SSL-versleuteling hebt geconfigureerd.

U een certificaat verlengen dat is gekoppeld aan een listener met behulp van de Azure-portal, Azure PowerShell of Azure CLI:

## <a name="azure-portal"></a>Azure Portal

Als u een listenercertificaat vanuit de portal wilt vernieuwen, navigeert u naar de listeners van de toepassingsgateway. Klik op de listener met een certificaat dat moet worden vernieuwd en klik vervolgens op **Geselecteerd certificaat verlengen of bewerken**.

![Certificaat verlengen](media/renew-certificate/ssl-cert.png)

Upload uw nieuwe PFX-certificaat, geef het een naam, typ het wachtwoord en klik op **Opslaan**.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Als u uw certificaat wilt vernieuwen met Azure PowerShell, gebruikt u het volgende script:

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

Zie SSL-offload configureren voor meer informatie over het configureren [van SSL-offloading](application-gateway-ssl-portal.md) met Azure Application Gateway
