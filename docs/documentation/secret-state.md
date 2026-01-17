# Geheimer Zustand (Secret State)

In einigen Spielen kann es notwendig sein, Informationen vor Spielern oder Zuschauern zu verbergen. Zum Beispiel möchtest du in Kartenspielen vielleicht nicht die Karten der Gegner preisgeben.

Dies lässt sich leicht auf der UI-Ebene umsetzen (indem geheime Informationen nicht gerendert werden), aber das Framework bietet auch Unterstützung dafür, solche Daten erst gar nicht an den Client zu senden.

Verwende dazu die Einstellung `playerView` im Spielobjekt. Sie akzeptiert eine Funktion, die ein Objekt mit `G`, `ctx` und `playerID` erhält und eine Version von `G` zurückgibt, die von allen Informationen befreit ist, die vor diesem speziellen Spieler verborgen werden sollen.

```js
const game = {
  // `playerID` kann für Zuschauer auch null oder undefined sein.
  playerView: ({ G, ctx, playerID }) => {
    return StripSecrets(G, playerID);
  },
  // ...
};
```

!> Stelle sicher, dass du die Spielclients den einzelnen Spielern zuordnest (wie im Abschnitt [Multiplayer](multiplayer.md) besprochen).

### PlayerView.STRIP_SECRETS

Das Framework wird mit einer Implementierung von `playerView` ausgeliefert, die Folgendes tut:

- Sie entfernt einen Schlüssel namens `secret` aus `G`.
- Wenn `G` ein `players`-Objekt enthält, entfernt sie alle Schlüssel außer demjenigen, der mit der `playerID` übereinstimmt.

```js
G: {
  secret: { ... },

  players: {
    '0': { ... },
    '1': { ... },
    '2': { ... },
  }
}
```

wird für Spieler `1` zu Folgendem:

```js
G: {
  players: {
    '1': { ... },
  }
}
```

Verwendung:

```js
import { PlayerView } from 'boardgame.io/core';

const game = {
  // ...
  playerView: PlayerView.STRIP_SECRETS,
};
```

### Deaktivieren von Spielzügen, die den geheimen Zustand auf dem Client manipulieren

Spielzüge, die den geheimen Zustand manipulieren, können oft nicht auf dem Client ausgeführt werden, da der Client nicht über alle notwendigen Daten verfügt, um solche Züge zu verarbeiten. Diese können als reine Server-Spielzüge markiert werden, indem `client: false` für den Spielzug gesetzt wird:

```js
moves: {
  moveThatUsesSecret: {
    move: ({ G, random }) => {
      G.secret.value = random.Number();
    },

    client: false,
  }
}
```
