---
ms.openlocfilehash: 65fd8e133174c6ac7bbbbc00487cabc72ebe561d
ms.sourcegitcommit: e0d9b18d2d4cbeb4a48890f3420a47e6a90abc53
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "49347836"
---
<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="44cf5-101">In diesem Abschnitt verwenden Sie das Microsoft Graph-SDK, um die nächsten drei bevorstehenden Ereignisse im Kalender des Benutzers für die aktuelle Woche abzurufen.</span><span class="sxs-lookup"><span data-stu-id="44cf5-101">In this section you'll use the Microsoft Graph SDK to get the next 3 upcoming events on the user's calendar for the current week.</span></span>

## <a name="get-a-calendar-view"></a><span data-ttu-id="44cf5-102">Abrufen einer Kalenderansicht</span><span class="sxs-lookup"><span data-stu-id="44cf5-102">Get a calendar view</span></span>

<span data-ttu-id="44cf5-103">Eine Kalenderansicht ist eine Liste von Ereignissen im Kalender eines Benutzers, die zwischen zwei Datums-/Zeitwerten liegen.</span><span class="sxs-lookup"><span data-stu-id="44cf5-103">A calendar view is a list of events on a user's calendar that fall between two date/time values.</span></span> <span data-ttu-id="44cf5-104">Der Vorteil der Verwendung einer Kalenderansicht besteht darin, dass Sie alle Vorkommen von wiederkehrenden Besprechungen enthält.</span><span class="sxs-lookup"><span data-stu-id="44cf5-104">The advantage of using a calendar view is that it includes any occurrences of recurring meetings.</span></span>

1. <span data-ttu-id="44cf5-105">Öffnen Sie **./CardHelper.cs** , und fügen Sie die folgende Funktion zur **CardHelper** -Klasse hinzu.</span><span class="sxs-lookup"><span data-stu-id="44cf5-105">Open **./CardHelper.cs** and add the following function to the **CardHelper** class.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/CardHelper.cs" id="GetEventCardSnippet":::

    <span data-ttu-id="44cf5-106">Mit diesem Code wird eine Adaptive Karte zum Rendern eines Kalenderereignisses erstellt.</span><span class="sxs-lookup"><span data-stu-id="44cf5-106">This code builds an Adaptive Card to render a calendar event.</span></span>

1. <span data-ttu-id="44cf5-107">Öffnen Sie **/Dialogs/MainDialog.cs** , und fügen Sie die folgende Funktion zur **MainDialog** -Klasse hinzu.</span><span class="sxs-lookup"><span data-stu-id="44cf5-107">Open **./Dialogs/MainDialog.cs** and add the following function to the **MainDialog** class.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/MainDialog.cs" id="DisplayCalendarViewSnippet":::

    <span data-ttu-id="44cf5-108">Überprüfen Sie die Funktionsweise dieses Codes.</span><span class="sxs-lookup"><span data-stu-id="44cf5-108">Consider what this code does.</span></span>

    - <span data-ttu-id="44cf5-109">Es ruft das **Mailbox Settings** des Benutzers ab, um die bevorzugten Zeitzone und Datum/Uhrzeit-Formate des Benutzers zu bestimmen.</span><span class="sxs-lookup"><span data-stu-id="44cf5-109">It gets the user's **MailboxSettings** to determine the user's preferred time zone and date/time formats.</span></span>
    - <span data-ttu-id="44cf5-110">Die Werte **StartDateTime und EndDate** werden auf **Now und das** Ende der Woche festgelegt.</span><span class="sxs-lookup"><span data-stu-id="44cf5-110">It sets the **startDateTime** and **endDateTime** values to now and the end of the week, respectively.</span></span> <span data-ttu-id="44cf5-111">Dadurch wird das Zeitfenster definiert, das die Kalenderansicht verwendet.</span><span class="sxs-lookup"><span data-stu-id="44cf5-111">This defines the window of time that the calendar view uses.</span></span>
    - <span data-ttu-id="44cf5-112">Er ruft `graphClient.Me.CalendarView` mit den folgenden Details auf.</span><span class="sxs-lookup"><span data-stu-id="44cf5-112">It calls `graphClient.Me.CalendarView` with the following details.</span></span>
        - <span data-ttu-id="44cf5-113">Die Kopfzeile wird `Prefer: outlook.timezone` auf die bevorzugte Zeitzone des Benutzers festgelegt, wodurch die Start-und Endzeit für die Ereignisse in der Zeitzone des Benutzers liegt.</span><span class="sxs-lookup"><span data-stu-id="44cf5-113">It sets the `Prefer: outlook.timezone` header to the user's preferred time zone, causing the start and end times for the events to be in the user's timezone.</span></span>
        - <span data-ttu-id="44cf5-114">Es verwendet die `Top(3)` -Methode, um die Ergebnisse auf die ersten 3 Ereignisse zu begrenzen.</span><span class="sxs-lookup"><span data-stu-id="44cf5-114">It uses the `Top(3)` method to limit the results to only the first 3 events.</span></span>
        - <span data-ttu-id="44cf5-115">Es wird verwendet `Select` , um die zurückgegebenen Felder auf die vom bot verwendeten Felder zu begrenzen.</span><span class="sxs-lookup"><span data-stu-id="44cf5-115">It uses `Select` to limit the fields returned to just those fields used by the bot.</span></span>
        - <span data-ttu-id="44cf5-116">Es verwendet `OrderBy` , um die Ereignisse nach Startzeit zu sortieren.</span><span class="sxs-lookup"><span data-stu-id="44cf5-116">It uses `OrderBy` to sort the events by start time.</span></span>
    - <span data-ttu-id="44cf5-117">Es fügt eine Adaptive Karte für jedes Ereignis zur Antwortnachricht hinzu.</span><span class="sxs-lookup"><span data-stu-id="44cf5-117">It adds an Adaptive Card for each event to the reply message.</span></span>

1. <span data-ttu-id="44cf5-118">Ersetzen Sie den Code innerhalb des `else if (command.StartsWith("show calendar"))` Blocks in `ProcessStepAsync` durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="44cf5-118">Replace the code inside the `else if (command.StartsWith("show calendar"))` block in `ProcessStepAsync` with the following.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/MainDialog.cs" id="ShowCalendarSnippet" highlight="3":::

1. <span data-ttu-id="44cf5-119">Speichern Sie alle Änderungen, und starten Sie den bot neu.</span><span class="sxs-lookup"><span data-stu-id="44cf5-119">Save all of your changes and restart the bot.</span></span>

1. <span data-ttu-id="44cf5-120">Verwenden Sie den bot Framework-Emulator, um eine Verbindung mit dem bot herzustellen und sich anzumelden.</span><span class="sxs-lookup"><span data-stu-id="44cf5-120">Use the Bot Framework Emulator to connect to the bot and log in.</span></span> <span data-ttu-id="44cf5-121">Klicken Sie auf die Schaltfläche **Kalender anzeigen** , um die Kalenderansicht anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="44cf5-121">Select the **Show calendar** button to display the calendar view.</span></span>

    ![Ein Screenshot der adaptiven Karte mit den nächsten drei Ereignissen](images/calendar-view.png)
