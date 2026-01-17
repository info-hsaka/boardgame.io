# Phasen

Die meisten Spiele, die über sehr einfache hinausgehen, neigen dazu, in verschiedenen Phasen unterschiedliche Verhaltensweisen zu zeigen. Ein Spiel könnte zum Beispiel zu Beginn eine Phase haben, in der die Spieler Karten ziehen (Drafting), bevor sie in eine Spielphase eintreten.

Jede Phase in [boardgame.io](https://boardgame.io/) definiert einen Satz von Spielkonfigurationsoptionen, die für die Dauer dieser Phase angewendet werden. Dies beinhaltet die Möglichkeit, einen anderen Satz von Spielzügen zu definieren, eine andere Zugreihenfolge zu verwenden usw. **Züge finden innerhalb von Phasen statt**.

### Kartenspiel

Beginnen wir mit einem einfachen Beispiel eines Spiels, das genau zwei Spielzüge hat:

- eine Karte vom Stapel auf die Hand ziehen.
- eine Karte von der Hand auf den Stapel spielen.

```js
function drawCard({ G, playerID }) {
  G.deck--;
  G.hand[playerID]++;
}

function playCard({ G, playerID }) {
  G.deck++;
  G.hand[playerID]--;
}

const game = {
  setup: ({ ctx }) => ({ deck: 6, hand: Array(ctx.numPlayers).fill(0) }),
  moves: { drawCard, playCard },
  turn: { minMoves: 1, maxMoves: 1 },
};
```

?> Beachte, wie wir die Spielzüge in eigenständige Funktionen ausgelagert haben, anstatt sie direkt im Spielobjekt zu definieren.

Wir ignorieren den Rendering-Teil dieses Spiels, aber so könnte es aussehen. Beachte, dass du jederzeit eine Karte ziehen oder spielen kannst, sogar wenn du eine Karte nimmst, wenn der Stapel leer ist.

```react
<iframe class='plain' src='snippets/phases-1' height='350' scrolling='no' title='example' frameborder='no' allowtransparency='true' allowfullscreen='true'></iframe>
```

### Phasen

Nehmen wir nun an, das Spiel soll in zwei Phasen ablaufen:

- eine erste Phase, in der die Spieler nur Karten ziehen (bis der Stapel leer ist).
- eine zweite Phase, in der die Spieler nur Karten spielen.

Um dies zu tun, definieren wir zwei `phases`. Jede Phase kann ihre eigene Liste von Spielzügen (Moves) angeben, die während dieser Phase in Kraft treten:

```js
const game = {
  setup: ({ ctx }) => ({ deck: 6, hand: Array(ctx.numPlayers).fill(0) }),
  turn: { minMoves: 1, maxMoves: 1 },

  phases: {
    draw: {
      moves: { DrawCard },
    },

    play: {
      moves: { PlayCard },
    },
  },
};
```

!> Eine Phase, die keine Spielzüge angibt, verwendet einfach die Spielzüge aus dem Hauptabschnitt `moves` des Spiels.

Das Spiel beginnt in keiner dieser Phasen. Um in der „draw“-Phase zu beginnen, fügen wir `start: true` zu ihrer Konfiguration hinzu. Nur eine Phase kann `start: true` haben.

```js
phases: {
  draw: {
    moves: { DrawCard },
    start: true,
  },

  play: {
    moves: { PlayCard },
  },
}
```

Lassen wir die „draw“-Phase auch automatisch enden, sobald der Stapel leer ist.

```js
phases: {
  draw: {
    moves: { DrawCard },
    endIf: ({ G }) => (G.deck <= 0),
    next: 'play',
    start: true,
  },

  play: {
    moves: { PlayCard },
  },
}
```

`endIf` beendet die Phase, in der es definiert ist, wenn es `true` zurückgibt. Das Spiel kehrt in einen Zustand zurück, in dem keine Phase aktiv ist. Für dieses Spiel möchten wir jedoch in die „play“-Phase wechseln, sobald die „draw“-Phase beendet ist. Dazu geben wir eine `next`-Option an, die dem Framework mitteilt, in diese Phase zu wechseln.

Beobachte unser Spiel in Aktion (jetzt mit Phasen). Beachte, dass du in der ersten Phase nur Karten ziehen und in der zweiten Phase nur Karten spielen kannst.

```react
<iframe class='plain' src='snippets/phases-2' height='350' scrolling='no' title='example' frameborder='no' allowtransparency='true' allowfullscreen='true'></iframe>
```

### Setup- und Cleanup-Hooks

Du kannst Code auch automatisch am Anfang oder Ende einer Phase ausführen lassen. Diese werden wie normale Spielzüge in `onBegin` und `onEnd` angegeben.

```js
phases: {
  phaseA: {
    onBegin: ({ G, ctx }) => { ... },
    onEnd: ({ G, ctx }) => { ... },
  },
};
```

?> Hooks like `onBegin` and `onEnd` are run only on the server in
multiplayer games. Moves, on the other hand, run on both client
and server. They are run on the client in order to facilitate
a lag-free experience, and are run on the server to calculate the
authoritative game state.

### Moving between Phases

#### Using events

The two primary ways of moving between phases are by calling the
following events:

1. `endPhase`: This ends the current phase and returns the game
   to a state where no phase is active. If the phase specifies a
   `next` option, then the game will move into that phase instead.

2. `setPhase`: This ends the current phase and moves the game into
   the phase specified by the argument.


#### Using an `endIf` condition

You can also end a phase by returning a truthy value from its
`endIf` method:

```js
phases: {
  phaseA: {
    next: 'phaseB',
    endIf: ({ G, ctx }) => true,
  },
  phaseB: { ... },
},
```

!> Whenever a phase ends, the current player's turn is first ended automatically.


### Setting the next phase dynamically

Instead of setting a phase’s `next` option with a string, you can
provide a function that will return the next phase based on game
state at the end of the phase:

```js
phases: {
  phaseA: {
    next: ({ G }) => {
      return G.condition ? 'phaseC' : 'phaseB';
    },
  },
  phaseB: { ... },
  phaseC: { ... },
},
```


### Override Behavior

As observed above, a phase can specify its own `moves` section
which comes into effect when the phase is active. This `moves`
section completely replaces the global `moves` section
for the duration of the phase. The moves may have the
same name as their global equivalents, but they are not related
to them in any way.

A phase can similarly also override the `turn` section. You will
typically do this if you want to use a different
[Turn Order](turn-order.md) during the phase.
