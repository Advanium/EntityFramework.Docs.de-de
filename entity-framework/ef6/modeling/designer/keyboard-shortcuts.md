---
title: Entity Framework Designer Tastenkombinationen-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 3c76cdd5-17c5-4c54-a6a5-cf21b974636b
ms.openlocfilehash: c75eafcca0863faa1ad64202e98b61832827377c
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78415310"
---
# <a name="entity-framework-designer-keyboard-shortcuts"></a>Entity Framework Designer Tastenkombinationen
Diese Seite enthält eine Liste der Tastenkombinationen, die in den verschiedenen Bildschirmen der Entity Framework Tools für Visual Studio verfügbar sind.

## <a name="adonet-entity-data-model-wizard"></a>ADO.NET Entity Data Model-Assistent

### <a name="step-one-choose-model-contents"></a>Schritt 1: Auswählen des Modell Inhalts

![Assistent eines](~/ef6/media/wizardone.png)

| Tastenkombination  | Action                                                     | Notizen                                               |
|:----------|:-----------------------------------------------------------|:----------------------------------------------------|
| **Alt + n** | Zum nächsten Bildschirm wechseln                                        | Nicht für alle Auswahlmöglichkeiten des Modell Inhalts verfügbar. |
| **Alt + f** | Beenden Sie den Assistenten.                                              | Nicht für alle Auswahlmöglichkeiten des Modell Inhalts verfügbar. |
| **Alt + w** | Wechseln Sie zu "Was soll das Modell enthalten?". schlugen. |                                                     |

### <a name="step-two-choose-your-connection"></a>Schritt 2: Auswählen der Verbindung

![Assistent 2](~/ef6/media/wizardtwo.png)

| Tastenkombination  | Action                                                     | Notizen                                                   |
|:----------|:-----------------------------------------------------------|:--------------------------------------------------------|
| **Alt + n** | Zum nächsten Bildschirm wechseln                                        |                                                         |
| **Alt + p** | Zum vorherigen Bildschirm wechseln                                    |                                                         |
| **Alt + w** | Wechseln Sie zu "Was soll das Modell enthalten?". schlugen. |                                                         |
| **Alt + c** | Öffnen Sie das Fenster "Verbindungs Eigenschaften".                    | Ermöglicht die Definition einer neuen Datenbankverbindung. |
| **Alt + e** | Sensible Daten aus der Verbindungs Zeichenfolge ausschließen          |                                                         |
| **Alt + i** | Einbeziehen von sensiblen Daten in die Verbindungs Zeichenfolge            |                                                         |
| **Alt + s** | Option "Verbindungseinstellungen in app. config speichern" umschalten |                                                         |

### <a name="step-three-choose-your-version"></a>Schritt 3: Wählen Sie Ihre Version aus.

![Assistent 3](~/ef6/media/wizardthree.png)

| Tastenkombination  | Action                                             | Notizen                                                                                 |
|:----------|:---------------------------------------------------|:--------------------------------------------------------------------------------------|
| **Alt + n** | Zum nächsten Bildschirm wechseln                                |                                                                                       |
| **Alt + p** | Zum vorherigen Bildschirm wechseln                            |                                                                                       |
| **Alt + w** | Fokus auf Entity Framework Versions Auswahl wechseln | Ermöglicht das Angeben einer anderen Version von Entity Framework für die Verwendung im Projekt. |

### <a name="step-four-choose-your-database-objects-and-settings"></a>Schritt 4: Auswählen der Datenbankobjekte und-Einstellungen

![Assistent vier](~/ef6/media/wizardfour.png)

| Tastenkombination  | Action                                                                                    | Notizen                                                               |
|:----------|:------------------------------------------------------------------------------------------|:--------------------------------------------------------------------|
| **Alt + f** | Beenden Sie den Assistenten.                                                                             |                                                                     |
| **Alt + p** | Zum vorherigen Bildschirm wechseln                                                                   |                                                                     |
| **Alt + w** | Fokus auf Auswahlbereich des Datenbankobjekts wechseln                                            | Ermöglicht das Angeben von Datenbankobjekten, die umgekehrt werden sollen.    |
| **Alt + s** | Aktivieren Sie die Option "generierte Objektnamen in pluralisieren oder einblenden".                       |                                                                     |
| **Alt + k** | Option "Fremdschlüssel Spalten in das Modell einschließen" umschalten                              | Nicht für alle Auswahlmöglichkeiten des Modell Inhalts verfügbar.                 |
| **Alt + i** | Option "ausgewählte gespeicherte Prozeduren und Funktionen in das Entitäts Modell importieren" umschalten | Nicht für alle Auswahlmöglichkeiten des Modell Inhalts verfügbar.                 |
| **Alt + m** | Schaltet den Fokus auf das Textfeld "Modell Namespace".                                        | Nicht für alle Auswahlmöglichkeiten des Modell Inhalts verfügbar.                 |
| **LEERTASTE** | Auswahl für Element umschalten                                                               | Wenn das Element über untergeordnete Elemente verfügt, werden alle untergeordneten Elemente ebenfalls ein-und ausgeschaltet. |
| **Left**  | Untergeordnete Struktur zuklappen                                                                       |                                                                     |
| **Right** | Untergeordnete Struktur erweitern                                                                         |                                                                     |
| **Up**    | Zum vorherigen Element in der Struktur navigieren                                                      |                                                                     |
| **Nach unten**  | Zum nächsten Element in der Struktur navigieren                                                          |                                                                     |

## <a name="ef-designer-surface"></a>EF-Designer Oberfläche

![Designeroberfläche](~/ef6/media/designersurface.png)

| Tastenkombination                                                                                | Action                      | Notizen                                                                                                                                                                                                                               |
|:----------------------------------------------------------------------------------------|:----------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Leerraum/EINGABETASTE**                                                                         | Auswahl umschalten            | Schaltet die Auswahl für das-Objekt mit dem Fokus um.                                                                                                                                                                                         |
| **ESC**                                                                                 | Auswahl Abbrechen            | Bricht die aktuelle Auswahl ab.                                                                                                                                                                                                      |
| **STRG + A**                                                                            | Alles auswählen                  | Wählt alle Formen auf der Entwurfs Oberfläche aus.                                                                                                                                                                                       |
| **Pfeil nach oben**                                                                            | Nach oben                     | Verschiebt die ausgewählte Entität um ein Raster Inkrement nach oben. <br/> Wechselt in einer Liste zum vorherigen neben geordneten Subfeld.                                                                                                                            |
| **Pfeil nach unten**                                                                          | Nach unten                   | Verschiebt die ausgewählte Entität um ein Raster Inkrement nach unten. <br/> Wechselt in einer Liste zum nächsten neben geordneten Unterfeld.                                                                                                                              |
| **NACH-LINKS-TASTE**                                                                          | Nach links verschieben                   | Verschiebt die ausgewählte Entität um ein Raster Inkrement nach links. <br/> Wechselt in einer Liste zum vorherigen neben geordneten Subfeld.                                                                                                                          |
| **NACH-RECHTS-TASTE**                                                                         | Nach rechts verschieben                  | Verschiebt die ausgewählte Entität um ein Raster Inkrement nach rechts. <br/> Wechselt in einer Liste zum nächsten neben geordneten Unterfeld.                                                                                                                             |
| **UMSCHALT + nach-links-Taste**                                                                  | Größe der Form Links             | Verringert die Breite der ausgewählten Entität um ein Raster Inkrement.                                                                                                                                                                     |
| **UMSCHALT + nach-rechts-Taste**                                                                 | Shape-Größe rechts            | Erhöht die Breite der ausgewählten Entität um ein Raster Inkrement.                                                                                                                                                                   |
| **Startseite**                                                                                | Erster Peer                  | Verschiebt den Fokus und die Auswahl auf das erste Objekt auf der Entwurfs Oberfläche auf der gleichen Peer Ebene.                                                                                                                                         |
| **Ende**                                                                                 | Letzter Peer                   | Verschiebt den Fokus und die Auswahl auf das letzte-Objekt auf der Entwurfs Oberfläche auf der gleichen Peer Ebene.                                                                                                                                          |
| **STRG + Startseite**                                                                         | Erster Peer (Fokus)          | Identisch mit dem ersten Peer, jedoch wird der Fokus verschoben, anstatt den Fokus und die Auswahl zu verschieben.                                                                                                                                                          |
| **STRG + Ende**                                                                          | Letzter Peer (Fokus)           | Identisch mit dem letzten Peer, verschiebt aber den Fokus, anstatt den Fokus und die Auswahl zu verschieben.                                                                                                                                                           |
| **TAB**                                                                                 | Nächster Peer                   | Verschiebt den Fokus und die Auswahl auf das nächste Objekt auf der Entwurfs Oberfläche auf der gleichen Peer Ebene.                                                                                                                                          |
| **UMSCHALT+TAB**                                                                           | Vorheriger Peer               | Verschiebt den Fokus und die Auswahl auf das vorherige Objekt auf der Entwurfs Oberfläche auf der gleichen Peer Ebene.                                                                                                                                      |
| **Alt + Strg + Tab**                                                                        | Nächster Peer (Fokus)           | Identisch mit dem nächsten Peer, verschiebt aber den Fokus, anstatt den Fokus und die Auswahl zu verschieben.                                                                                                                                                           |
| **ALT + STRG + UMSCHALT + TAB**                                                                  | Vorheriger Peer (Fokus)       | Identisch mit dem vorherigen Peer, aber Verschiebt den Fokus, anstatt den Fokus und die Auswahl zu verschieben.                                                                                                                                                       |
| **&lt;**                                                                                | Auff                      | Wechselt zum nächsten-Objekt auf der Entwurfs Oberfläche in der-Hierarchie um eine Ebene höher. Wenn in der Hierarchie keine Formen oberhalb dieser Form vorhanden sind (d. h., das Objekt wird direkt auf der Entwurfs Oberfläche platziert), wird das Diagramm ausgewählt. |
| **&gt;**                                                                                | Kandidat                     | Wechselt zum nächsten enthaltenen Objekt auf der Entwurfs Oberfläche, eine Ebene unterhalb dieses in der Hierarchie. Wenn kein eigenständiges Objekt vorhanden ist, handelt es sich hierbei um einen No-op-Vorgang.                                                                              |
| **STRG + &lt;**                                                                         | Aufsteigerückt (Fokus)              | Identisch mit dem Befehl "Ascend", verschiebt den Fokus jedoch ohne Auswahl.                                                                                                                                                                          |
| **STRG + &gt;**                                                                         | Abstieg (Fokus)             | Identisch mit dem Befehl "descen", aber Verschiebt den Fokus ohne Auswahl.                                                                                                                                                                         |
| **Umschalt + Ende**                                                                         | Zum Verbinden folgen         | Wechselt von einer Entität zu einer Entität, mit der diese Entität verbunden ist.                                                                                                                                                               |
| **ENTF**                                                                                 | Löschen                      | Löschen Sie ein Objekt oder einen Connector aus dem Diagramm.                                                                                                                                                                                     |
| **ELine**                                                                                 | Einfügen                      | Fügt einer Entität eine neue Eigenschaft hinzu, wenn entweder der Depot Header "Scalar Properties" oder eine Eigenschaft selbst ausgewählt ist.                                                                                                           |
| **PG-up**                                                                               | Diagramm nach oben scrollen           | Führt einen Bildlauf nach oben in Schritten von 75% der Höhe der aktuell sichtbaren Entwurfs Oberfläche durch.                                                                                                                    |
| **PG-Down**                                                                             | Bildlauf nach unten         | Führt einen Bildlauf nach unten durch.                                                                                                                                                                                                    |
| **UMSCHALT + PG nach unten**                                                                     | Diagramm nach rechts scrollen        | Führt einen Bildlauf zur Entwurfs Oberfläche nach rechts durch.                                                                                                                                                                                            |
| **UMSCHALT + PG nach oben**                                                                       | Diagramm nach links scrollen         | Führt einen Bildlauf zur Entwurfs Oberfläche nach links durch.                                                                                                                                                                                             |
| **F2**                                                                                  | In den Bearbeitungsmodus wechseln             | Standard Tastenkombination zum Wechseln in den Bearbeitungsmodus für ein Text Steuerelement.                                                                                                                                                               |
| **UMSCHALT + F10**                                                                         | Kontextmenü anzeigen       | Standard Tastenkombination zum Anzeigen des Kontextmenüs eines ausgewählten Elements.                                                                                                                                                          |
| **STRG + UMSCHALT + Maus Links klicken**  <br/> **STRG + UMSCHALT + mousrad vorwärts**  | Semantischer Zoom            | Zoomt in den Bereich der Diagramm Ansicht unterhalb des Mauszeigers.                                                                                                                                                                 |
| **STRG + UMSCHALT + Maus mit der rechten Maustaste** <br/> **STRG + UMSCHALT + mousrad rückwärts** | Semantische Vergrößerung           | Zoomt aus dem Bereich der Diagramm Ansicht unterhalb des Mauszeigers. Das Diagramm wird neu zentriert, wenn Sie für das aktuelle Diagramm Center zu weit zoomen.                                                                          |
| **Control + Shift + ' + '** <br/> **Steuerelement + mousrad vorwärts**                        | Vergrößern                     | Zoomt in der Mitte der Diagramm Ansicht.                                                                                                                                                                                         |
| **STRG + UMSCHALT + '-'** <br/> **Steuerelement + mousrad rückwärts**                       | Verkleinern                    | Zoomt aus dem angeklickten Bereich der Diagramm Ansicht. Das Diagramm wird neu zentriert, wenn Sie für das aktuelle Diagramm Center zu weit zoomen.                                                                                            |
| **STRG + UMSCHALT + Zeichnen eines Rechtecks mit der linken Maustaste**                  | Zoom Bereich                   | Zoomt in zentriert auf den Bereich, den Sie ausgewählt haben. Wenn Sie die Tasten STRG + UMSCHALT gedrückt halten, sehen Sie, dass sich der Cursor in eine Lupe ändert, sodass Sie den Bereich definieren können, in den Sie Zoomen können.                        |
| **Kontextmenü-Taste + 'm '**                                                              | Fenster "Mappingdetails öffnen" | Öffnet das Fenster Mappingdetails zum Bearbeiten von Zuordnungen für die ausgewählte Entität.                                                                                                                                                               |

## <a name="mapping-details-window"></a>Mappingdetails (Fenster)

![Tastenkombinationen für die Zuordnung](~/ef6/media/mappingdetailsshortcuts.png)

| Tastenkombination                  | Action         | Notizen                                                                                                                                 |
|:--------------------------|:---------------|:--------------------------------------------------------------------------------------------------------------------------------------|
| **TAB**                   | Kontext wechseln | Wechselt zwischen dem Hauptfenster Bereich und der Symbolleiste auf der linken Seite.                                                                     |
| **Pfeiltasten**            | Navigation     | Verschieben Sie Zeilen nach oben und nach unten oder nach rechts und Links über Spalten im Hauptfenster Bereich. Wechseln Sie zwischen den Schaltflächen in der Symbolleiste auf der linken Seite. |
| **EINGABETASTE** <br/> **LEERTASTE** | Select         | Wählt in der Symbolleiste auf der linken Seite eine Schaltfläche aus.                                                                                          |
| **Alt + nach-unten-Taste**      | Liste öffnen      | Dropdown Liste, wenn eine Zelle ausgewählt ist, die eine Dropdown Liste enthält.                                                                     |
| **EINGABETASTE**                 | Liste auswählen    | Wählt ein Element in einer Dropdown Liste aus.                                                                                               |
| **ESC**                   | Liste schließen     | Schließt eine Dropdown Liste.                                                                                                              |

## <a name="visual-studio-navigation"></a>Visual Studio-Navigation

Entity Framework bietet auch eine Reihe von Aktionen, für die benutzerdefinierte Tastenkombinationen zugeordnet werden können (Standardmäßig sind keine Verknüpfungen zugeordnet). Um diese benutzerdefinierten Verknüpfungen zu erstellen, klicken Sie auf das Menü Extras und dann auf Optionen.  Wählen Sie unter Umgebung die Option Tastatur aus.  Scrollen Sie in der Liste in der Mitte nach unten, bis Sie den gewünschten Befehl auswählen können, geben Sie die Verknüpfung in das Textfeld "Tastenkombinationen drücken" ein, und klicken Sie auf zuweisen. Die möglichen Tastenkombinationen lauten wie folgt:

| Tastenkombination                                                                                       |
|:-----------------------------------------------------------------------------------------------|
| **Othercontextmenus. microsoftdataentitydesigncontext. Add. ComplexProperty. complexTypes**        |
| **Othercontextmenus. microsoftdataentitydesigncontext. addcodegenerationitem**                   |
| **Othercontextmenus. microsoftdataentitydesigncontext. addfunctionimport**                       |
| **Othercontextmenus. microsoftdataentitydesigncontext. AddNew. addenumschlag Type**                      |
| **Othercontextmenus. microsoftdataentitydesigncontext. AddNew. Association**                      |
| **Othercontextmenus. microsoftdataentitydesigncontext. AddNew. ComplexProperty**                  |
| **Othercontextmenus. microsoftdataentitydesigncontext. AddNew. ComplexType**                      |
| **Othercontextmenus. microsoftdataentitydesigncontext. AddNew. Entity**                           |
| **Othercontextmenus. microsoftdataentitydesigncontext. AddNew. FunctionImport**                   |
| **Othercontextmenus. microsoftdataentitydesigncontext. AddNew. Vererbung**                      |
| **Othercontextmenus. microsoftdataentitydesigncontext. AddNew. NavigationProperty**               |
| **Othercontextmenus. microsoftdataentitydesigncontext. AddNew. ScalarProperty**                   |
| **Othercontextmenus. microsoftdataentitydesigncontext. addnewdiagram**                           |
| **Othercontextmenus. microsoftdataentitydesigncontext. adddediagram**                            |
| **Othercontextmenus. microsoftdataentitydesigncontext. Close**                                   |
| **Othercontextmenus. microsoftdataentitydesigncontext. Collapse**                                |
| **Othercontextmenus. microsoftdataentitydesigncontext. convertteum**                           |
| **Othercontextmenus. microsoftdataentitydesigncontext. Diagram. redualall**                     |
| **Othercontextmenus. microsoftdataentitydesigncontext. Diagram. ExpandAll**                       |
| **Othercontextmenus. microsoftdataentitydesigncontext. Diagram. exportasimage**                   |
| **Othercontextmenus. microsoftdataentitydesigncontext. Diagram. layoutdiagram**                   |
| **Othercontextmenus. microsoftdataentitydesigncontext. Edit**                                    |
| **Othercontextmenus. microsoftdataentitydesigncontext. EntityKey**                               |
| **Othercontextmenus. microsoftdataentitydesigncontext. Expand**                                  |
| **Othercontextmenus. microsoftdataentitydesigncontext. FunctionImportMapping**                   |
| **Othercontextmenus. microsoftdataentitydesigncontext. generatedatabasefrommodel**               |
| **Othercontextmenus. microsoftdataentitydesigncontext. godedefinition**                          |
| **Othercontextmenus. microsoftdataentitydesigncontext. Grid. ShowGrid**                           |
| **Othercontextmenus. microsoftdataentitydesigncontext. Grid. snapto Grid**                         |
| **Othercontextmenus. microsoftdataentitydesigncontext. includerelated**                          |
| **Othercontextmenus. microsoftdataentitydesigncontext. Mappingdetails**                          |
| **Othercontextmenus. microsoftdataentitydesigncontext. modelbrowser**                            |
| **Othercontextmenus. microsoftdataentitydesigncontext. muvediagramstoseparatefile**              |
| **Othercontextmenus. microsoftdataentitydesigncontext. muveproperties. Down**                     |
| **Othercontextmenus. microsoftdataentitydesigncontext. muveproperties. Down5**                    |
| **Othercontextmenus. microsoftdataentitydesigncontext. muveproperties. in Bottom**                 |
| **Othercontextmenus. microsoftdataentitydesigncontext. muveproperties. "Top"**                    |
| **Othercontextmenus. microsoftdataentitydesigncontext. muveproperties. up**                       |
| **Othercontextmenus. microsoftdataentitydesigncontext. muveproperties. UP5**                      |
| **Othercontextmenus. microsoftdataentitydesigncontext. wvetonewdiagram**                        |
| **Othercontextmenus. microsoftdataentitydesigncontext. Open**                                    |
| **Othercontextmenus. microsoftdataentitydesigncontext. Refactor. muvetonewcomplextype**           |
| **Othercontextmenus. microsoftdataentitydesigncontext. Refactor. rename**                         |
| **Othercontextmenus. microsoftdataentitydesigncontext. removefromdiagram**                       |
| **Othercontextmenus. microsoftdataentitydesigncontext. rename**                                  |
| **Othercontextmenus. microsoftdataentitydesigncontext. scalarpropertyformat. Display Name**        |
| **Othercontextmenus. microsoftdataentitydesigncontext. scalarpropertyformat. displaynameandtype** |
| **Othercontextmenus. microsoftdataentitydesigncontext. Select. BaseType**                         |
| **Othercontextmenus. microsoftdataentitydesigncontext. Select. Entity**                           |
| **Othercontextmenus. microsoftdataentitydesigncontext. Select. Property**                         |
| **Othercontextmenus. microsoftdataentitydesigncontext. Select. SubType**                          |
| **Othercontextmenus. microsoftdataentitydesigncontext. SelectAll**                               |
| **Othercontextmenus. microsoftdataentitydesigncontext. selectassociation**                       |
| **Othercontextmenus. microsoftdataentitydesigncontext. showindiagram**                           |
| **Othercontextmenus. microsoftdataentitydesigncontext. showinmodelbrowser**                      |
| **Othercontextmenus. microsoftdataentitydesigncontext. storedproceduremapping**                  |
| **Othercontextmenus. microsoftdataentitydesigncontext. TableMapping**                            |
| **Othercontextmenus. microsoftdataentitydesigncontext. updatemodelfromdatabase**                 |
| **Othercontextmenus. microsoftdataentitydesigncontext. Validate**                                |
| **Othercontextmenus. microsoftdataentitydesigncontext. Zoom. 10**                                 |
| **Othercontextmenus. microsoftdataentitydesigncontext. Zoom. 100**                                |
| **Othercontextmenus. microsoftdataentitydesigncontext. Zoom. 125**                                |
| **Othercontextmenus. microsoftdataentitydesigncontext. Zoom. 150**                                |
| **Othercontextmenus. microsoftdataentitydesigncontext. Zoom. 200**                                |
| **Othercontextmenus. microsoftdataentitydesigncontext. Zoom. 25**                                 |
| **Othercontextmenus. microsoftdataentitydesigncontext. Zoom. 300**                                |
| **Othercontextmenus. microsoftdataentitydesigncontext. Zoom. 33**                                 |
| **Othercontextmenus. microsoftdataentitydesigncontext. Zoom. 400**                                |
| **Othercontextmenus. microsoftdataentitydesigncontext. Zoom. 50**                                 |
| **Othercontextmenus. microsoftdataentitydesigncontext. Zoom. 66**                                 |
| **Othercontextmenus. microsoftdataentitydesigncontext. Zoom. 75**                                 |
| **Othercontextmenus. microsoftdataentitydesigncontext. Zoom. Custom**                             |
| **Othercontextmenus. microsoftdataentitydesigncontext. Zoom. ZoomIn**                             |
| **Othercontextmenus. microsoftdataentitydesigncontext. Zoom. ZoomOut**                            |
| **Othercontextmenus. microsoftdataentitydesigncontext. Zoom. zoomdefit**                          |
| **View. entitydatamodelta Browser**                                                                |
| **View. entitydatamodelta Mappingdetails**                                                         |
