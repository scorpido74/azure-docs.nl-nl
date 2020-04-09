---
title: Azure Single Sign Out SAML-protocol
description: In dit artikel wordt het SAML-protocol voor één malige uitloging beschreven in Azure Active Directory
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 07/19/2017
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.openlocfilehash: dbe21d020d5d01f24913b95587721403fa218cc8
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881262"
---
# <a name="single-sign-out-saml-protocol"></a>Enkelafmelden SAML-protocol

Azure Active Directory (Azure AD) ondersteunt het eenmalig afmeldprofiel van de SAML 2.0-webbrowser. Als eenmalige afmelding correct werkt, moet de **aanmeldingsURL** voor de toepassing expliciet worden geregistreerd bij Azure AD tijdens toepassingsregistratie. Azure AD gebruikt de log-URL om gebruikers om te leiden nadat ze zijn afgemeld.

In het volgende diagram ziet u de werkstroom van het eenmaligafmeldingsproces van Azure AD.

![Strooms-outs azure AD-](./media/single-sign-out-saml-protocol/active-directory-saml-single-sign-out-workflow.png)

## <a name="logoutrequest"></a>Uitmeldinggevraagd
De cloudservice `LogoutRequest` stuurt een bericht naar Azure AD om aan te geven dat een sessie is beëindigd. In het volgende `LogoutRequest` fragment ziet u een voorbeeldelement.

```
<samlp:LogoutRequest xmlns="urn:oasis:names:tc:SAML:2.0:metadata" ID="idaa6ebe6839094fe4abc4ebd5281ec780" Version="2.0" IssueInstant="2013-03-28T07:10:49.6004822Z" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.workaad.com</Issuer>
  <NameID xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
</samlp:LogoutRequest>
```

### <a name="logoutrequest"></a>Uitmeldinggevraagd
Voor `LogoutRequest` het element dat naar Azure AD wordt verzonden, zijn de volgende kenmerken vereist:

* `ID`- Dit identificeert het uithangverzoek. De waarde `ID` van moet niet beginnen met een getal. De typische praktijk is om **id** toe te wijzen aan de tekenreeksweergave van een GUID.
* `Version`- Stel de waarde van dit element in op **2,0**. Deze waarde is verplicht.
* `IssueInstant`- Dit `DateTime` is een tekenreeks met een coördinatenuniversele tijd (UTC) waarde en [retourformaat ("o")](https://msdn.microsoft.com/library/az4se3k1.aspx). Azure AD verwacht een waarde van dit type, maar handhaaft deze niet.

### <a name="issuer"></a>Verlener
Het `Issuer` element `LogoutRequest` in een moet exact overeenkomen met een van de **ServicePrincipalNames** in de cloudservice in Azure AD. Dit is meestal ingesteld op de URI van de **app-id** die is opgegeven tijdens de registratie van toepassingen.

### <a name="nameid"></a>Naam-ID
De waarde `NameID` van het element `NameID` moet exact overeenkomen met de gebruiker die wordt afgemeld.

## <a name="logoutresponse"></a>Reactie op afmelden
Azure AD `LogoutResponse` verzendt een `LogoutRequest` in reactie op een element. Het volgende fragment `LogoutResponse`toont een voorbeeld .

```
<samlp:LogoutResponse ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
</samlp:LogoutResponse>
```

### <a name="logoutresponse"></a>Reactie op afmelden
Azure AD `ID`stelt `Version` `IssueInstant` de waarden `LogoutResponse` en waarden in het element in. Het stelt `InResponseTo` het element ook `ID` in op `LogoutRequest` de waarde van het kenmerk van het dat de reactie ontlokte.

### <a name="issuer"></a>Verlener
Azure AD stelt `https://login.microsoftonline.com/<TenantIdGUID>/` deze \<waarde in op de plaats waar TenantIdGUID> de tenant-id van de Azure AD-tenant is.

Als u de `Issuer` waarde van het element wilt evalueren, gebruikt u de waarde van de **App ID URI** die tijdens de registratie van de toepassing wordt verstrekt.

### <a name="status"></a>Status
Azure AD `StatusCode` gebruikt het `Status` element in het element om het succes of het mislukken van afmelding aan te geven. Wanneer de uitmeldingspoging `StatusCode` mislukt, kan het element ook aangepaste foutmeldingen bevatten.
