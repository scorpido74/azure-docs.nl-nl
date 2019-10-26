---
title: Een SharePoint-site met Application Insights bewaken
description: Een nieuwe toepassing bewaken met een nieuwe instrumentatiesleutel
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 07/11/2018
ms.openlocfilehash: 271ef590d7cdbefc824a7f80aef110b0c08b6d6d
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899873"
---
# <a name="monitor-a-sharepoint-site-with-application-insights"></a>Een SharePoint-site met Application Insights bewaken
Azure Application Insights bewaakt de beschikbaarheid, de prestaties en het gebruik van uw apps. Hier wordt uitgelegd hoe u dit kunt instellen voor een SharePoint-site.

## <a name="create-an-application-insights-resource"></a>Een Application Insights-resource maken
Maak in de [Azure Portal](https://portal.azure.com) een nieuwe Application Insights-resource. Kies ASP.NET als het toepassingstype.

![Op Eigenschappen klikken, de sleutel selecteren en op Ctrl + C drukken](./media/sharepoint/001.png)

In het venster dat wordt geopend, ziet u prestatie- en gebruiksgegevens over uw app. Als u deze weer wilt openen wanneer u zich de volgende keer aanmeldt bij Azure, moet u de tegel hiervoor zoeken in het beginscherm. U kunt ook op Bladeren klikken om te zoeken.

## <a name="add-the-script-to-your-web-pages"></a>Het script toevoegen aan uw webpagina's

```HTML
<!-- 
To collect user behavior analytics tools about your application, 
insert the following script into each page you want to track.
Place this code immediately before the closing </head> tag,
and before any other scripts. Your first data will appear 
automatically in just a few seconds.
-->
<script type="text/javascript">
var appInsights=window.appInsights||function(a){
  function b(a){c[a]=function(){var b=arguments;c.queue.push(function(){c[a].apply(c,b)})}}var c={config:a},d=document,e=window;setTimeout(function(){var b=d.createElement("script");b.src=a.url||"https://az416426.vo.msecnd.net/scripts/a/ai.0.js",d.getElementsByTagName("script")[0].parentNode.appendChild(b)});try{c.cookie=d.cookie}catch(a){}c.queue=[];for(var f=["Event","Exception","Metric","PageView","Trace","Dependency"];f.length;)b("track"+f.pop());if(b("setAuthenticatedUserContext"),b("clearAuthenticatedUserContext"),b("startTrackEvent"),b("stopTrackEvent"),b("startTrackPage"),b("stopTrackPage"),b("flush"),!a.disableExceptionTracking){f="onerror",b("_"+f);var g=e[f];e[f]=function(a,b,d,e,h){var i=g&&g(a,b,d,e,h);return!0!==i&&c["_"+f](a,b,d,e,h),i}}return c
  }({
      instrumentationKey:"<your instrumentation key>"
  });
  
window.appInsights=appInsights,appInsights.queue&&0===appInsights.queue.length&&appInsights.trackPageView();
</script>
```

Voeg het script vlak voor de &lt;/Head-code&gt; van elke pagina die u wilt bijhouden. Als uw website een basis pagina heeft, kunt u het script daar plaatsen. In een ASP.NET MVC-project plaatst u deze bijvoorbeeld in View\Shared\_Layout.cshtml

Het script bevat de instrumentatiesleutel die de telemetrie naar uw Application Insights-resource stuurt.

### <a name="add-the-code-to-your-site-pages"></a>De code aan de pagina's toevoegen
#### <a name="on-the-master-page"></a>Op de basispagina
Als u de basispagina van de site kunt bewerken, biedt dit bewaking voor elke pagina op de site.

Bekijk de basispagina en bewerk deze met SharePoint Designer of een andere editor.

![](./media/sharepoint/03-master.png)

Voeg de code toe vlak voor de tag </head>. 

![](./media/sharepoint/04-code.png)

#### <a name="or-on-individual-pages"></a>Of op afzonderlijke pagina’s
Als u een beperkt aantal pagina's wilt bewaken, voegt u het script afzonderlijk toe aan elke pagina. 

Voeg een webonderdeel in en sluit het codefragment hierin in.

![](./media/sharepoint/05-page.png)

## <a name="view-data-about-your-app"></a>Gegevens over uw app weergeven
Implementeer uw app opnieuw.

Ga terug naar uw toepassingsblade in de [Azure Portal](https://portal.azure.com).

De eerste gebeurtenissen worden weergegeven bij Zoeken. 

![](./media/sharepoint/09-search.png)

Klik na een paar seconden op Vernieuwen als u meer gegevens verwacht.

## <a name="capturing-user-id"></a>Gebruikers-id vastleggen
Met het standaardcodefragment van de webpagina wordt niet de gebruikers-id van SharePoint vastgelegd, maar u kunt dit doen met een kleine wijziging.

1. Kopieer de instrumentatiesleutel van uw app van de vervolgkeuzelijst Essentials in Application Insights. 

    ![](./media/sharepoint/02-props.png)

1. Vervang de instrumentatiesleutel door XXXX in het onderstaande fragment. 
2. Sluit dit script in in uw SharePoint-app, in plaats van het fragment dat u via de portal krijgt.

```


<SharePoint:ScriptLink ID="ScriptLink1" name="SP.js" runat="server" localizable="false" loadafterui="true" /> 
<SharePoint:ScriptLink ID="ScriptLink2" name="SP.UserProfiles.js" runat="server" localizable="false" loadafterui="true" /> 

<script type="text/javascript"> 
var personProperties; 

// Ensure that the SP.UserProfiles.js file is loaded before the custom code runs. 
SP.SOD.executeOrDelayUntilScriptLoaded(getUserProperties, 'SP.UserProfiles.js'); 

function getUserProperties() { 
    // Get the current client context and PeopleManager instance. 
    var clientContext = new SP.ClientContext.get_current(); 
    var peopleManager = new SP.UserProfiles.PeopleManager(clientContext); 

    // Get user properties for the target user. 
    // To get the PersonProperties object for the current user, use the 
    // getMyProperties method. 

    personProperties = peopleManager.getMyProperties(); 

    // Load the PersonProperties object and send the request. 
    clientContext.load(personProperties); 
    clientContext.executeQueryAsync(onRequestSuccess, onRequestFail); 
} 

// This function runs if the executeQueryAsync call succeeds. 
function onRequestSuccess() { 
var appInsights=window.appInsights||function(config){
function s(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},r=document,f=window,e="script",o=r.createElement(e),i,u;for(o.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js",r.getElementsByTagName(e)[0].parentNode.appendChild(o),t.cookie=r.cookie,t.queue=[],i=["Event","Exception","Metric","PageView","Trace"];i.length;)s("track"+i.pop());return config.disableExceptionTracking||(i="onerror",s("_"+i),u=f[i],f[i]=function(config,r,f,e,o){var s=u&&u(config,r,f,e,o);return s!==!0&&t["_"+i](config,r,f,e,o),s}),t
    }({
        instrumentationKey:"XXXX"
    });
    window.appInsights=appInsights;
    appInsights.trackPageView(document.title,window.location.href, {User: personProperties.get_displayName()});
} 

// This function runs if the executeQueryAsync call fails. 
function onRequestFail(sender, args) { 
} 
</script> 


```



## <a name="next-steps"></a>Volgende stappen
* [Webtests](../../azure-monitor/app/monitor-web-app-availability.md) om de beschikbaarheid van de site te bewaken.
* [Application Insights](../../azure-monitor/app/app-insights-overview.md) voor andere typen app.

<!--Link references-->


