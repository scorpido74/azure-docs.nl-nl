---
title: Selfservice wachtwoord opnieuw instellen in Azure Active Directory B2C | Microsoft Documenten
description: Laat zien hoe u selfservicewachtwoordopnieuw instellen voor uw klanten in Azure Active Directory B2C
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d6dad52c8a3e63c64bb8e0e0030e8c50b5bab42c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183105"
---
# <a name="set-up-self-service-password-reset-for-your-customers"></a>Selfservice-wachtwoordopnieuw instellen voor uw klanten

Met de selfservice-functie voor het opnieuw instellen van wachtwoorden kunnen uw klanten die zich hebben aangemeld voor lokale accounts hun wachtwoorden zelf opnieuw instellen. Dit vermindert de last en last voor uw ondersteunend personeel aanzienlijk, vooral als uw toepassing miljoenen klanten heeft die het regelmatig gebruiken. Momenteel is het gebruik van een geverifieerd e-mailadres de enige ondersteunde herstelmethode.

> [!NOTE]
> Dit artikel is van toepassing op selfservice wachtwoordreset die wordt gebruikt in de context van de **V1-aanmeldingsstroom,** waarbij **U aangeeft** aan lokale accounts als identiteitsprovider. Als u volledig aanpasbare gebruikersstromen voor het opnieuw instellen van wachtwoorden vanuit uw app nodig hebt, [raadpleegt](user-flow-overview.md)u dit artikel .
>
>

Standaard is de wachtwoordreset van uw map niet ingeschakeld. Gebruik de volgende stappen om het in te schakelen:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/) als abonnementsbeheerder. Dit is hetzelfde werk- of schoolaccount of hetzelfde Microsoft-account dat u hebt gebruikt om uw directory te maken.
2. Open **Azure Active Directory** (in de navigatiebalk aan de linkerkant).
3. Schuif omlaag op het optieblad en selecteer **Wachtwoord opnieuw instellen**.
4. **Stel Self service wachtwoord reset ingeschakeld** om **Alle**.
5. Klik bovenaan de pagina op **Opslaan**. Je bent klaar!

Als u wilt testen, gebruikt u de functie Nu uitvoeren op een aanmeldingsstroom met lokale accounts als identiteitsprovider. Klik op de aanmeldingspagina voor het lokale account (waar u een e-mailadres en wachtwoord invoert, of een gebruikersnaam en wachtwoord) op **Toegang tot uw account?**

> [!NOTE]
> De selfservice wachtwoord reset pagina's kunnen worden aangepast met behulp van de [bedrijfsbranding functie](../active-directory/fundamentals/customize-branding.md).
>
>

