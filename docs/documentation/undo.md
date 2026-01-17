# Rückgängig machen / Wiederholen (Undo / Redo)

boardgame.io bietet integrierte Unterstützung zum Rückgängigmachen (Undo) und Wiederholen (Redo) von Spielzügen im aktuellen Zug. Dies ist ein verbreitetes Muster in Spielen, die es einem Spieler erlauben, mehrere Spielzüge pro Zug auszuführen, und kann eine nützliche Funktion sein, um dem Spieler das Experimentieren mit verschiedenen Zugkombinationen (und das Sehen ihrer Auswirkungen) zu ermöglichen, bevor er sich auf eine festlegt. Du kannst diese Funktion deaktivieren, indem du `disableUndo` in der Spielkonfiguration auf true setzt.

### Verwendung

Du kannst die Funktionen `undo` und `redo` vom Client aus aufrufen.

<!-- tabs:start -->
#### **Plain JS**

Die Methoden sind an eine `Client`-Instanz gebunden:

```js
client.undo();
client.redo();
```

#### **React**

Die Methoden werden in den `props` deiner Board-Komponente übergeben:

```js
props.undo();
props.redo();
```
<!-- tabs:end -->

### Einschränken von rückgängig machbaren Spielzügen

Falls du nur möchtest, dass bestimmte Spielzüge rückgängig gemacht werden können (zum Beispiel um das Spicken bei Karten oder das erneute Würfeln zu verhindern), kannst du die ausführliche Spielzug-Syntax verwenden, die den Spielzug als Objekt anstatt als Funktion angibt. Das Feld `undoable` gibt an, ob der Spielzug rückgängig gemacht werden kann:

```js
const game = {
  moves: {
    rollDice: {
      move: ({ G, ctx }) => {},
      undoable: false,
    },

    playCard: ({ G, ctx }) => {},
  },
};
```

Im obigen Beispiel kann `playCard` rückgängig gemacht werden, `rollDice` jedoch nicht.
