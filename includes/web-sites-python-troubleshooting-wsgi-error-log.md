---
title: bestand opnemen
description: bestand opnemen
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/11/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 020e59f029b09f3c7656f67039731e4141e68d31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67176639"
---
Als Python een fout tegenkomt tijdens het starten van uw toepassing, wordt alleen een eenvoudige foutpagina geretourneerd (bijvoorbeeld "De pagina kan niet worden weergegeven omdat er een interne serverfout is opgetreden".

Ga als u python-toepassingsfouten vastleggen:

1. Selecteer In de Azure-portal in uw web-app de optie **Instellingen**.
2. Selecteer op het tabblad **Instellingen** de optie **Toepassingsinstellingen**.
3. Voer **onder App-instellingen**het volgende sleutel-/waardepaar in:
    * Sleutel : WSGI_LOG
    * Waarde: D:\home\site\wwwroot\logs.txt (voer uw keuze van bestandsnaam in)

U ziet nu fouten in het bestand logs.txt in de map wwwroot.
