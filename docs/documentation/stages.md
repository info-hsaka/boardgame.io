# Etappen (Stages)

Etappen (Stages) sind eine Möglichkeit, einen Zug in kleinere Teile zu zerlegen. Sie sind nützlich, wenn du den Satz der Spielzüge einschränken möchtest, die ein Spieler ausführen kann. Ein Zug kann in viele Etappen unterteilt sein, von denen jede während dieser Etappe einen anderen Satz von Spielzügen erlaubt.

Etappen sind auch nützlich, um mehr als einen Spieler während eines Zuges spielen zu lassen. Standardmäßig darf nur der `currentPlayer` während eines Zuges Spielzüge ausführen. Einige Spielsituationen erfordern jedoch Spielzüge von anderen Spielern. Zum Beispiel könnte der `currentPlayer` eine Karte spielen, die erfordert, dass jeder andere Spieler im Spiel eine Karte abwirft. Diese Abwürfe müssen nicht in einer bestimmten Reihenfolge erfolgen, und sie sind nicht wirklich separate Züge (der `currentPlayer` kann immer noch andere Karten spielen, bevor der Zug schließlich endet). Etappen sind in solchen Situationen hilfreich.

Wann immer ein oder mehrere Spieler während eines Zuges in eine Etappe eintreten, erlaubt das Framework nur Spielzüge von diesen Spielern (anstatt vom `currentPlayer`). Die Spieler müssen auch nicht alle in derselben Etappe sein (jeder Spieler kann in seiner eigenen Etappe sein). Jeder Spieler, der sich in einer Etappe befindet, wird nun als „aktiver“ Spieler betrachtet, der Spielzüge ausführen kann, wie sie von der Etappe, in der er sich befindet, erlaubt sind.

Du kannst `playerID` innerhalb eines Spielzugs überprüfen, um herauszufinden, welcher Spieler ihn ausgeführt hat. Dies kann in Situationen notwendig sein, in denen mehrere Spieler aktiv sind (und gleichzeitig einen Spielzug machen könnten).

```js
const move = ({ G, ctx, playerID }) => {
  console.log(`Spielzug ausgeführt von Spieler ${playerID}`);
};
```

### Etappen definieren

Etappen werden innerhalb eines `turn`-Abschnitts definiert:

```js
const game = {
  moves: { ... },

  turn: {
    stages: {
      discard: {
        moves: { discardCard },
      },
    },
  },
};
```

Das obige Beispiel definiert eine einzelne `discard`-Etappe, in die Spieler eintreten, wenn sie eine Karte abwerfen müssen. Die Etappe definiert ihren eigenen `moves`-Abschnitt, der angibt, welche Spielzüge ein Spieler in dieser Etappe ausführen kann. Dieser `moves`-Abschnitt überschreibt den globalen `moves`-Abschnitt für Spieler in dieser Etappe vollständig (Spieler dürfen keine Spielzüge aus dem globalen `moves`-Abschnitt ausführen, während sie sich in dieser Etappe befinden). Wenn eine Etappe jedoch keinen `moves`-Abschnitt enthält, können die Spieler Spielzüge aus den globalen `moves` ausführen.

!> Ein in einer Etappe definierter Spielzug kann denselben Namen wie ein globaler Spielzug haben, ist aber in keiner Weise mit dem globalen Äquivalent verwandt.

### In Etappen eintreten

Eine Etappe kann durch Aufrufen des Ereignisses `setStage` betreten werden. Dies versetzt den Spieler, der das Ereignis aufgerufen hat, in die angegebene Etappe:

```js
setStage('discard');
```

### Etappen verlassen

Das Verlassen einer Etappe erfolgt durch Aufrufen des Ereignisses `endStage`. Dies entfernt den Spieler aus der Etappe, in der er sich gerade befindet, und versetzt ihn in einen Zustand zurück, in dem er sich in keiner Etappe befindet.

```js
endStage();
```

Es ist möglich, einen Spieler automatisch in eine andere Etappe zu versetzen, wenn `endStage` aufgerufen wird. Dies geschieht durch Angabe einer `next`-Option in der Etappenkonfiguration.

```js
stages: {
  A: { next: 'B' },
  B: { next: 'C' },
  C: { next: 'A' },
}
```

Im obigen Beispiel wird `endStage` zwischen den drei Etappen wechseln.

### Fortgeschrittenes

Manchmal musst du eine Gruppe von Spielern in eine Etappe versetzen (im Gegensatz zu nur dem Spieler, der das Ereignis aufgerufen hat). Wir verwenden dafür das Ereignis `setActivePlayers`:

```js
setActivePlayers({
  // Versetzt den aktuellen Spieler in eine Etappe.
  currentPlayer: 'stage-name',

  // Versetzt jeden anderen Spieler in eine Etappe.
  others: 'stage-name',

  // Versetzt alle Spieler in eine Etappe.
  all: 'stage-name',

  // Listet die Menge der Spieler und die Etappen auf, in denen sie sich befinden.
  value: {
    '0': 'stage-name',
    '1': 'stage-name',
    ...
  },

  // Verhindert ein manuelles endStage, bevor der Spieler die angegebene Anzahl an Spielzügen gemacht hat.
  minMoves: 1,

  // Ruft endStage automatisch auf, nachdem der Spieler die angegebene Anzahl an Spielzügen gemacht hat.
  maxMoves: 5,

  // Dies setzt die Etappenkonfiguration auf den Wert vor diesem setActivePlayers-Aufruf zurück,
  // sobald die Menge der aktiven Spieler leer wird (da Spieler entweder endStage aufrufen
  // oder maxMoves die Etappe für sie beendet).
  revert: true,

  // Eine next-Option wird verwendet, sobald die Menge der aktiven Spieler leer wird
  // (entweder durch Verwendung von maxMoves oder durch manuelles Entfernen von Spielern).
  // Alle in setActivePlayers verfügbaren Optionen sind auch in next verfügbar.
  next: { ... },
});
```

Kehren wir zu dem Beispiel zurück, das wir vorhin besprochen haben, bei dem wir von jedem anderen Spieler verlangen, eine Karte abzuwerfen, wenn wir eine spielen:

```js
function playCard({ events }) {
  events.setActivePlayers({ others: 'discard', minMoves: 1, maxMoves: 1 });
}

const game = {
  moves: { playCard },
  turn: {
    stages: {
      discard: {
        moves: { discard },
      },
    },
  },
};
```

```react
<iframe class='plain' src='snippets/stages-1' height='160' scrolling='no' title='example' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'></iframe>
```

#### Fortgeschrittene Spielzug-Limits

Die Übergabe eines `minMoves`-Arguments an `setActivePlayers` zwingt alle aktiven Spieler dazu, mindestens diese Anzahl an Spielzügen zu machen, bevor sie die Etappe beenden können. Manchmal möchtest du jedoch unterschiedliche Spielzug-Limits für verschiedene Spieler festlegen. Für Fälle wie diesen unterstützen `setStage` und `setActivePlayers` ausführliche Argumente:

```js
setStage({ stage: 'stage-name', minMoves: 3 });
```

```js
setActivePlayers({
  currentPlayer: { stage: 'stage-name', minMoves: 2 },
  others: { stage: 'stage-name', minMoves: 1 },
  value: {
    '0': { stage: 'stage-name', minMoves: 4 },
  },
});
```

Die Übergabe eines `maxMoves`-Arguments an `setActivePlayers` begrenzt alle aktiven Spieler auf diese Anzahl an Spielzügen. Manchmal möchtest du jedoch unterschiedliche Spielzug-Limits für verschiedene Spieler festlegen. Für Fälle wie diesen unterstützen `setStage` und `setActivePlayers` ausführliche Argumente:

```js
setStage({ stage: 'stage-name', maxMoves: 3 });
```

```js
setActivePlayers({
  currentPlayer: { stage: 'stage-name', maxMoves: 2 },
  others: { stage: 'stage-name', maxMoves: 1 },
  value: {
    '0': { stage: 'stage-name', maxMoves: 4 },
  },
});
```

### Stage.NULL

Manchmal möchtest du einen Spieler zur Menge der aktiven Spieler hinzufügen, ihn aber nicht in einer bestimmten Etappe haben. Du kannst dafür `Stage.NULL` verwenden:

```js
import { Stage } from 'boardgame.io/core';

// Dies erlaubt jedem Spieler einen Spielzug, beschränkt ihn aber nicht auf eine bestimmte Etappe.
setActivePlayers({ all: Stage.NULL });
```

Es gibt auch eine praktische Syntax, um die Spieler aufzulisten, die du in der Menge der aktiven Spieler haben möchtest:

```js
// Die Spieler 0 und 3 werden zur Menge der aktiven Spieler hinzugefügt, und keiner von beiden wird in eine Etappe versetzt.
setActivePlayers(['0', '3']);
```

### Aktive Spieler zu Beginn eines Zuges konfigurieren.

Du kannst `setActivePlayers` automatisch zu Beginn des Zuges aufrufen lassen, indem du einen `activePlayers`-Abschnitt zur `turn`-Konfiguration hinzufügst:

```js
turn: {
  activePlayers: { all: Stage.NULL },
}
```

### Voreinstellungen (Presets)

Eine Reihe von `activePlayers`-Konfigurationen sind als Voreinstellungen verfügbar, die du direkt verwenden kannst:

```js
import { ActivePlayers } from 'boardgame.io/core';

turn: {
  activePlayers: ActivePlayers.ALL;
}
```

#### ALL

Entspricht `{ all: Stage.NULL }`. Jeder Spieler kann spielen und ist nicht auf eine bestimmte Etappe beschränkt.

#### ALL_ONCE

Entspricht `{ all: Stage.NULL, minMoves: 1, maxMoves: 1 }`. Jeder Spieler kann genau einen Spielzug machen, bevor er aus der Menge der aktiven Spieler entfernt wird.

#### OTHERS

Ähnlich wie `ALL`, schließt aber den aktuellen Spieler aus der Menge der aktiven Spieler aus.

#### OTHERS_ONCE

Ähnlich wie `ALL_ONCE`, schließt aber den aktuellen Spieler aus der Menge der aktiven Spieler aus.
