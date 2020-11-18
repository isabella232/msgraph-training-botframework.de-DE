---
ms.openlocfilehash: 65fd8e133174c6ac7bbbbc00487cabc72ebe561d
ms.sourcegitcommit: e0d9b18d2d4cbeb4a48890f3420a47e6a90abc53
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "49347836"
---
<!-- markdownlint-disable MD002 MD041 -->

In diesem Abschnitt verwenden Sie das Microsoft Graph-SDK, um die nächsten drei bevorstehenden Ereignisse im Kalender des Benutzers für die aktuelle Woche abzurufen.

## <a name="get-a-calendar-view"></a>Abrufen einer Kalenderansicht

Eine Kalenderansicht ist eine Liste von Ereignissen im Kalender eines Benutzers, die zwischen zwei Datums-/Zeitwerten liegen. Der Vorteil der Verwendung einer Kalenderansicht besteht darin, dass Sie alle Vorkommen von wiederkehrenden Besprechungen enthält.

1. Öffnen Sie **./CardHelper.cs** , und fügen Sie die folgende Funktion zur **CardHelper** -Klasse hinzu.

    :::code language="csharp" source="../demo/GraphCalendarBot/CardHelper.cs" id="GetEventCardSnippet":::

    Mit diesem Code wird eine Adaptive Karte zum Rendern eines Kalenderereignisses erstellt.

1. Öffnen Sie **/Dialogs/MainDialog.cs** , und fügen Sie die folgende Funktion zur **MainDialog** -Klasse hinzu.

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/MainDialog.cs" id="DisplayCalendarViewSnippet":::

    Überprüfen Sie die Funktionsweise dieses Codes.

    - Es ruft das **Mailbox Settings** des Benutzers ab, um die bevorzugten Zeitzone und Datum/Uhrzeit-Formate des Benutzers zu bestimmen.
    - Die Werte **StartDateTime und EndDate** werden auf **Now und das** Ende der Woche festgelegt. Dadurch wird das Zeitfenster definiert, das die Kalenderansicht verwendet.
    - Er ruft `graphClient.Me.CalendarView` mit den folgenden Details auf.
        - Die Kopfzeile wird `Prefer: outlook.timezone` auf die bevorzugte Zeitzone des Benutzers festgelegt, wodurch die Start-und Endzeit für die Ereignisse in der Zeitzone des Benutzers liegt.
        - Es verwendet die `Top(3)` -Methode, um die Ergebnisse auf die ersten 3 Ereignisse zu begrenzen.
        - Es wird verwendet `Select` , um die zurückgegebenen Felder auf die vom bot verwendeten Felder zu begrenzen.
        - Es verwendet `OrderBy` , um die Ereignisse nach Startzeit zu sortieren.
    - Es fügt eine Adaptive Karte für jedes Ereignis zur Antwortnachricht hinzu.

1. Ersetzen Sie den Code innerhalb des `else if (command.StartsWith("show calendar"))` Blocks in `ProcessStepAsync` durch Folgendes.

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/MainDialog.cs" id="ShowCalendarSnippet" highlight="3":::

1. Speichern Sie alle Änderungen, und starten Sie den bot neu.

1. Verwenden Sie den bot Framework-Emulator, um eine Verbindung mit dem bot herzustellen und sich anzumelden. Klicken Sie auf die Schaltfläche **Kalender anzeigen** , um die Kalenderansicht anzuzeigen.

    ![Ein Screenshot der adaptiven Karte mit den nächsten drei Ereignissen](images/calendar-view.png)
