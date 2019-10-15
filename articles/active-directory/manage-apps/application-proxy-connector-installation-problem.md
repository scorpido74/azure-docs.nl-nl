---
title: Probleem bij het installeren van de agent connector voor de toepassings proxy | Microsoft Docs
description: Problemen oplossen die u mogelijk ondervindt bij het installeren van de agent connector van de toepassings proxy
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: a35558b81d064680981bcf403a3584e3a3d00e4f
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/14/2019
ms.locfileid: "72311740"
---
# <a name="problem-installing-the-application-proxy-agent-connector"></a>Probleem bij het installeren van de connector voor de toepassingsproxyagent

Micro soft AAD Application proxy connector is een intern domein onderdeel dat uitgaande verbindingen gebruikt om verbinding te maken tussen het eind punt in de cloud dat beschikbaar is voor het interne domein.

## <a name="general-problem-areas-with-connector-installation"></a>Algemene probleem gebieden met installatie van connector

Wanneer de installatie van een connector mislukt, is de hoofd oorzaak meestal een van de volgende gebieden:

1.  **Connectiviteit** : om een geslaagde installatie te volt ooien, moet de nieuwe connector de eigenschappen van de vertrouwens relatie in de toekomst registreren en instellen. Dit wordt gedaan door verbinding te maken met de service voor de AAD-toepassings proxy.

2.  Het maken van een **vertrouwens relatie** : de nieuwe connector maakt een zelfondertekend certificaat en registreert zich bij de Cloud service.

3.  **Verificatie van de beheerder** : tijdens de installatie moet de gebruiker beheerders referenties opgeven om de installatie van de connector te volt ooien.

> [!NOTE]
> De installatie logboeken van de connector vindt u in de map% TEMP% en biedt aanvullende informatie over wat een installatie fout veroorzaakt.

## <a name="verify-connectivity-to-the-cloud-application-proxy-service-and-microsoft-login-page"></a>Controleer de verbinding met de Cloud toepassings proxy-service en de micro soft-aanmeldings pagina

**Doel stelling:** Controleer of de connector computer verbinding kan maken met het registratie-eind punt van de AAD-toepassings proxy en de aanmeldings pagina van micro soft.

1.  Voer op de connector server een poort test uit met behulp van [Telnet](https://docs.microsoft.com/windows-server/administration/windows-commands/telnet) of een ander hulp programma voor poort testen om te controleren of de poorten 443 en 80 zijn geopend.

2.  Als een van deze poorten niet is geslaagd, controleert u of de firewall of de back-end-proxy toegang heeft tot de vereiste domeinen en poorten Zie, [uw on-premises omgeving voorbereiden](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment).

3.  Open een browser (tabblad afzonderlijk) en ga naar de volgende webpagina: <https://login.microsoftonline.com>, zorg ervoor dat u zich kunt aanmelden bij deze pagina.

## <a name="verify-machine-and-backend-components-support-for-application-proxy-trust-cert"></a>De ondersteuning voor de machine en back-end-onderdelen controleren voor het certificaat van de toepassings proxy Trust

**Doel stelling:** Controleer of de connector computer, back-end proxy en firewall het certificaat kunnen ondersteunen dat door de connector is gemaakt voor toekomstige vertrouwens relaties.

>[!NOTE]
>De connector probeert een SHA512 gebruikt-certificaat te maken dat wordt ondersteund door TLS 1.2. Als de computer of de back-end firewall en proxy geen ondersteuning bieden voor TLS 1.2, mislukt de installatie.
>
>

**Om het probleem op te lossen:**

1.  Controleren of de computer TLS 1.2 ondersteunt: alle Windows-versies na 2012 R2 moeten TLS 1,2 ondersteunen. Als uw connector computer van een versie van 2012 R2 of eerder is, controleert u of de volgende Kb's zijn geïnstalleerd op de computer: <https://support.microsoft.com/help/2973337/sha512-is-disabled-in-windows-when-you-use-tls-1.2>

2.  Neem contact op met uw netwerk beheerder en vraag om te controleren of de back-end-proxy en firewall SHA512 gebruikt niet blok keren voor uitgaand verkeer.

## <a name="verify-admin-is-used-to-install-the-connector"></a>Controleren of beheerder wordt gebruikt voor het installeren van de connector

**Doel stelling:** Controleer of de gebruiker die de connector probeert te installeren, een beheerder met de juiste referenties is. Op dit moment moet de gebruiker ten minste een toepassings beheerder zijn om de installatie te kunnen volt ooien.

**Controleren of de referenties juist zijn:**

Verbinding maken met <https://login.microsoftonline.com> en dezelfde referenties gebruiken. Zorg ervoor dat de aanmelding is geslaagd. U kunt de gebruikersrol controleren door naar **Azure Active Directory** - @ no__t-2- **gebruikers en-groepen** - @ No__t-5 **alle gebruikers**te gaan. 

Selecteer uw gebruikers account en vervolgens ' Directory-rol ' in het resulterende menu. Controleer of de geselecteerde rol ' toepassings beheerder ' is. Als u in deze stappen geen toegang hebt tot een van de pagina's, hebt u niet de vereiste rol.

## <a name="next-steps"></a>Volgende stappen
[Azure AD-toepassingsproxy-connectors begrijpen](application-proxy-connectors.md)
