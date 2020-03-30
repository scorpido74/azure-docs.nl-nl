---
title: Paginasjablonen in Azure API Management | Microsoft Documenten
description: Meer informatie over het aanpassen van de inhoud van ontwikkelaarsportalpagina's met behulp van een set sjablonen in Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: e57df269-1019-4b74-b74d-53155b809d59
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: ce56c406c884471c445b25343d5c42f9edcbe4c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249540"
---
# <a name="page-templates-in-azure-api-management"></a>Paginasjablonen in Azure API-beheer
Azure API Management biedt u de mogelijkheid om de inhoud van ontwikkelaarsportalpagina's aan te passen met behulp van een reeks sjablonen die de inhoud ervan configureren. Met behulp van [DotLiquid](http://dotliquidmarkup.org/) syntaxis en de editor van uw keuze, zoals [DotLiquid voor ontwerpers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers), en een meegeleverde set van gelokaliseerde [string bronnen,](api-management-template-resources.md#strings) [Glyph middelen,](api-management-template-resources.md#glyphs)en [Pagina besturingselementen](api-management-page-controls.md), hebt u grote flexibiliteit om de inhoud van de pagina's te configureren zoals u dat wilt met behulp van deze sjablonen.  
  
 Met de sjablonen in deze sectie u de inhoud van het aanmelden, aanmelden en pagina's die niet zijn gevonden in de ontwikkelaarsportal aanpassen.  
  
-   [Aanmelden](#SignIn)  
  
-   [Aanmelden](#SignUp)  
  
-   [Pagina niet gevonden](#PageNotFound)  
  
> [!NOTE]
>  Standaardsjablonen voor voorbeelden zijn opgenomen in de volgende documentatie, maar kunnen worden gewijzigd als gevolg van continue verbeteringen. U de standaardstandaardsjablonen in de ontwikkelaarsportal bekijken door naar de gewenste afzonderlijke sjablonen te navigeren. Zie [De API Management-ontwikkelaarsportal aanpassen met sjablonen](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/)voor meer informatie over het werken met sjablonen.  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="sign-in"></a><a name="SignIn"></a>Aanmelden  
 Met **de aanmeldingssjabloon** u de aanmeldingspagina aanpassen in de ontwikkelaarsportal.  
  
 ![Aanmeldingspagina](./media/api-management-page-templates/APIM-Sign-In-Page-Developer-Portal-Templates.png "APIM-portalsjablonen voor paginaontwikkelaars")  
  
### <a name="default-template"></a>Standaardsjabloon  
  
```xml  
<h2 class="text-center">{% localized "SigninStrings|WebAuthenticationSigninTitle" %}</h2>  
{% if registrationEnabled == true %}  
<p class="text-center">{% localized "SigninStrings|WebAuthenticationNotAMember" %}</p>  
{% endif %}  
  
<div class="row center-block ap-idp-container">  
  <div class="col-md-6">  
    {% if registrationEnabled == true %}  
        <p>{% localized "SigninStrings|WebAuthenticationSigininWithPassword" %}</p>  
    <basic-SignIn></basic-SignIn>  
    {% endif %}  
  </div>  
  
    {% if registrationEnabled != true and providers.size == 0 %}  
        {% localized "ProviderInfoStrings|TextboxExternalIdentitiesDisabled" %}  
  {% else %}  
        {% if providers.size > 0 %}  
      <div class="col-md-6">  
            <div class="providers-list">  
                <p class="text-left">  
                {% if registrationEnabled == true %}  
                    {% localized "ProviderInfoStrings|TextboxExternalIdentitiesSigninInvitation" %}  
                {% else %}  
                    {% localized "ProviderInfoStrings|TextboxExternalIdentitiesSigninInvitationPrimary" %}  
                {% endif %}  
                </p>  
        <providers></providers>  
            </div>  
    </div>  
        {% endif %}  
    {% endif %}  
  
  {% if userRegistrationTermsEnabled == true %}  
    <div class="col-md-6">  
        <div id="terms" class="modal" role="dialog" tabindex="-1">  
            <div class="modal-dialog">  
                <div class="modal-content">  
                    <div class="modal-header">  
                        <h4 class="modal-title">{% localized "SigninResources|DialogHeadingTermsOfUse" %}</h4>  
                    </div>  
                    <div class="modal-body break-all">{{userRegistrationTerms}}</div>  
                    <div class="modal-footer">  
                        <button type="button" class="btn btn-default" data-dismiss="modal">{% localized "CommonStrings|ButtonLabelClose" %}</button>  
                    </div>  
                </div>  
            </div>  
        </div>  
        <p>{% localized "SigninResources|TextblockUserRegistrationTermsProvided" %}</p>  
    </div>  
    {% endif %}  
</div>  
```  
  
### <a name="controls"></a>Besturingselementen  
 Deze sjabloon kan de volgende [paginabesturingselementen](api-management-page-controls.md)gebruiken .  
  
-   [basis-signin](api-management-page-controls.md#basic-signin)  
  
-   [Providers](api-management-page-controls.md#providers)  
  
### <a name="data-model"></a>Gegevensmodel  
 [Aanmelden door](api-management-template-data-model-reference.md#UseSignIn) gebruiker.  
  
### <a name="sample-template-data"></a>Voorbeeldsjabloongegevens  
  
```json  
{
    "Email": null,
    "Password": null,
    "ReturnUrl": null,
    "RememberMe": false,
    "RegistrationEnabled": true,
    "DelegationEnabled": false,
    "DelegationUrl": null,
    "SsoSignUpUrl": null,
    "AuxServiceUrl": "https://portal.azure.com/#resource/subscriptions/{subscription ID}/resourceGroups/Api-Default-West-US/providers/Microsoft.ApiManagement/service/contoso5",
    "Providers": [  
        {  
            "Properties": {  
                "AuthenticationType": "Aad",  
                "Caption": "Azure Active Directory"  
            },  
            "AuthenticationType": "Aad",  
            "Caption": "Azure Active Directory"  
        }  
        ],
    "UserRegistrationTerms": null,
    "UserRegistrationTermsEnabled": false
}
```  
  
##  <a name="sign-up"></a><a name="SignUp"></a>Inschrijven  
 Met de **sjabloon aanmelden** u de aanmeldingspagina aanpassen in de ontwikkelaarsportal.  
  
 ![Aanmeldingspagina](./media/api-management-page-templates/APIM-Sign-Up-Page-Developer-Portal-Templates.png "APIM-portalsjablonen voor voor pagina-ontwikkelaars")  
  
### <a name="default-template"></a>Standaardsjabloon  
  
```xml  
<h2 class="text-center">{% localized "SignupStrings|PageTitleSignup" %}</h2>  
<p class="text-center">  
  {% localized "SignupStrings|WebAuthenticationAlreadyAMember" %} <a href="/signin">{% localized "SignupStrings|WebAuthenticationSigninNow" %}</a>  
</p>  
  
<div class="row center-block ap-idp-container">  
  <div class="col-md-6">  
    <p>{% localized "SignupStrings|WebAuthenticationCreateNewAccount" %}</p>  
    <sign-up></sign-up>  
  </div>  
</div>  
```  
  
### <a name="controls"></a>Besturingselementen  
 Deze sjabloon kan de volgende [paginabesturingselementen](api-management-page-controls.md)gebruiken .  
  
-   [Aanmelden](api-management-page-controls.md#sign-up)  
  
### <a name="data-model"></a>Gegevensmodel  
 [Entiteit voor het aanmelden van](api-management-template-data-model-reference.md#UserSignUp) de gebruiker.  
  
### <a name="sample-template-data"></a>Voorbeeldsjabloongegevens  
  
```json  
{  
    "PasswordConfirm": null,  
    "Password": null,  
    "PasswordVerdictLevel": 0,  
    "UserRegistrationTerms": null,  
    "UserRegistrationTermsOptions": 0,  
    "ConsentAccepted": false,  
    "Email": null,  
    "FirstName": null,  
    "LastName": null,  
    "UserData": null,  
    "NameIdentifier": null,  
    "ProviderName": null  
}  
```  
  
##  <a name="page-not-found"></a><a name="PageNotFound"></a>Pagina niet gevonden  
 Met de **pagina die niet is gevonden,** u de pagina die niet is gevonden in de ontwikkelaarsportal aanpassen.  
  
 ![Niet gevonden pagina](./media/api-management-page-templates/APIM-Not-Found-Page-Developer-Portal-Templates.png "APIM-portalsjablonen voor paginaontwikkelaars")  
  
### <a name="default-template"></a>Standaardsjabloon  
  
```xml  
<h2>{% localized "NotFoundStrings|PageTitleNotFound" %}</h2>  
  
<h3>{% localized "NotFoundStrings|TitlePotentialCause" %}</h3>  
<ul>  
  <li>{% localized "NotFoundStrings|TextblockPotentialCauseOldLink" %}</li>  
  <li>{% localized "NotFoundStrings|TextblockPotentialCauseMisspelledUrl" %}</li>  
</ul>  
  
<h3>{% localized "NotFoundStrings|TitlePotentialSolution" %}</h3>  
<ul>  
  <li>{% localized "NotFoundStrings|TextblockPotentialSolutionRetype" %}</li>  
  <li>  
    {% capture textPotentialSolutionStartOver %}{% localized "NotFoundStrings|TextblockPotentialSolutionStartOver" %}{% endcapture %}  
    {% capture homeLink %}<a href="/">{% localized "NotFoundStrings|LinkLabelHomePage" %}</a>{% endcapture %}  
    {% assign replaceString = '{0}' %}  
  
    {{ textPotentialSolutionStartOver | replace : replaceString, homeLink }}  
  </li>  
</ul>  
  
<p>  
  {% capture textReportProblem %}{% localized "NotFoundStrings|TextReportProblem" %}{% endcapture %}  
  {% capture emailLink %}<a href="mailto:apimgmt@microsoft.com" target="_self" title="API Management Support">{% localized "NotFoundStrings|LinkLabelSendUsEmail" %}</a>{% endcapture %}  
  {% assign replaceString = '{0}' %}  
  
  {{ textReportProblem | replace : replaceString, emailLink }}  
</p>  
```  
  
### <a name="controls"></a>Besturingselementen  
 Deze sjabloon mag geen [paginabesturingselementen](api-management-page-controls.md)gebruiken.  
  
### <a name="data-model"></a>Gegevensmodel  
  
|Eigenschap|Type|Beschrijving|  
|--------------|----------|-----------------|  
|referenceCode|tekenreeks|Code gegenereerd als deze pagina is weergegeven als gevolg van een interne fout.|  
|errorCode (errorCode)|tekenreeks|Code gegenereerd als deze pagina is weergegeven als gevolg van een interne fout.|  
|emailBody|tekenreeks|E-mailbody gegenereerd als deze pagina is weergegeven als gevolg van een interne fout.|  
|requestedUrl|tekenreeks|De URL die is aangevraagd toen de pagina niet is gevonden.|  
|verwijzerUrl|tekenreeks|De URL van de verwijzer naar de gevraagde URL.|  
  
### <a name="sample-template-data"></a>Voorbeeldsjabloongegevens  
  
```json  
{  
    "referenceCode": null,  
    "errorCode": null,  
    "emailBody": null,  
    "requestedUrl": "https://contoso5.portal.azure-api.net:443/NotFoundPage?startEditTemplate=NotFoundPage",  
    "referrerUrl": "https://contoso5.portal.azure-api.net/signup?startEditTemplate=SignUpTemplate"  
}  
```

## <a name="next-steps"></a>Volgende stappen
Zie [De API Management-ontwikkelaarsportal aanpassen met sjablonen](api-management-developer-portal-templates.md)voor meer informatie over het werken met sjablonen.
