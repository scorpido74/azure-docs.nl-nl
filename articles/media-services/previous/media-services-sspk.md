---
title: Microsoft&reg; Smooth Streaming Client Porting Kit licentieverlening
description: Meer informatie over het&reg; verlenen van licenties voor de Microsoft Smooth Streaming Client Porting Kit.
services: media-services
documentationcenter: ''
author: xpouyat
manager: femila
editor: ''
ms.assetid: e3b488e7-8428-4c10-a072-eb3af46c82ad
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/21/2019
ms.author: xpouyat
ms.openlocfilehash: bd77c53a195a9549f6aaad9ee2928206f5324b0c
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686900"
---
# <a name="licensing-microsoftreg-smooth-streaming-client-porting-kit"></a>Microsoft&reg; Smooth Streaming Client Porting Kit licentieverlening 
## <a name="overview"></a>Overzicht
Microsoft Smooth Streaming Client Porting Kit **(SSPK** voor kort) is een Smooth Streaming client implementatie die is geoptimaliseerd om embedded apparaat fabrikanten, kabel-en mobiele operators, content service providers, handset fabrikanten, onafhankelijke software leveranciers (ISV's), en solution providers om producten en diensten voor streaming adaptieve inhoud in Smooth Streaming formaat te creëren. SSPK is een apparaat- en platformonafhankelijke implementatie van Smooth Streaming-client die door de licentiehouder naar elk apparaat en platform kan worden geport. 

Hieronder is een architectuur op hoog niveau en IIS Smooth Streaming Porting Kit box is de Smooth Streaming Client implementatie die door Microsoft en bevat alle kern logica voor het afspelen van Smooth Streaming content. Deze inhoud wordt vervolgens geport door partners voor een specifiek apparaat of platform door het implementeren van geschikte interfaces. 

![SSPK (SSPK)](./media/media-services-sspk/sspk-arch.png)

## <a name="description"></a>Beschrijving
SSPK is gelicentieerd op voorwaarden die een uitstekende bedrijfswaarde bieden. SSPK licentie biedt de industrie met:

* Smooth Streaming Porting Kit-bron in C++ 
  * implementeert functionaliteit voor smooth streaming client
  * voegt formaat parsing, heuristiek, bufferen logica, enz.
* API's voor spelerstoepassingen 
  * programmeerinterfaces voor interactie met een mediaspelertoepassing
* Pal-interface (Platform Abstraction Layer) 
  * programmeerinterfaces voor interactie met het besturingssysteem (threads, sockets)
* HAL-interface (Hardware Abstractie Layer) 
  * programmeerinterfaces voor interactie met hardware A/V-decoders (decoderen, renderen)
* DRM-interface (Digital Rights Management) 
  * programmeerinterfaces voor het verwerken van DRM via de DRM Abstraction Layer (DAL)
  * Microsoft PlayReady Porting Kit wordt afzonderlijk geleverd, maar integreert via deze interface. Klik [hier](https://www.microsoft.com/playready/licensing/device_technology.mspx#pddipdl)voor meer informatie over microsoft PlayReady Device-licenties.
* Uitvoeringsmonsters 
  * voorbeeld PAL-implementatie voor Linux
  * voorbeeld HAL-implementatie voor GStreamer

## <a name="licensing-options"></a>Licentieopties
Microsoft Smooth Streaming Client Porting Kit wordt beschikbaar gesteld aan licentiehouders onder twee verschillende licentieovereenkomsten: een voor het ontwikkelen van Smooth Streaming Client Interim-producten en een voor het distribueren van Smooth Streaming Client Final Products aan eindgebruikers.

* Voor chipsetfabrikanten, systeemintegratoren of onafhankelijke softwareleveranciers (ISV's) die een broncodeportingkit nodig hebben om Interim-producten te ontwikkelen, moet een Microsoft Smooth Streaming Client Porting Kit **Interim-productlicentie** worden uitgevoerd.
* Voor apparaatfabrikanten of ISV's die distributierechten nodig hebben voor smooth streaming client-eindproducten aan eindgebruikers, moet de **definitieve productlicentie** voor Microsoft Smooth Streaming Client Porting Kit worden uitgevoerd.

### <a name="microsoft-smooth-streaming-client-porting-kit-interim-product-license"></a>Microsoft Smooth Streaming Client Porting Kit Interim Product License
Onder deze licentie biedt Microsoft een Smooth Streaming Client Porting Kit en de nodige intellectuele eigendomsrechten om Smooth Streaming Client Interim-producten te ontwikkelen en te distribueren naar andere Smooth Streaming Client Porting Kit-apparaatlicentiehouders die Smooth Streaming Client Final Products distribueren.

#### <a name="fee-structure"></a>Vergoedingsstructuur
Een amerikaanse licentievergoeding van $ 50.000 biedt toegang tot de Smooth Streaming Client Porting Kit. 

### <a name="microsoft-smooth-streaming-client-porting-kit-final-product-license"></a>Microsoft Smooth Streaming Client Porting Kit Final Product License
Onder deze licentie biedt Microsoft alle benodigde intellectuele eigendomsrechten om Smooth Streaming Client Interim-producten van andere Smooth Streaming Client Porting Kit-licentiehouders te ontvangen en om smooth streaming client-eindproducten van het bedrijf te distribueren naar eindgebruikers.

#### <a name="fee-structure"></a>Vergoedingsstructuur
Het Smooth Streaming Client Final Product wordt aangeboden onder een royaltymodel als onder:

* $ 0,10 per verzonden apparaatimplementatie
* De royalty wordt gemaximeerd op $ 50.000 per jaar
* Geen royalty's voor de eerste 10.000 apparaatimplementaties per jaar 

## <a name="licensing-procedure-and-sspk-access"></a>Licentieprocedure en SSPK-toegang
E-mail [sspkinfo@microsoft.com](mailto:sspkinfo@microsoft.com) voor alle licentiequery's.

Het SSPK Distribution portal is toegankelijk voor geregistreerde Interim licentiehouders.

Interim en Final SSPK licentiehouders [smoothpk@microsoft.com](mailto:smoothpk@microsoft.com)kunnen technische vragen stellen aan .

## <a name="microsoft-smooth-streaming-client-interim-product-agreement-licensees"></a>Microsoft Smooth Streaming Client Interim Product Agreement Licensees

* Adroit Business Solutions, Inc
* Geavanceerde digitale uitzending SA
* AirTies Kablosuz Iletism Sanayive Dis Ticaret A.S.
* Albis Technologies Ltd.
* Alticast Corporation
* Amazon Digital Services, Inc.
* Arion Technology, Inc.
* AVC Multimedia Software Co., Ltd.
* Cavium, Inc.
* EchoStar Inkoop corporation
* Enseo, Inc.
* Fluendo S.A.
* Guangzhou Shikun Electronics., Ltd.
* HANDAN BroadInfoCom Co., Ltd.
* Infomir GMBH
* Irdeto USA Inc.
* Liberty Global Services BV
* MediaTek Inc.
* MStar Co, Ltd
* Nintendo Co., Ltd.
* OpenTV, Inc.
* Saffraan Digital Limited
* Sichuan Changhong Electric Co., Ltd
* SoftAtHome SoftAtHome
* Sony Corporation
* Tatung Technology Inc.
* Top Victory Investments, Ltd.
* Vestel Elektronik Sanayi ve Ticaret A.S.
* VisualOn, Inc.
* ZTE Corporation

## <a name="microsoft-smooth-streaming-client-final-product-agreement-licensees"></a>Microsoft Smooth Streaming Client Final Product Agreement Licensees
* Geavanceerde digitale uitzending SA
* AirTies Kablosuz Iletism Sanayive Dis Ticaret A.S.
* Albis Technologies Ltd.
* Amazon Digital Services, Inc.
* AmTRAN Technology Co., Ltd.
* Arcadyan Technology Corporation
* Arion Technology, Inc.
* ATMACA ELEKTRONİK SAN. VE TİC. A.ş
* British Sky Broadcasting Limited
* CastPal Technology Inc., Shenzhen
* Compal Electronics, Inc.
* Dongguan Digital AV Technology Corp., Ltd.
* EchoStar Inkoop corporation
* Enseo, Inc.
* FilmFlex Movies Limited
* Fluendo S.A.
* FUNAI ELECTRIC CO., LTD
* Gibson Innovations Limited
* Haier Informatie AanvragersIon S.R.L
* HANDAN BroadInfoCom Co., Ltd.
* Hisense International Co., Ltd. 
* Homecast Co., Ltd
* Hon Hai Precision Industry Co., Ltd.
* Infomir GMBH
* Kaonmedia Co., Ltd.
* KDDI Corporation
* Nintendo Co., Ltd.
* Oranje SA
* Saffraan Digital Limited
* Sagemcom Breedband SAS
* Shenzhen Chuangwei-RGB Electronics Co.,Ltd.
* Shenzhen Coship Electronics CO., LTD
* Shenzhen Jiuzhou Electric Co., Ltd
* Shenzhen Skyworth Digital Technology Co,, Ltd
* Sichuan Changhong Electric Co., Ltd.
* Skardin Industrial Corp.
* Sky Deutschland Fernsehen GmbH & Co. KG
* SmarDTV S.A.
* SoftAtHome SoftAtHome
* Sony Corporation
* Technicolor Delivery Technologies, SAS
* Tongfang Global Ltd.
* Top Victory Investments, Ltd.
* Toshiba Lifestyle Producten & Services Corporation
* Universal Media Corporation /Slowakije/ s.r.o.
* VIZIO, Inc.
* Wistron Corporation
* ZTE Corporation

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

