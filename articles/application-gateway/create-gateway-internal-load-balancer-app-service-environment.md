---
title: Problemen met een toepassingsgateway in Azure oplossen – ILB ASE | Microsoft Documenten
description: Meer informatie over het oplossen van problemen met een toepassingsgateway met behulp van een interne load balancer met een app-serviceomgeving in Azure
services: vpn-gateway
documentationCenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/06/2018
ms.author: genli
ms.openlocfilehash: 9c3216af283ebd9d84a5469d4d50d18c19f67534
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71121953"
---
# <a name="back-end-server-certificate-is-not-whitelisted-for-an-application-gateway-using-an-internal-load-balancer-with-an-app-service-environment"></a>Back-endservercertificaat wordt niet op de witte lijst voor een toepassingsgateway weergegeven met behulp van een interne load balancer met een app-serviceomgeving

In dit artikel wordt het volgende probleem opgelost: een certificaat wordt niet op de witte lijst weergegeven wanneer u een toepassingsgateway maakt met behulp van een ILB (Internal Load Balancer) samen met een App Service Environment (ASE) aan de achterkant bij het gebruik van end-to-end SSL in Azure.

## <a name="symptoms"></a>Symptomen

Wanneer u een toepassingsgateway maakt met behulp van een ILB met een ASE aan de achterkant, kan de back-endserver ongezond worden. Dit probleem treedt op als het verificatiecertificaat van de toepassingsgateway niet overeenkomt met het geconfigureerde certificaat op de back-endserver. Zie het volgende scenario als voorbeeld:

**Configuratie van toepassingsgateway:**

- **Luisteraar:** Meerdere plaatsen
- **Poort:** 443
- **Hostname:** test.appgwtestase.com
- **SSL-certificaat:** CN=test.appgwtestase.com
- **Backend Pool:** IP-adres of FQDN
- **IP-adres:**: 10.1.5.11
- **HTTP-instellingen:** HTTPS
- **Haven:**: 443
- **Aangepaste sonde:** Hostname – test.appgwtestase.com
- **Verificatiecertificaat:** .cer van test.appgwtestase.com
- **Backend Gezondheid:** Niet in orde : backendservercertificaat staat niet op de witte lijst met Application Gateway.

**ASE-configuratie:**

- **ILB IP:** 10.1.5.11
- **Domeinnaam:** appgwtestase.com
- **App-service:** test.appgwtestase.com
- **SSL-binding:** SNI SSL – CN=test.appgwtestase.com

Wanneer u de toepassingsgateway opent, ontvangt u het volgende foutbericht omdat de back-endserver niet in orde is:

**502 – Webserver heeft een ongeldige reactie ontvangen terwijl deze fungeert als gateway- of proxyserver.**

## <a name="solution"></a>Oplossing

Wanneer u geen hostnaam gebruikt om toegang te krijgen tot een HTTPS-website, retourneert de back-endserver het geconfigureerde certificaat op de standaardwebsite, voor het geval SNI is uitgeschakeld. Voor een ILB ASE is het standaardcertificaat afkomstig van het ILB-certificaat. Als er geen geconfigureerde certificaten voor de ILB zijn, is het certificaat afkomstig van het ASE App-certificaat.

Wanneer u een volledig gekwalificeerde domeinnaam (FQDN) gebruikt om toegang te krijgen tot de ILB, retourneert de back-endserver het juiste certificaat dat is geüpload in de HTTP-instellingen. Als dat niet het geval is, overweeg dan de volgende opties:

- Gebruik FQDN in de back-endpool van de toepassingsgateway om naar het IP-adres van de ILB te wijzen. Deze optie werkt alleen als u een privé-DNS-zone of een aangepaste DNS-instelling hebt geconfigureerd. Anders moet u een A-record maken voor een openbare DNS.

- Gebruik het geüploade certificaat op de ILB of het standaardcertificaat (ILB-certificaat) in de HTTP-instellingen. De toepassingsgateway krijgt het certificaat wanneer deze toegang krijgt tot het IP-adres van de ILB voor de sonde.

- Gebruik een wildcardcertificaat op de ILB en de back-endserver, zodat voor alle websites het certificaat gebruikelijk is. Deze oplossing is echter alleen mogelijk in het geval van subdomeinen en niet als elk van de websites verschillende hostnamen vereist.

- Schakel de optie **Gebruik voor App-service** voor de toepassingsgateway uit voor het geval u het IP-adres van de ILB gebruikt.

Om de overhead te verminderen, u het ILB-certificaat uploaden in de HTTP-instellingen om het sondepad te laten werken. (Deze stap is alleen voor whitelisting. Het zal niet worden gebruikt voor SSL-communicatie.) U het ILB-certificaat ophalen door toegang te krijgen tot de ILB met het IP-adres vanuit uw browser op HTTPS en vervolgens het SSL-certificaat te exporteren in een basis-64 gecodeerde CER-indeling en het certificaat te uploaden naar de respectievelijke HTTP-instellingen.

## <a name="need-help-contact-support"></a>Hebt u hulp nodig? Contact opnemen met ondersteuning

Als u nog steeds hulp nodig hebt, neemt u [contact op met de ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om uw probleem snel op te lossen.
