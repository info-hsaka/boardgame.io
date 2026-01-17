# Tutorial

Das Ziel dieses Tutorials ist es, ein einfaches Tic-Tac-Toe-Spiel mit boardgame.io zu erstellen.
Du wirst die grundlegenden Konzepte von boardgame.io kennenlernen und erfahren, wie man sie einsetzt. Am Ende wirst du ein funktionierendes Spiel haben.

[node]: https://nodejs.dev/learn/how-to-install-nodejs
[cmd]: https://tutorial.djangogirls.org/en/intro_to_command_line/
[vsc]: https://code.visualstudio.com/
[atom]: https://atom.io/

## Setup

Klone das boardgame.io-Template-Repository von https://github.com/info-hsaka/boardgame-template .
(Siehe hier für Anweisungen dazu: https://js.oc.is/docs/intro/setup/#34-clone-git-repository)
Wie zuvor müssen wir einige zusätzliche Programme installieren, indem wir `npm i` im Ordner ausführen. Schau hier für detailliertere (und Windows!) Anweisungen: https://js.oc.is/docs/intro/setup/#35-weitere-programme-installieren

Wie beim JS-Tutorial solltest du auf die Schaltfläche „Run“ klicken können, um das Spiel zu starten. (Siehe https://js.oc.is/docs/intro/howto/ für eine Auffrischung dazu)
Du solltest eine Website sehen können, wenn du in deinem Browser zu http://localhost:3000/ navigierst.

Sobald du das Template-Repository geklont hast, wird dieses Tutorial in der Datei `src/TicTacToe.js` stattfinden. Du kannst dir die anderen Dateien im Ordner `src` ansehen, musst sie aber für dieses Tutorial nicht ändern. Die Datei `src/Game.js` enthält ein Spielobjekt mit ein paar weiteren hinzugefügten Funktionen, damit du ein Gefühl dafür bekommst, was später möglich sein wird. Vorerst konzentrieren wir uns auf das `TicTacToe`-Objekt in der Datei `TicTacToe.js`.

## Ein Spiel definieren

Wir definieren ein Spiel, indem wir ein Objekt erstellen, das Informationen über dein Spiel enthält, um
boardgame.io mitzuteilen, wie es funktioniert. Mehr oder weniger alles
ist optional, sodass wir einfach anfangen und nach und nach mehr Komplexität hinzufügen können.
Im Template sind die meisten Funktionen bereits definiert, aber nicht ausgefüllt, was bedeutet, dass du die relevanten Teile ausfüllen musst.

Zu Beginn füllen wir die `setup`-Funktion in der Datei `src/Game.js` aus, die den
Anfangswert des Spielzustands `G` festlegt.

?> Der Spielzustand `G` ist ein einfaches JavaScript-Objekt, das den Zustand des Spiels repräsentiert, wie wir es während der Präsenzveranstaltung besprochen haben. Wenn du zu irgendeinem Zeitpunkt Fragen zu Teilen von boardgame.io oder anderen Konzepten hast, die wir hier verwenden, kannst du gerne fragen und dich auch im Rest der Dokumentation hier umsehen.

```js
export const TicTacToe = {
  // Fülle die Zellen des Tic-Tac-Toe-Spielfelds mit `null`, um anzuzeigen, dass sie leer sind.
  // Dies ist der Anfangszustand des Spiels.
  // Diese Syntax mag neu sein, aber wir definieren hier nur eine Funktion in einem JavaScript-Objekt.
  // Die Funktion wird im Feld `setup` des `TicTacToe`-Objekts verfügbar sein und würde
  // so aufgerufen werden: `TicTacToe.setup()`. Wir müssen sie jedoch nicht selbst aufrufen, boardgame.io wird das für uns tun.
  setup: function setup() {
    return { cells: [null, null, null, null, null, null, null, null, null] }
  }
};
```

Als Nächstes kommt das `moves`-Objekt.

Ein Spielzug (Move) ist eine Funktion, die eine Eingabe entgegennimmt (z. B. die Zelle, auf die ein Spieler geklickt hat) und `G` auf den gewünschten neuen Zustand aktualisiert.
„Moves“ repräsentieren Dinge, die ein Spieler im Spiel tun kann. In Tic-Tac-Toe ist es ziemlich einfach: Ein Spieler kann auf eine Zelle klicken, um dort seine Markierung zu setzen.

Das `moves`-Objekt ist eine Sammlung aller möglichen Spielzüge mit ihren Namen als normales JavaScript-Objekt:

```js
export const TicTacToe = {
  // Ich werde nicht alles im Objekt wiederholen, nur neue Teile, die hinzugefügt wurden
  // ...

  moves: {
    clickCell: () => {
      console.log("Ein clickCell-Spielzug wurde ausgeführt!")
    },
  },
}
```

Spielzug-Funktionen erhalten ein Argument, das einige Felder enthält, vor allem `G` und `playerID`. `G` ist das Spielzustandsobjekt, das wir mit der `setup`-Funktion eingerichtet haben, und `playerID` ist die ID (Abkürzung für Identifikator, in unserem Fall 0 oder 1) des Spielers, der den Spielzug gemacht hat. Das zweite Argument kann alles andere sein, was wir übergeben müssen, um einen gültigen Spielzug auszuführen. Wir werden später sehen, wie man das benutzt. In diesem Fall müssen wir wissen, in welche Zelle der Spieler sein X/O setzen möchte, also fügen wir der Funktion `clickCell` ein zweites Argument `cellIndex` hinzu.

```js
export const TicTacToe = {
  // ...

  moves: {
    clickCell: function clickCell(move, cellIndex) {
      console.log("Spieler Nummer " + move.playerID + " möchte seine Markierung in Zelle " + cellIndex + " setzen")
    },
  },
}
```

Um nun den Spielzustand zu aktualisieren, aktualisieren wir einfach `G` in unserer Spielzug-Funktion:

```js
export const TicTacToe = {
  // ...

  moves: {
    clickCell: function clickCell(move, cellIndex) {
      // cells ist das Array, das wir in der setup-Funktion eingerichtet haben, und wir weisen dem cellIndex einfach die playerID zu,
      // um anzuzeigen, welcher Spieler dort seine Markierung gesetzt hat.
      move.G.cells[cellIndex] = move.playerID;
    },
  },
}
```

?> Die `setup`-Funktion erhält ebenfalls ein Objekt als erstes Argument,
genau wie Spielzüge. Dies ist nützlich, wenn du den Anfangszustand
basierend auf einem Feld in `ctx` anpassen musst – zum Beispiel der Anzahl der Spieler –
aber für Tic-Tac-Toe brauchen wir das nicht.

Zu diesem Zeitpunkt sollte deine Datei `TicTacToe.js` so aussehen:

```js
export const TicTacToe = {
  setup: function setup() {
    return { cells: [null, null, null, null, null, null, null, null, null] }
  },

  moves: {
    clickCell: function clickCell(move, cellIndex) {
      // cells ist das Array, das wir in der setup-Funktion eingerichtet haben, und wir weisen dem cellIndex einfach die playerID zu,
      // um anzuzeigen, welcher Spieler dort seine Markierung gesetzt hat.
      move.G.cells[cellIndex] = move.playerID;
    },
  },
}
```

Du kannst jetzt auf die Schaltfläche „Run“ klicken, um das Spiel in Aktion zu sehen. Gehe zu http://localhost:3000/, um das Spiel zu sehen.

Zu diesem Zeitpunkt solltest du ein leeres Tic-Tac-Toe-Spielfeld und das boardgame.io Debug Panel sehen.
Dieses Panel bedeutet, dass wir unser Tic-Tac-Toe-Spiel bereits spielen können!

Du kannst einen Spielzug machen, indem du im Debug Panel auf `clickCell` klickst, eine Zahl zwischen `0` und `8` in die Klammern `()` eingibst und **Enter** drückst. Der aktuelle Spieler wird einen Spielzug auf der gewählten Zelle ausführen. Die Zahl, die du eingibst, ist die `id`, die als erstes Argument nach `move` an die Funktion `clickCell` übergeben wird. Beachte, wie sich das `cells`-Array im Debug Panel aktualisiert, während du Spielzüge machst. Du kannst den Zug beenden, indem du auf `endTurn` klickst und **Enter** drückst. Der nächste Aufruf von `clickCell` führt zu einer „1“ in der gewählten Zelle anstelle einer „0“.

?> Du kannst das Debug Panel ausschalten, indem du `debug: false` in der `Client`-Konfiguration übergibst.

## Spielverbesserungen

### Spielzüge validieren

Bisher wird eine bereits ausgefüllte Zelle überschrieben, wenn ein Spieler `clickCell` aufruft. Lassen wir das verhindern, indem wir `clickCell` so aktualisieren, dass es uns mitteilt, dass ein Spielzug ungültig ist, wenn die ausgewählte Zelle nicht `null` ist.

Spielzüge können dem Framework mitteilen, dass sie ungültig sind, indem sie eine spezielle Konstante zurückgeben, die wir in `src/Game.js` importieren:

```js
import { INVALID_MOVE } from 'boardgame.io/core';
```

Jetzt können wir `INVALID_MOVE` von `clickCell` zurückgeben:

```js
import { INVALID_MOVE } from 'boardgame.io/core';

export const TicTacToe = {
  // ...

  moves: {
    clickCell: function clickCell(move, cellIndex) {
      if (move.G.cells[cellIndex] !== null) {
        return INVALID_MOVE;
      }
      move.G.cells[cellIndex] = move.playerID;
    },
  },
}
```

### Züge verwalten

Im Debug Panel haben wir auf `endTurn` geklickt, um den Zug nach einem Spielzug an den nächsten Spieler zu übergeben. Das könnten wir auch in unserem Client-Code tun: einen Spielzug machen und dann den Zug beenden. Das wäre flexibel, weil ein Spieler selbst entscheiden könnte, wann er seinen Zug beendet, aber in Tic-Tac-Toe wissen wir, dass der Zug immer enden sollte, wenn ein Spielzug gemacht wurde.

Es gibt verschiedene Möglichkeiten, Züge in boardgame.io zu verwalten.
Wir verwenden die Option `maxMoves` in unserer Spieldefinition, um dem
Framework mitzuteilen, dass der Zug eines Spielers automatisch nach einem einzigen
Spielzug beendet werden soll, sowie die Option `minMoves`, damit Spieler einen Spielzug machen *müssen* und nicht einfach `endTurn` aufrufen können.

```js
export const TicTacToe = {
  setup: // ...
  moves: { /* ... */ },

  turn: {
    minMoves: 1,
    maxMoves: 1,
  },
}
```

Versuche noch einmal, im Debug Panel mit dem Spiel herumzuspielen. Du solltest sehen, dass du keinen Spielzug in einer bereits gefüllten Zelle machen kannst und dass der Zug automatisch endet, nachdem ein Spielzug gemacht wurde.

?> Mehr erfährst du in den Leitfäden [Zugreihenfolge](turn-order.md)
    und [Ereignisse](events.md).

### Siegbedingung

Das Tic-Tac-Toe-Spiel, das wir bisher haben, endet eigentlich nie.
Lassen wir den Gewinner nachverfolgen, falls ein Spieler das Spiel gewinnt.

Um das zu tun, müssen wir zuerst wissen, ob ein Spieler gewonnen hat und wenn ja, welcher.

Tic-Tac-Toe endet oft unentschieden, daher müssen wir das ebenfalls behandeln. Wir können eine Hilfsfunktion hinzufügen, um zu prüfen, ob das Spiel vorbei ist, d. h. alle Zellen gefüllt sind.

```js
function isDraw(cells) {
  // Gib `true` zurück, wenn alle Zellen gefüllt sind, andernfalls `false`
}
```

Schreibe den Code für diese Funktion wie beschrieben, und wir werden später sehen, wie man sie benutzt.

Das andere Problem, das wir haben, ist herauszufinden, ob jemand das Spiel gewonnen hat. In Tic-Tac-Toe gewinnt ein Spieler, wenn er drei seiner Markierungen in einer Reihe hat, entweder horizontal, vertikal oder diagonal. Wir sollten eine Hilfsfunktion schreiben, um zu prüfen, ob ein Spieler gewonnen hat:

```js
function isVictory(cells) {
  // Gib die playerID des Gewinners zurück, falls es einen gibt, andernfalls `null`
}
```

Hinweise: `cells` ist ein Array mit genau 9 Elementen und jedes Element ist entweder `null`, `0` oder `1`. Stell dir vor, die Elemente des Arrays sind wie folgt angeordnet:

```
0 | 1 | 2
3 | 4 | 5
6 | 7 | 8
```

Und schreibe dann eine Funktion, die jede Reihe, Spalte und Diagonale auf einen Sieg prüft.

Da wir nun diese Funktionen haben, fügen wir unserem Spiel eine `endIf`-Methode hinzu.
Diese Methode wird jedes Mal aufgerufen, wenn sich unser Zustand aktualisiert, um zu prüfen, ob das Spiel vorbei ist.

```js
export const TicTacToe = {
  // setup, moves, etc.

  endIf: function endIf(endIf) {
    const winner = isVictory(endIf.G.cells);
    if (winner != null) {
      // unsere isVictory-Funktion hat eine playerID zurückgegeben, also ist das Spiel vorbei und wir haben einen Gewinner
      return { winner: winner };
    }
    // wenn es keinen Gewinner gibt, prüfe, ob das Spiel unentschieden ist
    if (isDraw(endIf.G.cells)) {
      // das Spiel ist unentschieden, also teilen wir boardgame.io dieses Ergebnis mit
      return { draw: true };
    }
  },
};
```

?> `endIf` nimmt eine Funktion entgegen, die bestimmt, ob das Spiel vorbei ist. Wenn sie irgendetwas zurückgibt, endet das Spiel und der Rückgabewert ist unter `ctx.gameover` verfügbar. Damit Bots (siehe unten) richtig funktionieren, sollte der Rückgabewert ein Objekt mit einem `winner`-Schlüssel sein, falls es einen Gewinner gibt. Siehe das Beispiel oben.

## Bots

In diesem Abschnitt zeigen wir dir, wie du einen Bot hinzufügst, der in der Lage ist, dein Spiel zu spielen. Wir müssen dem Bot mitteilen, welche Spielzüge im Spiel erlaubt sind, und er wird Spielzüge finden, die tendenziell zu Sieg-Ergebnissen führen.

Um dies zu tun, füge einen `ai`-Abschnitt zur Spieldefinition hinzu.
Die Funktion `enumerate` sollte ein Array mit möglichen Spielzügen zurückgeben, in unserem Fall also einen `clickCell`-Spielzug für jede leere Zelle.

```js
export const TicTacToe = {
  // setup, turn, moves, endIf ...

  ai: {
    enumerate: function enumerate(G)  {
      // diese Funktion gibt jedes Mal die Zelle oben links als einzigen möglichen Spielzug zurück
      // Ändere diese Funktion so, dass sie alle möglichen Tic-Tac-Toe-Spielzüge zurückgibt, damit der
      // Bot das Spiel tatsächlich basierend auf `G.cells` spielt.
      // Überlege dir, welche Spielzüge in Tic-Tac-Toe möglich sind und wie du sie in unserem cells-Array finden kannst.
      return [{ move: 'clickCell', args: [0] }];
    },
  },
};
```

Das war's! Jetzt kannst du den KI-Abschnitt des Debug Panels besuchen:

- `play` veranlasst den Bot, einen einzelnen Spielzug zu berechnen und auszuführen
  (Shortcut: <kbd>2</kbd>)

- `simulate` veranlasst den Bot, das gesamte Spiel allein zu spielen
  (Shortcut: <kbd>3</kbd>)

`play` hilft dir dabei, Spielzüge, die du selbst machst, mit Bot-Zügen zu kombinieren. Du kannst zum Beispiel einige manuelle Spielzüge machen, um zwei in eine Reihe zu bekommen, und dann überprüfen, ob der Bot einen Block setzt.

?> Der Bot verwendet intern [MCTS](https://nicolodavis.com/blog/tic-tac-toe/), um den Spielbaum zu erkunden und gute Spielzüge zu finden. Standardmäßig werden 1000 Iterationen pro Spielzug verwendet. Dies kann konfiguriert werden, um die Spielstärke des Bots anzupassen.

## Weiterführende Literatur und was als Nächstes kommt

Du kannst gerne noch weiter mit Tic-Tac-Toe herumspielen und dir überlegen, wie sich andere Spiele auf die gelernten Konzepte übertragen lassen.

In einem zukünftigen Tutorial wirst du auch lernen, wie du die Benutzeroberfläche tatsächlich selbst zeichnest, wie du sie hübsch machst und wie du auf Benutzereingaben reagierst.

Schau dich gerne in den anderen Dateien im Template um, aber mach dir keine Sorgen, wenn du noch nicht alles verstehst. Das Tutorial-Template verwendet Konzepte, die wir während der HSAKA gar nicht benötigen werden (wie HTML und CSS), da wir eine andere Technologie zum Zeichnen der Benutzeroberfläche verwenden werden.

Wenn du das Gefühl hast, dass du alles verstehst, was in der Tic-Tac-Toe-Datei vor sich geht, ist das mehr als genug.

