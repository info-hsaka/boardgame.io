# Zugreihenfolge (Turn Order)

Das Standardverhalten des Frameworks besteht darin, den Zug im Round-Robin-Verfahren (Reihum-Verfahren) weiterzugeben. Ein Spieler führt einen oder mehrere Spielzüge aus, bevor er ein `endTurn`-Ereignis auslöst, welches den Zug an den nächsten Spieler weitergibt.

Der Zustand der Zugreihenfolge wird in den folgenden Feldern verwaltet:

```js
ctx: {
  currentPlayer: '0',
  playOrder: ['0', '1', '2', ...],
  playOrderPos: 0,
}
```

##### `currentPlayer`

Dies ist der Besitzer des aktuellen Zuges und normalerweise der einzige Spieler, der während des Zuges Spielzüge ausführen kann. Du kannst über [Etappen (Stages)](stages.md) auch weiteren Spielern erlauben, während des Zuges Spielzüge zu machen.

##### `playOrder`

Der Standardwert ist `['0', '1', '2', ... ]`. Man kann sich dies als die Reihenfolge vorstellen, in der sich die Spieler an den Tisch setzen. Eine Round-Robin-Zugreihenfolge würde den `currentPlayer` der Reihe nach durch diese Liste bewegen.

##### `playOrderPos`

Ein Index für `playOrder`. Dies ist der Wert, der durch die Zugreihenfolgen-Richtlinie (Turn Order Policy) aktualisiert wird, um den `currentPlayer` zu berechnen. Das Standardverhalten besteht darin, ihn einfach im Round-Robin-Verfahren zu erhöhen. `currentPlayer` ist dann einfach `playOrder[playOrderPos]`.

### Ändern der Zugreihenfolge

Das Ändern der Zugreihenfolge des Spiels erfolgt über die Option `order` innerhalb des `turn`-Abschnitts der Spielkonfiguration:

```js
import { TurnOrder } from 'boardgame.io/core';

const game = {
  turn: {
    order: TurnOrder.ONCE,
  },
};
```

Typischerweise wirst du eine der unten aufgeführten Voreinstellungen verwenden. Du kannst die Zugreihenfolge auch in jeder Phase des Spiels ändern. Siehe den Leitfaden zu [Phasen](phases.md) für weitere Details.

### Voreinstellungen (Presets)

#### DEFAULT

Dies ist das Standard-Round-Robin-Verfahren. Es wird verwendet, wenn du keine Zugreihenfolge angibst.

#### RESET

Dies ähnelt `DEFAULT`, aber anstatt die vorherige Position zu Beginn einer Phase zu erhöhen, wird immer bei `0` begonnen.

#### CONTINUE

Dies ähnelt ebenfalls `DEFAULT`, aber anstatt die vorherige Position zu Beginn einer Phase zu erhöhen, wird mit dem Spieler begonnen, der die vorherige Phase beendet hat.

#### ONCE

Dies ist ein weiteres Round-Robin-Verfahren, das jedoch nur einmal reihum geht. Danach endet die Phase automatisch.

#### CUSTOM

Round-Robin wie `DEFAULT`, setzt aber `playOrder` auf den angegebenen Wert.

```js
turn: {
  order: TurnOrder.CUSTOM(['1', '3']),
}
```

#### CUSTOM_FROM

Round-Robin wie `DEFAULT`, setzt aber `playOrder` auf den Wert eines bestimmten Feldes in `G`.

```js
turn: {
  order: TurnOrder.CUSTOM_FROM('property_in_G'),
}
```

### Ad Hoc

Du kannst den nächsten Spieler auch während des `endTurn`-Ereignisses festlegen.

```js
endTurn({ next: playerID });
```

Dieses Argument kann auch der Rückgabewert von `turn.endIf` sein und funktioniert auf die gleiche Weise.

In beiden folgenden Beispielen wird Spieler `3` zum neuen Spieler gemacht:

```js
function Move({ events }) {
  events.endTurn({ next: '3' });
}
```

```js
const game = {
  turn: {
    endIf: () => ({ next: '3' }),
  },
};
```

### Eine benutzerdefinierte Zugreihenfolge erstellen

Wenn die oben genannten Voreinstellungen nicht das sind, was du suchst, kannst du eine benutzerdefinierte Zugreihenfolge von Grund auf neu erstellen:

```js
turn: {
  order: {
    // Ermittelt den Anfangswert von playOrderPos.
    // Dies wird zu Beginn der Phase aufgerufen.
    first: ({ G, ctx }) => 0,

    // Ermittelt den nächsten Wert von playOrderPos.
    // Dies wird am Ende jedes Zuges aufgerufen.
    // Die Phase endet, wenn dies undefined zurückgibt.
    next: ({ G, ctx }) => (ctx.playOrderPos + 1) % ctx.numPlayers,

    // OPTIONAL:
    // Überschreibt den Anfangswert von playOrder.
    // Dies wird zu Beginn des Spiels / der Phase aufgerufen.
    playOrder: ({ G, ctx }) => [...],
  }
}
```
