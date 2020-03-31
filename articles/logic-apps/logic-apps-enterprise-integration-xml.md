---
title: XML-berichten en platte bestanden
description: XML-berichten verwerken, valideren en transformeren in Azure Logic Apps met Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/27/2017
ms.openlocfilehash: 3cb3472572abbd891f8d36ea036b371c1224f38c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792146"
---
# <a name="xml-messages-and-flat-files-in-azure-logic-apps-with-enterprise-integration-pack"></a>XML-berichten en platte bestanden in Azure Logic Apps met Enterprise Integration Pack

In [Azure Logic Apps](logic-apps-overview.md)u XML-berichten verwerken die u verzendt en ontvangt met behulp van het Enterprise Integration Pack. Als u BizTalk Server hebt gebruikt, biedt het Enterprise Integration Pack vergelijkbare mogelijkheden om berichten te transformeren en te valideren, met platte bestanden te werken en zelfs XPath te gebruiken om specifieke eigenschappen uit een bericht te verrijken of te extraheren. Als u nieuw bent in deze ruimte, wordt met deze functies uitgelegd hoe u berichten verwerkt in de werkstroom van uw logische app. Als u bijvoorbeeld een B2B-scenario (business-to-business) hebt en met specifieke XML-schema's werkt, u het Enterprise Integration Pack gebruiken om de manier waarop uw bedrijf deze berichten verwerkt te verbeteren.

Het Enterprise Integration Pack bevat bijvoorbeeld de volgende mogelijkheden:

* [XML-validatie:](logic-apps-enterprise-integration-xml-validation.md)een binnenkomend of uitgaand XML-bericht valideren aan de hand van een specifiek schema.

* [XML-transformatie:](logic-apps-enterprise-integration-transform.md)converteer of pas een XML-bericht aan op basis van uw vereisten of de vereisten van een partner met behulp van kaarten.

* [Plat bestand codering en platte bestand decoderen:](logic-apps-enterprise-integration-flatfile.md)Coderen of decoderen van een plat bestand.

  SAP accepteert en verzendt bijvoorbeeld IDOC-bestanden in platte bestandsindeling. Veel integratieplatforms maken XML-berichten, waaronder Logic Apps. U dus een logische app maken die de platte bestandsencoder gebruikt om XML-bestanden te converteren naar platte bestanden.

* [XPath:](workflow-definition-language-functions-reference.md#xpath)Verrijk een bericht en haal specifieke eigenschappen uit het bericht. U vervolgens de geëxtraheerde eigenschappen gebruiken om het bericht naar een bestemming of een tusseneindpunt te leiden.

## <a name="sample"></a>Voorbeeld

[Implementeer een volledig operationele logische app](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-veter-pipeline) (GitHub-voorbeeld) met behulp van de XML-functies in Azure Logic Apps.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)
