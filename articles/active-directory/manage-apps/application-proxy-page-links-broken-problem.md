---
title: Koppelingen op de pagina werken niet voor een toepassing voor toepassingsproxy
description: Problemen met verbroken koppelingen op application proxy-toepassingen die u hebt geïntegreerd met Azure AD oplossen
services: active-directory
author: msmimart
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca782b5a190704911472d70c414d12afa7e558f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430248"
---
# <a name="links-on-the-page-dont-work-for-an-application-proxy-application"></a>Koppelingen op de pagina werken niet voor een toepassing voor toepassingsproxy

Met dit artikel u oplossen waarom koppelingen op uw Azure Active Directory Application Proxy-toepassing niet correct werken.

## <a name="overview"></a>Overzicht 
Na het publiceren van een Application Proxy-app zijn de enige koppelingen die standaard in de toepassing werken koppelingen naar bestemmingen die zijn opgenomen in de gepubliceerde hoofd-URL. De koppelingen binnen de toepassingen werken niet, de interne URL voor de toepassing bevat waarschijnlijk niet alle bestemmingen van links binnen de toepassing.

**Waarom gebeurt dit?** Wanneer u op een koppeling in een toepassing klikt, probeert Application Proxy de URL op te lossen als een interne URL binnen dezelfde toepassing of als een extern beschikbare URL. Als de koppeling verwijst naar een interne URL die niet binnen dezelfde toepassing valt, behoort deze niet tot een van deze buckets en resulteert deze niet in een niet-gevonden fout.

## <a name="ways-you-can-resolve-broken-links"></a>Manieren waarop u verbroken koppelingen oplossen

Er zijn drie manieren om dit probleem op te lossen. De onderstaande keuzes worden weergegeven in toenemende complexiteit.

1.  Zorg ervoor dat de interne URL een hoofdmap is die alle relevante koppelingen voor de toepassing bevat. Hierdoor kunnen alle koppelingen worden opgelost als inhoud die binnen dezelfde toepassing wordt gepubliceerd.

    Als u de interne URL wijzigt maar de bestemmingspagina voor gebruikers niet wilt wijzigen, wijzigt u de URL van de startpagina in de eerder gepubliceerde interne URL. Dit kan worden gedaan door naar "Azure Active Directory" -&gt; App-registraties -&gt; selecteer de toepassing -&gt; Branding. In de sectie Branding ziet u het veld 'URL van de startpagina', dat u aanpassen aan de gewenste bestemmingspagina. Als u nog steeds de ervaring met oudere app-registraties gebruikt, wordt op het tabblad Eigenschappen de details van de URL van de startpagina weergegeven. 
    
    > [!IMPORTANT]
    > Als u bovenstaande wijzigingen wilt aanbrengen, hebt u rechten nodig om toepassingsobjecten in Azure AD te wijzigen. De gebruiker moet de rol [Toepassingsbeheerder](../users-groups-roles/roles-delegate-app-roles.md#assign-built-in-application-admin-roles) toegewezen krijgen die toepassingsmodificaionrechten in Azure AD aan de gebruiker verleent.
    >

2.  Als uw toepassingen volledig gekwalificeerde domeinnamen (FQDN's) gebruiken, gebruikt u [aangepaste domeinen](application-proxy-configure-custom-domain.md) om uw toepassingen te publiceren. Met deze functie kan dezelfde URL zowel intern als extern worden gebruikt.

    Deze optie zorgt ervoor dat de koppelingen in uw toepassing extern toegankelijk zijn via Application Proxy, omdat de koppelingen binnen de toepassing naar interne URL's ook extern worden herkend. Alle links moeten nog steeds deel uitmaken van een gepubliceerde toepassing. Met deze optie hoeven de koppelingen echter niet tot dezelfde toepassing te behoren en kunnen ze tot meerdere toepassingen behoren.

3.  Als geen van deze opties haalbaar is, zijn er meerdere opties voor het inschakelen van inline linkvertaling. Deze opties omvatten het gebruik van de Intune Managed Browser, My Apps extensie, of het gebruik van de link vertaling instelling op uw toepassing. Zie [Hardcoded links omleiden voor apps die zijn gepubliceerd met Azure AD Application Proxy voor](application-proxy-configure-hard-coded-link-translation.md)meer informatie over elk van deze opties en hoe u deze inschakelen.

## <a name="next-steps"></a>Volgende stappen
[Werken met bestaande on-premises proxyservers](application-proxy-configure-connectors-with-proxy-servers.md)

