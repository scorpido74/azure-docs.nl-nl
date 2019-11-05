---
title: 'Snelstartgids: computers verbinden met Azure met behulp van Azure Arc voor servers-Portal'
description: In deze Quick Start leert u hoe u met behulp van Azure Arc voor servers verbinding kunt maken met Azure via de portal
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: bobbytreed
ms.author: robreed
keywords: Azure Automation, DSC, Power shell, desired state Configuration, update beheer, wijzigingen bijhouden, inventaris, runbooks, Python, grafisch, hybride, onboard
ms.date: 08/25/2019
ms.custom: mvc
ms.topic: quickstart
ms.openlocfilehash: b014f6015b3e13a603cf3893062bd0463eb110ee
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73501986"
---
# <a name="quickstart-connect-machines-to-azure-using-azure-arc-for-servers---portal"></a>Snelstartgids: computers verbinden met Azure met behulp van Azure Arc voor servers-Portal

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Bekijk de ondersteunde clients en de vereiste netwerk configuratie in het [overzicht van Azure-Arc voor servers](overview.md).

## <a name="generate-the-agent-install-script-using-the-azure-portal"></a>Het installatie script voor de agent genereren met behulp van de Azure Portal

1. Start [https://aka.ms/hybridmachineportal] [aka_hybridmachineportal]
1. Klik op **+ toevoegen**
1. Volg de wizard om te volt ooien
1. Op de laatste pagina is een script gegenereerd dat u kunt kopiëren (of downloaden).

Het script moet worden uitgevoerd op de doel computer waarmee u verbinding wilt maken. Het downloadt de agent, installeert deze en verbindt de computer als één bewerking.

Op de niet-Azure-servers die u wilt beheren:

1. Aanmelden bij de server (via SSH, RDP of externe communicatie met Power shell)
1. Start een shell: bash in Linux, Power shell als beheerder op Windows
1. Plak het script vanuit de portal en voer dit op de server uit om verbinding te maken met Azure.
1. De standaard verificatie voor het onboarden van een afzonderlijke server is *interactief* met behulp van Azure ' Device aanmelding. Wanneer u het script uitvoert, wordt een bericht weer gegeven dat vergelijkbaar is met het volgende:

  ```none
  To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code B3V3NLWRF to authenticate.
  ```
  
   Open een browser en voer de code in die u wilt verifiëren. De browser hoeft niet te worden uitgevoerd op de server waarop u het onboarding uitvoert. deze kan zich op een andere computer, zoals uw laptop, bevinden.

1. Als u niet-interactief wilt verifiëren, voert u de stappen in [een service-principal maken](quickstart-onboard-powershell.md#create-a-service-principal-for-onboarding-at-scale) uit en wijzigt u het script dat is gegenereerd op basis van de portal.

> [!NOTE]
> Als u Internet Explorer op de server gebruikt voor de eerste keer dat u zich aanmeldt, wordt er een fout bericht weer. U kunt de browser gewoon opnieuw openen en opnieuw doen.

## <a name="execute-the-script-on-target-nodes"></a>Het script uitvoeren op doel knooppunten

Meld u aan bij elk knoop punt en voer het script uit dat u hebt gegenereerd op basis van de portal. Nadat het script is voltooid, gaat u naar de Azure Portal Controleer of de server met succes is verbonden.

![Geslaagde onboarding](./media/quickstart-onboard/arc-for-servers-successful-onboard.png)

## <a name="clean-up"></a>Opruimen

Als u een computer wilt loskoppelen van Azure Arc voor servers, moet u twee stappen uitvoeren.

1. Selecteer de machine in de [Portal](https://aka.ms/hybridmachineportal), klik op het weglatings teken (`...`) en selecteer **verwijderen**.
1. Verwijder de agent van de computer.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een beleid toewijzen aan verbonden computers](../../governance/policy/assign-policy-portal.md)