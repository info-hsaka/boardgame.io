# Plugins

Die Plugin-API ermöglicht es dir, Objekte zu erstellen, die benutzerdefinierte Funktionen für [boardgame.io](https://boardgame.io/) bereitstellen. Du kannst Wrapper um Spielzüge erstellen, APIs zu `ctx` hinzufügen usw.

### Ein Plugin erstellen

Ein Plugin ist ein Objekt, das die folgenden Felder enthält.

```js
{
  // Erforderlich.
  name: 'plugin-name',

  // Initialisiert die Daten des Plugins.
  // Diese werden in einem speziellen Bereich des Zustandsobjekts gespeichert
  // und sind für die Spielzug-Funktionen nicht sichtbar.
  setup: ({ G, ctx, game }) => datenobjekt,

  // Erstellt ein Objekt, das in `ctx` unter `ctx['plugin-name']` verfügbar wird.
  // Dies wird am Anfang eines Spielzugs oder Ereignisses aufgerufen.
  // Dieses Objekt wird im Speicher gehalten, bis flush (unten) aufgerufen wird.
  api: ({ G, ctx, game, data, playerID }) => api-objekt,

  // Gibt eine aktualisierte Version der Daten zurück, die im
  // Zustandsobjekt des Spiels gespeichert werden.
  flush: ({ G, ctx, game, data, api }) => datenobjekt,

  // Funktion, die einen Spielzug / eine Trigger-Funktion entgegennimmt
  // und eine andere Funktion zurückgibt, die diese umschließt. Dieser
  // Wrapper kann G ändern, bevor er es an die umschlossene Funktion weitergibt.
  // Es ist gute Praxis, die Änderung am Ende des Aufrufs rückgängig zu machen.
  // `fnType` gibt den Typ des umschlossenen Hooks an und ist einer der
  // `GameMethod`-Werte — import { GameMethod } from 'boardgame.io/core'
  fnWrap: (fn, fnType) => ({ G, ...rest }, ...args) => {
    G = preprocess(G);
    G = fn({ G, ...rest }, ...args);
    if (fnType === GameMethod.TURN_ON_END) {
      // wird nur ausgeführt, wenn die onEnd-Funktion eines Zuges umschlossen wird
    }
    G = postprocess(G);
    return G;
  },

  // Funktion, mit der das Plugin angeben kann, dass es nicht auf dem Client ausgeführt werden soll.
  // Wenn sie true zurückgibt, verwirft der Client die Zustandsaktualisierung und wartet stattdessen auf den Master.
  noClient: ({ G, ctx, game, data, api }) => boolean,

  // Funktion, mit der das Plugin angeben kann, dass die aktuelle Aktion als ungültig erklärt
  // und abgebrochen werden soll. Wenn `isInvalid` eine Fehlermeldung zurückgibt, wird die gesamte
  // Aktualisierung abgebrochen und ein Fehler an den Client zurückgegeben.
  isInvalid: ({ G, ctx, game, data, api }) => false | string,

  // Funktion, die `data` filtern kann, um geheimen Zustand zu verbergen,
  // bevor er an einen bestimmten Client gesendet wird.
  // `playerID` könnte für Zuschauer auch null oder undefined sein.
  playerView: ({ G, ctx, game, data, playerID }) => gefiltertes datenobjekt,
}
```

### Plugins zu Spielen hinzufügen

Die Liste der Plugins wird in der Spielspezifikation angegeben.

```js
import { PluginA, PluginB } from 'boardgame.io/plugins';

const game = {
  name: 'my-game',

  plugins: [PluginA, PluginB],

  // ...
};
```

?> Plugins werden nacheinander in der Reihenfolge angewendet, in der sie angegeben sind (von links nach rechts).

### Plugins konfigurieren

Einige Plugins benötigen möglicherweise eine Konfiguration durch den Benutzer. Der empfohlene Weg besteht darin, das Plugin als Factory-Funktion zu entwerfen, die die Konfiguration als Argumente entgegennimmt und ein Plugin-Objekt zurückgibt.

```js
import { ConfigurablePlugin } from './plugins';

const game = {
  name: 'my-game',
  plugins: [
    ConfigurablePlugin(options),
  ],
}
```

?> Siehe `PluginPlayer` unten für ein praktisches Beispiel hierfür.

### Verfügbare Plugins

#### PluginPlayer

```js
import { PluginPlayer } from 'boardgame.io/plugins';

// definiere eine Funktion, um den Zustand jedes Spielers zu initialisieren
const playerSetup = (playerID) => ({ ... });

// filtere die an jeden Client zurückgegebenen Daten, um geheimen Zustand zu verbergen (OPTIONAL)
const playerView = (players, playerID) => ({
  [playerID]: players[playerID],
});

const game = {
  plugins: [
    // übergib deine Funktion an das Spieler-Plugin
    PluginPlayer({
      setup: playerSetup,
      playerView: playerView,
    }),
  ],
};
```

`PluginPlayer` erleichtert die Verwaltung des Spielerzustands. Es erstellt ein Objekt `players`, das den Zustand für einzelne Spieler speichert. Dieses Objekt wird im privaten Speicherbereich des Plugins gespeichert:

```
players: {
  '0': { ... },
  '1': { ... },
  '2': { ... },
  ...
}
```

Die Anfangswerte dieser Zustände werden durch die `setup`-Funktion in ihrem Optionsobjekt bestimmt, die den Zustand für eine bestimmte `playerID` erstellt.

Der mit dem aktuellen Spieler verknüpfte Datensatz kann über `ctx.player.get()` abgerufen werden. Wenn es sich um ein Spiel für 2 Spieler handelt, ist der Datensatz des Gegners über `ctx.player.opponent.get()` verfügbar. Diese Felder können mit ihren entsprechenden `set()`-Versionen geändert werden.

```js
ctx.player.get() // Den Datensatz des aktuellen Spielers abrufen.
ctx.player.set() // Den Datensatz des aktuellen Spielers aktualisieren.
ctx.player.opponent.get() // Den Datensatz des gegnerischen Spielers abrufen.
ctx.player.opponent.set() // Den Datensatz des gegnerischen Spielers aktualisieren.
```
