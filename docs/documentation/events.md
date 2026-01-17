# Ereignisse (Events)

Ein Ereignis (Event) wird verwendet, um den Spielzustand voranzubringen. Es ist in gewisser Weise analog zu einem Spielzug, außer dass ein Spielzug `G` ändert, während ein Ereignis `ctx` ändert. Außerdem werden Ereignisse vom Framework bereitgestellt (im Gegensatz zu Spielzügen, die von dir geschrieben werden).

### Ereignistypen

#### endStage

Dieses Ereignis nimmt den Spieler, der es aufgerufen hat, aus der Etappe (Stage), in der er sich befindet. Wenn die Definition für die aktuelle Etappe im Spielobjekt eine `next`-Option angibt, wird der Spieler in die nächste Etappe versetzt. Wenn nicht, kehrt der Spieler in einen Zustand zurück, in dem er sich in keiner Etappe befindet.

```js
endStage();
```

#### endTurn

Dieses Ereignis beendet den Zug. Das Standardverhalten besteht darin, `ctx.turn` um `1` zu erhöhen und den `currentPlayer` gemäß der konfigurierten [Zugreihenfolge](turn-order.md) zum nächsten Spieler voranzubringen (Standard ist ein Round-Robin-Verfahren).

Dieses Ereignis akzeptiert auch ein Argument, das (falls angegeben) den Zug stattdessen auf den angegebenen Spieler umschaltet.

```js
endTurn(); // ohne Argument
endTurn({ next: '2' }); // Spieler 2 ist der nächste Spieler.
```

#### endPhase

Dieses Ereignis beendet die aktuelle Phase. Wenn die Definition für die aktuelle Phase im Spielobjekt eine `next`-Option angibt, wechselt das Spiel in diese Phase. Wenn nicht, kehrt das Spiel in einen Zustand zurück, in dem keine Phase aktiv ist.

```js
endPhase();
```

#### endGame

Dieses Ereignis beendet das Spiel. Wenn du ihm ein Argument übergibst, wird dieses Argument in `ctx.gameover` verfügbar gemacht. Nachdem das Spiel beendet ist, sind weitere Zustandsänderungen am Spiel (über einen Spielzug oder ein Ereignis) nicht mehr möglich. Um die enthaltene Bot/KI-Logik zu ermöglichen, musst du den Gewinner im `endGame`-Ereignis angeben.

```js
endGame({ winner: '2' });
```

#### setStage

Versetzt den Spieler, der das Ereignis aufgerufen hat, in die angegebene Etappe.

```js
setStage('stage-name');
```

#### setPhase

Versetzt das Spiel in die angegebene Phase. Beendet zuerst die aktive Phase.

```js
setPhase('phase-name');
```

#### setActivePlayers

Ermöglicht das Hinzufügen weiterer Spieler zur Menge der „aktiven Spieler“ sowie die Angabe der Etappen, in die sie versetzt werden sollen. Siehe den Leitfaden zu [Etappen](stages.md) für weitere Details.

### Ein Ereignis aus der Spiellogik auslösen

Du kannst Ereignisse aus einem Spielzug oder aus Code innerhalb deiner Spiellogik (z. B. dem `onBegin`-Hook einer Phase) auslösen. Dies geschieht über die `events`-API in dem Objekt, das als erstes Argument an Spielzüge übergeben wird:

```js
moves: {
  drawCard: ({ G, ctx, events }) => {
    events.endPhase();
  };
}
```

!> Ereignisse werden in eine Warteschlange gestellt und **nach** einem Spielzug ausgelöst. Alle Änderungen, die du an `G` vornimmst, werden angewendet, bevor Ereignisse ausgelöst werden, selbst wenn das Ereignis in deiner Spielzug-Funktion zuerst aufgerufen wird.

### Ein Ereignis vom Client auslösen

<!-- tabs:start -->

#### **Plain JS**

Ereignisse sind über die `events`-Eigenschaft einer boardgame.io-Client-Instanz verfügbar. Zum Beispiel:

```js
import { Client } from 'boardgame.io/client';

const client = Client({ /* options */ });

const clickHandler = () => {
  client.events.endTurn();
}
```

#### **React**

Ereignisse sind über `props` innerhalb des `events`-Objekts verfügbar. Zum Beispiel:

```js
import React from 'react';

function Board({ events }) {
  const onClick = () => {
    events.endTurn();
  };

  return <button onClick={onClick}>Zug beenden</button>;
}
```
<!-- tabs:end -->

### Ereignisse deaktivieren

Ereignisse können deaktiviert werden. Zum Beispiel möchtest du vielleicht nicht, dass ein Spieler das Spiel direkt durch einfachen Aufruf des `endGame`-Ereignisses beenden kann.

Um ein Ereignis zu deaktivieren, füge einfach `eventName: false` zum Abschnitt `events` in deiner Spielkonfiguration hinzu.

```js
const game = {
  events: {
    endGame: false,
    // ...
  },
};
```

!> Dies gilt nicht für Ereignisse in Spielzügen oder Hooks, sondern nur für die Möglichkeit, ein Ereignis direkt von einem Client aus aufzurufen.

### Ereignisse aus Hooks aufrufen

Die Ereignis-API ist in Spiel-Hooks ebenso verfügbar wie in Spielzügen. Aufgrund der Art und Weise, wie Hooks und Ereignisse interagieren, können jedoch bestimmte Ereignisse nicht aus bestimmten Hooks aufgerufen werden. Die folgende Tabelle zeigt, welche Hooks welche Ereignisse unterstützen.

|                    | turn<br>`onMove` | turn<br>`onBegin` | turn<br>`onEnd` | phase<br>`onBegin` | phase<br>`onEnd` | game<br>`onEnd` |
|-------------------:|:----------------:|:-----------------:|:---------------:|:------------------:|:----------------:|:---------------:|
|         `setStage` |         ✅        |         ❌         |        ❌        |          ❌         |         ❌        |        ❌        |
|         `endStage` |         ✅        |         ❌         |        ❌        |          ❌         |         ❌        |        ❌        |
| `setActivePlayers` |         ✅        |         ✅         |        ❌        |          ❌         |         ❌        |        ❌        |
|          `endTurn` |         ✅        |         ✅         |        ❌        |          ✅         |         ❌        |        ❌        |
|         `setPhase` |         ✅        |         ✅         |        ✅        |          ✅         |         ❌        |        ❌        |
|         `endPhase` |         ✅        |         ✅         |        ✅        |          ✅         |         ❌        |        ❌        |
|          `endGame` |         ✅        |         ✅         |        ✅        |          ✅         |         ✅        |        ❌        |

✅ = unterstützt &nbsp;&nbsp;&nbsp; ❌ = nicht unterstützt
