# Unveränderlichkeit (Immutability)

Das Prinzip der Unveränderlichkeit (Immutability), angewandt auf zustandsändernde Funktionen wie Spielzüge in [boardgame.io](https://boardgame.io/), schreibt vor, dass diese reine Funktionen (Pure Functions) sein müssen. Das bedeutet, dass du nicht von einem **externen Zustand** abhängen darfst und auch keine **Nebenwirkungen** (Side-Effects) haben darfst, d. h. du darfst nichts ändern, was keine lokale Variable ist (nicht einmal die Argumente).

Die Vorteile einer Systemarchitektur nach diesem Prinzip bestehen darin, dass du die Wiederholbarkeit sicherstellen kannst (Spielzüge können über einem bestimmten Zustandswert mehrmals an verschiedenen Stellen abgespielt werden) und du günstige Vergleiche durchführen kannst, um zu prüfen, ob sich etwas geändert hat.

Eine traditionelle reine Funktion akzeptiert einfach Argumente und gibt dann den neuen Zustand zurück. Etwa so:

```js
function move({ G }) {
  // Gib den neuen Wert von G zurück, ohne die Argumente zu ändern.
  return { ...G, hand: G.hand + 1 };
}
```

?> Das obige Beispiel verwendet die [Spread-Syntax](https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Operators/Spread_syntax), um ein neues Objekt zu erstellen.

[boardgame.io](https://boardgame.io/) bietet eine bequemere Syntax, indem es dir erlaubt, `G` direkt zu mutieren, während intern eine [Bibliothek](https://github.com/mweststrate/immer) verwendet wird, um deinen Spielzug in eine reine Funktion umzuwandeln, die das Prinzip der Unveränderlichkeit respektiert. Beide Stile werden gleichermaßen unterstützt, verwende also denjenigen, den du bevorzugst.

```js
function move({ G }) {
  G.hand++;
}
```

?> Beachte, dass du in diesem Stil den neuen Zustand nicht zurückgibst. Tatsächlich gilt es als Fehler, etwas zurückzugeben und gleichzeitig `G` zu mutieren.

!> Du kannst nur `G` ändern. Andere Werte, die an deine Spielzüge übergeben werden, sind schreibgeschützt und sollten niemals in einem der beiden Stile geändert werden. Änderungen an `ctx` können über [Ereignisse](events.md) vorgenommen werden.

### Ungültige Spielzüge

In beiden Stilen werden ungültige Spielzüge durch die Rückgabe einer speziellen Konstante angezeigt. Dies teilt dem Framework mit, dass der aktuelle Satz von übergebenen Argumenten unzulässig ist und der Spielzug verworfen werden sollte. Dies könntest du zum Beispiel tun, wenn der Benutzer versucht, in Tic-Tac-Toe auf eine bereits ausgefüllte Zelle zu klicken.

```js
import { INVALID_MOVE } from 'boardgame.io/core';

moves: {
  clickCell: function({ G, ctx }, id) {
    // Unzulässiger Spielzug: Zelle ist bereits ausgefüllt.
    if (G.cells[id] !== null) {
      return INVALID_MOVE;
    }

    // Zelle mit 0 oder 1 füllen, je nach aktuellem Spieler.
    G.cells[id] = ctx.currentPlayer;
  }
}
```

### Weiterführende Literatur

[Immutable Update Patterns](https://redux.js.org/recipes/structuring-reducers/immutable-update-patterns)
