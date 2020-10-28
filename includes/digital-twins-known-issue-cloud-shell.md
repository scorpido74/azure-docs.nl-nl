---
author: baanders
description: bestand opnemen voor Azure Digital Twins - vermeldt bekend probleem met Cloud Shell-verificatie
ms.service: digital-twins
ms.topic: include
ms.date: 9/1/2020
ms.author: baanders
ms.openlocfilehash: 574ca9d42565e897e49d6800e61bb2c33717891b
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92321343"
---
>[!NOTE]
>Er is momenteel een **bekend probleem** in Cloud Shell dat deze opdrachtgroepen beïnvloedt bij het uitvoeren vanaf *https://shell.azure.com* : `az dt route`, `az dt model`, `az dt twin`.
>
>U kunt dit op een van de volgende manieren oplossen:
> * Voer `az login` uit in Cloud Shell voordat u de opdracht uitvoert.
> * Open het deelvenster Cloud Shell in Azure Portal en maak uw Cloud Shell-werk daar af.
>  :::image type="content" source="../articles/digital-twins/media/includes/portal-cloud-shell.png" alt-text="Weergave van Azure Portal met het pictogram Cloud Shell gemarkeerd en Cloud Shell weergegeven onder aan het portalvenster":::
> * Gebruik de [lokale CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) in plaats van Cloud Shell.
>
>Zie [*Problemen oplossen: Bekende problemen in Azure Digital Twins*](../articles/digital-twins/troubleshoot-known-issues.md#400-client-error-bad-request-in-cloud-shell) voor meer informatie hierover.