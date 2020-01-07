---
title: Neuerungen in ASP.NET Core 3.1
author: rick-anderson
description: Informationen zu den neuen Features in ASP.NET Core 3.1.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- SignalR
uid: aspnetcore-3.1
ms.openlocfilehash: 89c676b96ef66f648544a8a884593bdafa3876de
ms.sourcegitcommit: 851b921080fe8d719f54871770ccf6f78052584e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2019
ms.locfileid: "74944225"
---
# <a name="whats-new-in-aspnet-core-31"></a><span data-ttu-id="65d07-103">Neuerungen in ASP.NET Core 3.1</span><span class="sxs-lookup"><span data-stu-id="65d07-103">What's new in ASP.NET Core 3.1</span></span>

<span data-ttu-id="65d07-104">In diesem Artikel werden die wichtigsten Änderungen in ASP.NET Core 3.1 aufgezeigt und Links zur relevanten Dokumentation bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="65d07-104">This article highlights the most significant changes in ASP.NET Core 3.1 with links to relevant documentation.</span></span>

## <a name="partial-class-support-for-razor-components"></a><span data-ttu-id="65d07-105">Unterstützung von partiellen Klassen für Razor-Komponenten</span><span class="sxs-lookup"><span data-stu-id="65d07-105">Partial class support for Razor components</span></span>

<span data-ttu-id="65d07-106">Razor-Komponenten werden nun als partielle Klassen generiert.</span><span class="sxs-lookup"><span data-stu-id="65d07-106">Razor components are now generated as partial classes.</span></span> <span data-ttu-id="65d07-107">Der Code für eine Razor-Komponente kann nun mithilfe einer CodeBehind-Datei geschrieben werden, die als eine partielle Klasse definiert ist, anstatt den gesamten Code für die Komponente in einer einzelnen Datei zu definieren.</span><span class="sxs-lookup"><span data-stu-id="65d07-107">Code for a Razor component can be written using a code-behind file defined as a partial class rather than defining all the code for the component in a single file.</span></span> <span data-ttu-id="65d07-108">Weitere Informationen finden Sie unter [Unterstützung von partiellen Klassen](xref:blazor/components#partial-class-support).</span><span class="sxs-lookup"><span data-stu-id="65d07-108">For more information, see [Partial class support](xref:blazor/components#partial-class-support).</span></span>

## <a name="opno-locblazor-component-tag-helper-and-pass-parameters-to-top-level-components"></a>Blazor<span data-ttu-id="65d07-109">-Komponententaghilfsprogramm und Übergeben von Parametern an Komponenten der obersten Ebene</span><span class="sxs-lookup"><span data-stu-id="65d07-109"> Component Tag Helper and pass parameters to top-level components</span></span>

<span data-ttu-id="65d07-110">In Blazor mit ASP.NET Core 3.0 wurden Komponenten mithilfe eines HTML-Hilfsprogramms (`Html.RenderComponentAsync`) in Seiten und Ansichten gerendert.</span><span class="sxs-lookup"><span data-stu-id="65d07-110">In Blazor with ASP.NET Core 3.0, components were rendered into pages and views using an HTML Helper (`Html.RenderComponentAsync`).</span></span> <span data-ttu-id="65d07-111">In ASP.NET Core 3.1 werden Komponenten mithilfe eines neuen Komponententaghilfsprogramms in Seiten oder Ansichten gerendert:</span><span class="sxs-lookup"><span data-stu-id="65d07-111">In ASP.NET Core 3.1, render a component from a page or view with the new Component Tag Helper:</span></span>

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="65d07-112">Das HTML-Hilfsprogramm wird in ASP.NET Core 3.1 weiterhin unterstützt, jedoch wird die Verwendung des Komponententaghilfsprogramms empfohlen.</span><span class="sxs-lookup"><span data-stu-id="65d07-112">The HTML Helper remains supported in ASP.NET Core 3.1, but the Component Tag Helper is recommended.</span></span>

Blazor<span data-ttu-id="65d07-113">-Server-Apps können nun während dem ersten Rendering Parameter an Komponenten der obersten Ebene übergeben.</span><span class="sxs-lookup"><span data-stu-id="65d07-113"> Server apps can now pass parameters to top-level components during the initial render.</span></span> <span data-ttu-id="65d07-114">Zuvor konnten Sie Parameter nur an Komponenten der obersten Ebene mit [RenderMode.Static](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static) übergeben.</span><span class="sxs-lookup"><span data-stu-id="65d07-114">Previously you could only pass parameters to a top-level component with [RenderMode.Static](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static).</span></span> <span data-ttu-id="65d07-115">Ab diesem Release werden sowohl [RenderMode.Server](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server) als auch [RenderModel.ServerPrerendered](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered) unterstützt.</span><span class="sxs-lookup"><span data-stu-id="65d07-115">With this release, both [RenderMode.Server](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server) and [RenderModel.ServerPrerendered](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered) are supported.</span></span> <span data-ttu-id="65d07-116">Alle festgelegten Parameterwerte werden als JSON serialisiert und sind in der ersten Antwort enthalten.</span><span class="sxs-lookup"><span data-stu-id="65d07-116">Any specified parameter values are serialized as JSON and included in the initial response.</span></span>

<span data-ttu-id="65d07-117">So können Sie eine `Counter`-Komponente beispielsweise mit einem Inkrement vorab rendern (`IncrementAmount`):</span><span class="sxs-lookup"><span data-stu-id="65d07-117">For example, prerender a `Counter` component with an increment amount (`IncrementAmount`):</span></span>

```razor
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

<span data-ttu-id="65d07-118">Weitere Informationen finden Sie unter [Integrieren von Komponenten in Razor-Seiten und MVC-Apps](xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps).</span><span class="sxs-lookup"><span data-stu-id="65d07-118">For more information, see [Integrate components into Razor Pages and MVC apps](xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps).</span></span>

## <a name="support-for-shared-queues-in-httpsys"></a><span data-ttu-id="65d07-119">Unterstützung für freigegebene Warteschlangen in HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="65d07-119">Support for shared queues in HTTP.sys</span></span>

<span data-ttu-id="65d07-120">[HTTP.sys](xref:fundamentals/servers/httpsys) unterstützt das Erstellen anonymer Anforderungswarteschlangen.</span><span class="sxs-lookup"><span data-stu-id="65d07-120">[HTTP.sys](xref:fundamentals/servers/httpsys) supports creating anonymous request queues.</span></span> <span data-ttu-id="65d07-121">In ASP.NET Core 3.1 wurde die Möglichkeit hinzugefügt, eine benannte HTTP.sys-Anforderungswarteschlange zu erstellen oder Elemente an eine solche Warteschlange anzufügen.</span><span class="sxs-lookup"><span data-stu-id="65d07-121">In ASP.NET Core 3.1, we've added to ability to create or attach to an existing named HTTP.sys request queue.</span></span> <span data-ttu-id="65d07-122">Das Erstellen einer benannten HTTP.sys-Anforderungswarteschlange bzw. das Anfügen von Elementen an eine solche ermöglicht Szenarien, in denen der HTTP.sys-Controllerprozess, der die Warteschlange besitzt, unabhängig vom Listenerprozess ist.</span><span class="sxs-lookup"><span data-stu-id="65d07-122">Creating or attaching to an existing named HTTP.sys request queue enables scenarios where the HTTP.sys controller process that owns the queue is independent of the listener process.</span></span> <span data-ttu-id="65d07-123">Diese Unabhängigkeit ermöglicht die Beibehaltung vorhandener Verbindungen und in der Warteschlange eingereihter Anforderungen zwischen Neustarts des Listenerprozesses:</span><span class="sxs-lookup"><span data-stu-id="65d07-123">This independence makes it possible to preserve existing connections and enqueued requests between listener process restarts:</span></span>

[!code-csharp[](sample/Program.cs?name=snippet)]

## <a name="breaking-changes-for-samesite-cookies"></a><span data-ttu-id="65d07-124">Breaking Changes für SameSite-Cookies</span><span class="sxs-lookup"><span data-stu-id="65d07-124">Breaking changes for SameSite cookies</span></span>

<span data-ttu-id="65d07-125">Das Verhalten von SameSite-Cookies wurde gemäß bevorstehender Browseränderungen geändert.</span><span class="sxs-lookup"><span data-stu-id="65d07-125">The behavior of SameSite cookies has changed to reflect upcoming browser changes.</span></span> <span data-ttu-id="65d07-126">Dies kann sich auf Authentifizierungsszenarios wie Azure AD, OpenIdConnect oder WsFederation auswirken.</span><span class="sxs-lookup"><span data-stu-id="65d07-126">This may affect authentication scenarios like AzureAd, OpenIdConnect, or WsFederation.</span></span> <span data-ttu-id="65d07-127">Weitere Informationen finden Sie unter <xref:security/samesite>.</span><span class="sxs-lookup"><span data-stu-id="65d07-127">For more information, see <xref:security/samesite>.</span></span>

## <a name="prevent-default-actions-for-events-in-opno-locblazor-apps"></a><span data-ttu-id="65d07-128">Verhindern von Standardaktionen für Ereignisse in Blazor-Apps</span><span class="sxs-lookup"><span data-stu-id="65d07-128">Prevent default actions for events in Blazor apps</span></span>

<span data-ttu-id="65d07-129">Verwenden Sie das Direktivenattribut `@on{EVENT}:preventDefault`, um die Standardaktion für ein Ereignis zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="65d07-129">Use the `@on{EVENT}:preventDefault` directive attribute to prevent the default action for an event.</span></span> <span data-ttu-id="65d07-130">Im folgenden Beispiel wird die Standardaktion verhindert, welche das Zeichen eines Schlüssels im Textfeld anzeigt:</span><span class="sxs-lookup"><span data-stu-id="65d07-130">In the following example, the default action of displaying the key's character in the text box is prevented:</span></span>

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />
```

<span data-ttu-id="65d07-131">Weitere Informationen finden Sie unter [Verhindern von Standardaktionen](xref:blazor/components#prevent-default-actions).</span><span class="sxs-lookup"><span data-stu-id="65d07-131">For more information, see [Prevent default actions](xref:blazor/components#prevent-default-actions).</span></span>

## <a name="stop-event-propagation-in-opno-locblazor-apps"></a><span data-ttu-id="65d07-132">Beenden der Ereignisweitergabe in Blazor-Apps</span><span class="sxs-lookup"><span data-stu-id="65d07-132">Stop event propagation in Blazor apps</span></span>

<span data-ttu-id="65d07-133">Verwenden Sie das Direktivenattribut `@on{EVENT}:stopPropagation`, um die Ereignisweitergabe zu beenden.</span><span class="sxs-lookup"><span data-stu-id="65d07-133">Use the `@on{EVENT}:stopPropagation` directive attribute to stop event propagation.</span></span> <span data-ttu-id="65d07-134">Im folgenden Beispiel verhindert das Aktivieren des Kontrollkästchens die Weitergabe von Klickereignissen des untergeordneten `<div>`-Elements an das übergeordnete `<div>`-Element:</span><span class="sxs-lookup"><span data-stu-id="65d07-134">In the following example, selecting the check box prevents click events from the child `<div>` from propagating to the parent `<div>`:</span></span>

```razor
<input @bind="_stopPropagation" type="checkbox" />

<div @onclick="OnSelectParentDiv">
    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="_stopPropagation">
        ...
    </div>
</div>

@code {
    private bool _stopPropagation = false;
}
```

<span data-ttu-id="65d07-135">Weitere Informationen finden Sie unter [Beenden der Ereignisweitergabe](xref:blazor/components#stop-event-propagation).</span><span class="sxs-lookup"><span data-stu-id="65d07-135">For more information, see [Stop event propagation](xref:blazor/components#stop-event-propagation).</span></span>

## <a name="detailed-errors-during-opno-locblazor-app-development"></a><span data-ttu-id="65d07-136">Ausführliche Fehler bei der Entwicklung von Blazor-Apps</span><span class="sxs-lookup"><span data-stu-id="65d07-136">Detailed errors during Blazor app development</span></span>

<span data-ttu-id="65d07-137">Wenn eine Blazor-App während der Entwicklung nicht ordnungsgemäß funktioniert, erhalten Sie nun ausführliche Fehlerinformationen von der App, die Sie beim Beheben des Problems unterstützen.</span><span class="sxs-lookup"><span data-stu-id="65d07-137">When a Blazor app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="65d07-138">Wenn ein Fehler auftritt, zeigen Blazor-Apps eine goldene Leiste am unteren Rand der Anzeige an:</span><span class="sxs-lookup"><span data-stu-id="65d07-138">When an error occurs, Blazor apps display a gold bar at the bottom of the screen:</span></span>

* <span data-ttu-id="65d07-139">Während der Entwicklung leitet die goldene Leiste Sie an die Browserkonsole weiter, in der die Ausnahme angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="65d07-139">During development, the gold bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="65d07-140">In der Produktion benachrichtigt die goldene Leiste den Benutzer darüber, dass ein Fehler aufgetreten ist, und empfiehlt eine Aktualisierung des Browsers.</span><span class="sxs-lookup"><span data-stu-id="65d07-140">In production, the gold bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="65d07-141">Weitere Informationen finden Sie unter [Ausführliche Fehler während der Entwicklung](xref:blazor/handle-errors#detailed-errors-during-development).</span><span class="sxs-lookup"><span data-stu-id="65d07-141">For more information, see [Detailed errors during development](xref:blazor/handle-errors#detailed-errors-during-development).</span></span>