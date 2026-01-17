# Konzepte

### Zustand

boardgame.io erfasst den Spielzustand in zwei Objekten: `G` und `ctx`.

```js
{
  // Der Spielzustand (von dir verwaltet).
  G: {},

  // Schreibgeschützte Metadaten (vom Framework verwaltet).
  ctx: {
    turn: 0,
    currentPlayer: '0',
    numPlayers: 2,
  }
}
```

Diese Zustandsobjekte werden überall weitergereicht und nahtlos
sowohl auf dem Client als auch auf dem Server gepflegt. Der Zustand in `ctx` ist
inkrementell adaptierbar, was bedeutet, dass du den gesamten
Zustand manuell in `G` verwalten kannst, wenn du dies wünschst.

?> `ctx` enthält weitere Felder, die hier nicht gezeigt werden und die Spiele
nutzen können, einschließlich der Unterstützung für Spielphasen und komplexe
Zugreihenfolgen.

!> Da der Zustand zwischen Client und Server gesendet werden kann,
muss `G` ein JSON-serialisierbares Objekt sein; insbesondere darf es
keine Klassen oder Funktionen enthalten.

### Spielzüge (Moves)

Dies sind Funktionen, die dem Framework mitteilen, wie `G` geändert werden soll,
wenn ein bestimmter Spielzug ausgeführt wird. Sie dürfen nicht von
externem Zustand abhängen oder Nebenwirkungen haben (außer der Änderung von `G`).
Siehe den Leitfaden zur [Immutabilität](immutability.md) für Informationen darüber,
wie Unveränderlichkeit vom Framework gehandhabt wird.

```js
moves: {
  drawCard: ({ G, ctx }) => {
    const card = G.deck.pop();
    G.hand.push(card);
  },

  // ...
}
```

Auf dem Client verwendest du ein `moves`-Objekt, um deine
Spielzug-Funktionen aufzurufen.

<!-- tabs:start -->
#### **Plain JS**

Du kannst auf `moves` über eine Instanz des Plain-JavaScript-Clients zugreifen:

```js
client.moves.drawCard();
```

#### **React**

In React wird `moves` über die `props` deiner Komponente bereitgestellt:

```js
props.moves.drawCard();
```

<!-- tabs:end -->

### Ereignisse (Events)

Dies sind vom Framework bereitgestellte Funktionen, die analog zu Spielzügen sind, außer dass sie auf `ctx` wirken. Diese bringen den Spielzustand typischerweise voran, indem sie Dinge tun wie
den Zug beenden, die Spielphase ändern usw.
Ereignisse werden vom Client auf ähnliche Weise wie Spielzüge ausgelöst.

<!-- tabs:start -->
#### **Plain JS**
```js
client.events.endTurn();
```

#### **React**
```js
props.events.endTurn();
```
<!-- tabs:end -->

Weitere Details findest du im Leitfaden zu [Ereignissen](events.md).

### Phase

Eine Phase ist ein Zeitraum im Spiel, der die Spielkonfiguration
überschreibt, während sie aktiv ist. Zum Beispiel kannst du während
einer Phase einen anderen Satz von Spielzügen oder eine andere Zugreihenfolge verwenden. Das Spiel kann zwischen verschiedenen Phasen wechseln, und Züge
finden innerhalb von Phasen statt. Siehe den Leitfaden zu [Phasen](phases.md) für weitere Details.

### Zug (Turn)

Ein Zug ist ein Zeitraum des Spiels, der einem einzelnen
Spieler zugeordnet ist. Er besteht typischerweise aus einem oder mehreren Spielzügen, die von
diesem Spieler ausgeführt werden, bevor er an einen anderen Spieler weitergegeben wird. Du kannst
auch anderen Spielern erlauben, während deines Zuges zu spielen, obwohl
dies weniger üblich ist. Siehe den Leitfaden zur
[Zugreihenfolge](turn-order.md) für weitere Details.

### Etappe (Stage)

Eine Etappe (Stage) ist ähnlich wie eine Phase, außer dass sie innerhalb eines Zuges stattfindet und
für einzelne Spieler gilt und nicht für das Spiel als Ganzes.
Ein Zug kann in viele Etappen unterteilt sein, von denen jede einen anderen Satz von Spielzügen erlaubt
und andere Spielkonfigurationsoptionen überschreibt, während diese Etappe aktiv ist.
Außerdem können sich verschiedene Spieler während eines Zuges in unterschiedlichen Etappen befinden.
Siehe den Leitfaden zu [Etappen](stages.md) für weitere Details.
