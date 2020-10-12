---
title: Okta single Sign-On-gegevens koppelen aan Azure Sentinel | Microsoft Docs
description: Meer informatie over het verbinden van Okta single Sign-On-gegevens naar Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: 05a9b8009d896a2ee87df3e1c4493d249a887566
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87083919"
---
# <a name="connect-your-okta-single-sign-on-to-azure-sentinel-with-azure-function"></a>Verbind uw Okta single Sign-On met Azure Sentinel met Azure function

> [!IMPORTANT]
> De Okta single Sign-On Data Connector in azure Sentinel is momenteel beschikbaar als open bare preview.
> Deze functie wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Met de Okta single Sign-On (SSO)-connector kunt u eenvoudig al uw [Okta single Sign-On (SSO)](https://www.okta.com/products/single-sign-on/) -beveiligings oplossingen koppelen met Azure Sentinel, voor het weer geven van Dash boards, het maken van aangepaste waarschuwingen en het verbeteren van onderzoek. Integratie tussen Okta single Sign-On en Azure Sentinel maakt gebruik van Azure Functions om logboek gegevens te verzamelen met behulp van REST API.

> [!NOTE]
> Gegevens worden opgeslagen in de geografische locatie van de werk ruimte waarop u Azure Sentinel uitvoert.

## <a name="configure-and-connect-okta-single-sign-on"></a>Okta single Sign-On configureren en verbinden

Azure Functions kunnen gebeurtenissen en logboeken rechtstreeks vanuit Okta single Sign-On integreren en ophalen en deze door sturen naar Azure Sentinel.

1. Klik in de Azure-Sentinel-Portal op **Data connectors** en selecteer **Okta single sign-on** connector.

1. Selecteer de **pagina connector openen**.

1. Volg de instructies op de pagina **Okta single sign-on** .

## <a name="find-your-data"></a>Uw gegevens zoeken

Nadat de verbinding tot stand is gebracht, worden de gegevens weer gegeven in Log Analytics onder de tabel **Okta_CL** .

## <a name="validate-connectivity"></a>Connectiviteit valideren

Het kan 20 minuten duren voordat uw logboeken in Log Analytics worden weer gegeven.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u Okta single Sign-On kunt verbinden met Azure Sentinel met behulp van Azure function-apps. Zie de volgende artikelen voor meer informatie over Azure Sentinel:

- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.

