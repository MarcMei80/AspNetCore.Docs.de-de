---
title: Blazor-Layouts in ASP.NET Core
author: guardrex
description: In diesem Artikel erfahren Sie, wie Sie wiederverwendbare Layoutkomponenten für Blazor-Apps erstellen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/layouts
ms.openlocfilehash: 5b6e1c7ceb4a6e41230e31bbe379bde1bb0a8286
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/06/2020
ms.locfileid: "78647923"
---
# <a name="aspnet-core-opno-locblazor-layouts"></a>Blazor-Layouts in ASP.NET Core

Von [Rainer Stropek](https://www.timecockpit.com) und [Luke Latham](https://github.com/guardrex)

Einige App-Elemente wie Menüs, Copyrightmeldungen und Firmenlogos sind in der Regel Teil des allgemeinen Layouts von Apps und werden von allen Komponenten der App verwendet. Es ist nicht sonderlich effizient, den Code dieser Elemente in alle anderen Komponenten einer App zu kopieren. Jedes Mal, wenn eines dieser Elemente aktualisiert werden muss, müssen alle Komponenten aktualisiert werden. Eine solche Duplizierung ist schwer zu verwalten und kann im Laufe der Zeit zu inkonsistenten Inhalten führen. Mit *Layouts* wird dieses Problem gelöst.

Technisch gesehen ist ein Layout nur eine weitere Komponente. Layouts werden in Razor-Vorlagen oder in C#-Code definiert und können [Datenbindungen](xref:blazor/data-binding), [Abhängigkeitsinjektion](xref:blazor/dependency-injection) und andere Komponentenszenarios nutzen.

Zum Umwandeln einer *Komponente* in ein *Layout* muss die Komponente:

* von `LayoutComponentBase` erben, was eine `Body`-Eigenschaft für die gerenderten Inhalte im Layout definiert.
* die Razor-Syntax `@Body` verwenden, um den Standort im Layoutmarkup anzugeben, ab den der Inhalt gerendert wird.

Im folgenden Codebeispiel wird eine Razor-Vorlage einer Layoutkomponente (*MainLayout.razor*) veranschaulicht. Das Layout erbt `LayoutComponentBase` und legt `@Body` zwischen der Navigationsleiste und der Fußzeile fest:

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

Bei Apps, die auf Blazor-App-Vorlagen basieren, befindet sich die `MainLayout`-Komponente (*MainLayout.razor*) im Ordner *Shared* der App.

## <a name="default-layout"></a>Standardlayout

Legen Sie das Standardlayout für die App in der `Router`-Komponente der Datei *App.razor* der App fest. Mit der folgenden `Router`-Komponente, die von den Blazor-Standardvorlagen bereitgestellt wird, wird das Standardlayout auf die `MainLayout`-Komponente festgelegt:

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

Geben Sie `NotFound` für `LayoutView`-Inhalt an, um ein Standardlayout für `NotFound`-Inhalt anzugeben:

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

Weitere Informationen zur `Router`-Komponente finden Sie unter <xref:blazor/routing>.

Das Festlegen des Layouts als Standardlayout im Router ist nützlich, da es pro Komponente oder pro Ordner überschrieben werden kann. Das Verwenden des Routers zum Festlegen des Standardlayouts der App wird bevorzugt, da es sich dabei um die allgemeinste Methode handelt.

## <a name="specify-a-layout-in-a-component"></a>Festlegen eines Layouts in einer Komponente

Verwenden Sie die Razor-Anweisung `@layout`, um ein Layout auf eine Komponente anzuwenden. Der Compiler konvertiert `@layout` in eine `LayoutAttribute`-Eigenschaft, die auf die Komponentenklasse angewendet wird.

Der Inhalt der folgenden `MasterList`-Komponente wird bei der Position von `MasterLayout` in `@Body` eingefügt:

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

Durch das direkte Festlegen eines Layouts in einer Komponente wird das im Router oder mit einer aus *_Imports.razor* importierten `@layout`-Anweisung festgelegte *Standardlayout* überschrieben.

## <a name="centralized-layout-selection"></a>Zentrale Layoutauswahl

Alle Ordner einer App können optional eine Vorlagendatei namens *_Imports.razor* enthalten. Der Compiler enthält die in der Importdatei angegebenen Anweisungen in allen Razor-Vorlagen im gleichen Ordner und rekursiv in allen Unterordnern. Daher wird mit einer *_Imports.razor*-Datei, die `@layout MyCoolLayout` enthält, sichergestellt, dass alle Komponenten in einem Ordner `MyCoolLayout` verwenden. `@layout MyCoolLayout` muss nicht wiederholt zu allen *RAZOR*-Dateien im Ordner und in den Unterordnern hinzugefügt werden. `@using`-Anweisungen werden auf die gleiche Weise auf Komponenten angewendet.

Die folgende Datei *_Imports.razor* importiert Folgendes:

* `MyCoolLayout`.
* Alle Razor-Komponenten im gleichen Ordner und in dessen Unterordnern
* Der `BlazorApp1.Data` -Namespace.
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

Die Datei *_Imports.razor* ähnelt der Datei [_ViewImports.cshtml für Razor-Ansichten und -Seiten](xref:mvc/views/layout#importing-shared-directives), gilt aber spezifisch für Razor-Komponentendateien.

Durch das Festlegen eines Layouts in *_Imports.razor* wird ein Layout überschrieben, das als *Standardlayout* des Routers festgelegt wird.

## <a name="nested-layouts"></a>Geschachtelte Layouts

Apps können aus geschachtelten Layouts bestehen. Eine Komponente kann auf ein Layout verweisen, das wiederum auf ein anderes Layout verweist. Geschachtelte Layouts werden beispielsweise dazu verwendet, eine Menüstruktur mit mehreren Ebenen zu erstellen.

Im folgenden Beispiel wird die Verwendung von geschachtelten Layouts veranschaulicht. Die Datei *EpisodesComponent.razor* ist die Komponente, die angezeigt werden soll. Die Komponente verweist auf `MasterListLayout`:

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

Die Datei *MasterListLayout.razor* stellt `MasterListLayout` bereit. Das Layout verweist auf ein anderes Layout (`MasterLayout`), in dem es gerendert wird. `EpisodesComponent` wird dort gerendert, wo `@Body` vorkommt:

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

Schließlich enthält `MasterLayout` in der Datei *MasterLayout.razor* die allgemeinen Layoutelemente, z. B. die Kopfzeile, das Hauptmenü und die Fußzeile. Das Layout `MasterListLayout` mit der Komponente `EpisodesComponent` wird dort gerendert, wo `@Body` vorkommt:

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-razor-pages-layout-with-integrated-components"></a>Freigeben eines Razor Pages-Layouts mit integrierten Komponenten

Wenn routingfähige Komponenten in eine Razor Pages-App integriert werden, kann das freigegebene Layout der App mit den Komponenten verwendet werden. Weitere Informationen finden Sie unter <xref:blazor/integrate-components>.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:mvc/views/layout>
