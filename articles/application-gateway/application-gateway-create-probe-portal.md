---
title: Een aangepaste probe-Azure-toepassing-gateway maken-Azure Portal | Microsoft Docs
description: Meer informatie over het maken van een aangepaste test voor Application Gateway met behulp van de portal
services: application-gateway
documentationcenter: na
author: vhorne
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.assetid: 33fd5564-43a7-4c54-a9ec-b1235f661f97
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: victorh
ms.openlocfilehash: b92b9d953b6dd941b8b5f445ad64059f557c2980
ms.sourcegitcommit: 388c8f24434cc96c990f3819d2f38f46ee72c4d8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2019
ms.locfileid: "70061793"
---
# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>Een aangepaste test maken voor Application Gateway met behulp van de portal

> [!div class="op_single_selector"]
> * [Azure-portal](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Azure Classic PowerShell](application-gateway-create-probe-classic-ps.md)

In dit artikel voegt u een aangepaste test toe aan een bestaande toepassings gateway via de Azure Portal. Aangepaste tests zijn handig voor toepassingen met een specifieke status controle pagina of voor toepassingen die geen geslaagde reactie op de standaard webtoepassing bieden.

## <a name="before-you-begin"></a>Voordat u begint

Als u nog geen toepassings gateway hebt, gaat u naar [een Application Gateway maken](application-gateway-create-gateway-portal.md) om een toepassings gateway te maken waarmee u kunt werken.

## <a name="createprobe"></a>De test maken

Tests worden in een proces in twee stappen geconfigureerd via de portal. De eerste stap is het maken van de test. In de tweede stap voegt u de test toe aan de back-end-http-instellingen van de toepassings gateway.

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Als u nog geen account hebt, kunt u zich aanmelden voor een [gratis proef versie van één maand](https://azure.microsoft.com/free)

1. Klik in het deel venster favorieten Azure Portal op alle resources. Klik in de Blade alle resources op de toepassings gateway. Als het abonnement dat u hebt geselecteerd, al verschillende resources heeft, kunt u partners.contoso.net invoeren in de filter op naam... voor eenvoudige toegang tot de toepassingsgateway.

1. Klik op **tests** en klik op de knop **toevoegen** om een test toe te voegen.

   ![Blade test toevoegen met ingevulde gegevens][1]

1. Vul op de Blade **status test toevoegen** de vereiste informatie voor de test in en klik op **OK**wanneer u klaar bent.

   |**Instelling** | **Waarde** | **Details**|
   |---|---|---|
   |**Name**|customProbe|Deze waarde is een beschrijvende naam voor de test die toegankelijk is in de portal.|
   |**Protocol**|HTTP of HTTPS | Het protocol dat wordt gebruikt door de status test.|
   |**Host**|dat wil zeggen contoso.com|Deze waarde is de hostnaam die wordt gebruikt voor de test. Alleen van toepassing als multi-site is geconfigureerd op Application Gateway, anders ' 127.0.0.1 ' gebruiken. Deze waarde wijkt af van de naam van de VM-host.|
   |**Pad**|/of een ander pad|De rest van de volledige URL voor de aangepaste test. Er begint een geldig pad met '/'. Gebruik voor het standaardpad van http:\//contoso.com alleen '/' |
   |**Interval (sec.)**|30|Hoe vaak de test wordt uitgevoerd om de status te controleren. Het wordt afgeraden om de lagere dan 30 seconden in te stellen.|
   |**Time-out (SEC)**|30|De hoeveelheid tijd die de test wacht voordat een time-out optreedt. Het time-outinterval moet hoog genoeg zijn dat er een http-aanroep kan worden uitgevoerd om ervoor te zorgen dat de status van de back-end beschikbaar is.|
   |**Drempel waarde voor onjuiste status**|3|Aantal mislukte pogingen om te worden beschouwd als een slechte status. De drempel waarde kan worden ingesteld op 1 of meer.|

   > [!IMPORTANT]
   > De hostnaam is niet hetzelfde als de naam van de server. Deze waarde is de naam van de virtuele host die wordt uitgevoerd op de toepassings server. De test wordt verzonden naar http://(hostnaam):(poort van httpsetting)/urlPath

## <a name="add-probe-to-the-gateway"></a>Test toevoegen aan de gateway

Nu de test is gemaakt, is het tijd om deze toe te voegen aan de gateway. De test instellingen worden ingesteld op de back-end-http-instellingen van de toepassings gateway.

1. Klik op **http-instellingen** op de Application Gateway om de Blade configuratie weer te geven. Klik op de huidige back-end-http-instellingen in het venster.

   ![venster https-instellingen][2]

1. Schakel op de Blade **appGatewayBackEndHttpSettings** -instellingen het selectie vakje **aangepaste test gebruiken** in en kies de test die u hebt gemaakt in de sectie [de test maken](#createprobe) in de vervolg keuzelijst **aangepaste test** .
   Wanneer u klaar bent, klikt u op **Opslaan** en worden de instellingen toegepast.

De standaard test controleert de standaard toegang tot de webtoepassing. Nu u een aangepaste test hebt gemaakt, gebruikt de toepassings gateway het aangepaste pad dat is gedefinieerd om de status van de back-endservers te controleren. Op basis van de criteria die zijn gedefinieerd, controleert de toepassings gateway het pad dat is opgegeven in de test. Als de aanroep van host: poort/pad geen HTTP 200-399-status antwoord retourneert, wordt de server uit de rotatie gehaald nadat de drempel waarde voor een onjuiste status is bereikt. Er wordt door probing geblijfd op het beschadigde exemplaar om te bepalen wanneer het weer in orde is. Zodra het exemplaar is toegevoegd aan een goede Server groep, begint het verkeer opnieuw naar het proces en wordt het door de gebruiker opgegeven interval op de normale wijze door gegeven aan het proces.

## <a name="next-steps"></a>Volgende stappen

Zie [SSL-offload configureren](application-gateway-ssl-portal.md) voor meer informatie over het configureren van SSL-offloading met Azure-toepassing gateway

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png

