---
title: Azure-SAML-protocol voor eenmalige afmelding | Microsoft Docs
description: In dit artikel wordt het SAML-protocol voor eenmalige afmeldingen in Azure Active Directory beschreven
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 0e4aa75d-d1ad-4bde-a94c-d8a41fb0abe6
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/19/2017
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.openlocfilehash: 95d3deff73ce357f012b15a7fc1cfa3decdb4bda
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701362"
---
# <a name="single-sign-out-saml-protocol"></a>SAML-protocol voor eenmalige afmelding

Azure Active Directory (Azure AD) ondersteunt de SAML 2,0-webbrowser voor eenmalige afmelding. Voor een juiste werking van eenmalige afmelding moet de **LogoutURL** voor de toepassing expliciet worden geregistreerd bij Azure ad tijdens de registratie van de toepassing. Azure AD gebruikt de LogoutURL om gebruikers te omleiden nadat ze zijn afgemeld.

In het volgende diagram ziet u de werk stroom van het proces voor eenmalige aanmelding van Azure AD.

![Azure AD-werk stroom voor eenmalige afmelding](./media/single-sign-out-saml-protocol/active-directory-saml-single-sign-out-workflow.png)

## <a name="logoutrequest"></a>LogoutRequest
De Cloud service stuurt een `LogoutRequest` bericht naar Azure AD om aan te geven dat een sessie is beëindigd. Het volgende fragment toont een `LogoutRequest` element van een voor beeld.

```
<samlp:LogoutRequest xmlns="urn:oasis:names:tc:SAML:2.0:metadata" ID="idaa6ebe6839094fe4abc4ebd5281ec780" Version="2.0" IssueInstant="2013-03-28T07:10:49.6004822Z" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.workaad.com</Issuer>
  <NameID xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
</samlp:LogoutRequest>
```

### <a name="logoutrequest"></a>LogoutRequest
Het `LogoutRequest`-element dat naar Azure AD wordt verzonden, vereist de volgende kenmerken:

* `ID`: Hiermee wordt de afmeldings aanvraag geïdentificeerd. De waarde van `ID` mag niet beginnen met een getal. De gang bare procedure is het toevoegen van **id** aan de teken reeks representatie van een GUID.
* `Version`: Stel de waarde van dit element in op **2,0**. Deze waarde is verplicht.
* `IssueInstant`: dit is een `DateTime` teken reeks met een UTC-waarde (Universal Time Coordinate) en een notatie voor de [retour Tour ("o")](https://msdn.microsoft.com/library/az4se3k1.aspx). Azure AD verwacht een waarde van dit type, maar dwingt dit niet af.

### <a name="issuer"></a>Verlener
Het `Issuer`-element in een `LogoutRequest` moet exact overeenkomen met een van de **ServicePrincipalNames** in de Cloud service in azure AD. Dit is normaal gesp roken ingesteld op de **App-ID-URI** die is opgegeven tijdens de registratie van de toepassing.

### <a name="nameid"></a>Meid
De waarde van het `NameID` element moet exact overeenkomen met de `NameID` van de gebruiker die wordt afgemeld.

## <a name="logoutresponse"></a>LogoutResponse
Azure AD verzendt een `LogoutResponse` als reactie op een `LogoutRequest`-element. Het volgende fragment toont een voor beeld `LogoutResponse`.

```
<samlp:LogoutResponse ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
</samlp:LogoutResponse>
```

### <a name="logoutresponse"></a>LogoutResponse
Azure AD stelt de `ID`, `Version` en `IssueInstant` waarden in het element `LogoutResponse` in. Het `InResponseTo`-element wordt ook ingesteld op de waarde van het kenmerk `ID` van de `LogoutRequest` die het antwoord heeft aangevallen.

### <a name="issuer"></a>Verlener
Deze waarde wordt door Azure AD ingesteld op `https://login.microsoftonline.com/<TenantIdGUID>/` waarbij \<TenantIdGUID > de Tenant-ID is van de Azure AD-Tenant.

Als u de waarde van het element `Issuer` wilt evalueren, gebruikt u de waarde van de URI van de **App-ID** die tijdens de registratie van de toepassing is opgenomen.

### <a name="status"></a>Status
Azure AD gebruikt het `StatusCode`-element in het `Status`-element om aan te geven dat de afmelding is geslaagd of mislukt. Wanneer de aanmeldings poging mislukt, kan het `StatusCode`-element ook aangepaste fout berichten bevatten.
