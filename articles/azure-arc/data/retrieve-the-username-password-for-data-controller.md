---
title: De gebruikers naam en het wacht woord ophalen om verbinding te maken met de Arc-gegevens controller
description: De gebruikers naam en het wacht woord ophalen om verbinding te maken met de Arc-gegevens controller
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 1c33cf21be0b4b1ea39a568d6df9fd90507dd454
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90935948"
---
# <a name="retrieve-the-user-name-and-password-to-connect-to-the-arc-data-controller"></a>De gebruikers naam en het wacht woord ophalen om verbinding te maken met de Arc-gegevens controller

Mogelijk hebt u een situatie waarin u de gebruikers naam en het wacht woord voor de gegevens controller moet ophalen. Dit zijn de opdrachten die u nodig hebt wanneer u uitvoert. 

```console
azdata login
```

Als u de Kubernetes-beheerder bent voor het cluster. Zo kunt u de rechten hebben om opdrachten uit te voeren die uit het Kubernetes-geheim worden opgehaald, waarin de informatie wordt opgeslagen die Azure-Arc daar aanhoudt.

> [!NOTE]
>  Als u een andere naam hebt gebruikt voor de naam ruimte waarin de gegevens controller is gemaakt, moet u de `-n arc` para meter in de onderstaande opdrachten wijzigen om de naam van de naam ruimte te gebruiken waarnaar u de gegevens controller hebt gemaakt.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="linux"></a>Linux

Voer de volgende opdracht uit om de gebruikers naam op te halen:

```console
kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['username']}" | base64 -d
```

Voer de volgende opdracht uit om het wacht woord op te halen:

```console
kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['password']}" | base64 -d
```

## <a name="powershell"></a>PowerShell

Voer de volgende opdracht uit om de gebruikers naam op te halen:

```console
[Text.Encoding]::Utf8.GetString([Convert]::FromBase64String((kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['username']}")))
```

Voer de volgende opdracht uit om het wacht woord op te halen:

```console
[Text.Encoding]::Utf8.GetString([Convert]::FromBase64String((kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['password']}")))
```

## <a name="next-steps"></a>Volgende stappen

Andere [scenario's](https://github.com/microsoft/Azure-data-services-on-Azure-Arc/tree/master/scenarios) uitproberen
