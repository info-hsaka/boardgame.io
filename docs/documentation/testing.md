# Teststrategien

### Unit-Tests

Spielzüge (Moves) sind nur Funktionen, daher eignen sie sich gut für Unit-Tests. Eine nützliche Strategie besteht darin, jeden Spielzug als eigenständige Funktion zu implementieren, bevor er an das Spielobjekt übergeben wird:

`Game.js`

```js
export function clickCell({ G, playerID }, id) {
  G.cells[id] = playerID;
}

export const TicTacToe = {
  moves: { clickCell },
  // ...
}
```

`Game.test.js`

```js
import { clickCell } from './Game';

it('sollte den richtigen Wert in die Zelle setzen', () => {
  // ursprünglicher Zustand.
  const G = {
    cells: [null, null, null, null, null, null, null, null, null],
  };

  // Spielzug ausführen.
  clickCell({ G, playerID: '1' }, 3);

  // neuen Zustand überprüfen.
  expect(G).toEqual({
    cells: [null, null, null, '1', null, null, null, null, null],
  });
});
```

### Szenario-Tests

Teste deine Spiellogik in spezifischen Szenarien.

```js
import { Client } from 'boardgame.io/client';
import { TicTacToe } from './Game';

it('sollte Spieler 1 als Gewinner deklarieren', () => {
  // ein spezifisches Brett-Szenario einrichten
  const TicTacToeCustomScenario = {
    ...TicTacToe,
    setup: () => ({
      cells: ['0', '0', null, '1', '1', null, null, null, null],
    }),
  };

  // den Client mit deinem benutzerdefinierten Szenario initialisieren
  const client = Client({
    game: TicTacToeCustomScenario,
  });

  // einige Spielzüge ausführen
  client.moves.clickCell(8);
  client.moves.clickCell(5);

  // den neuesten Spielzustand abrufen
  const { G, ctx } = client.getState();

  // das Brett sollte jetzt so aussehen
  expect(G.cells).toEqual(['0', '0', null, '1', '1', '1', null, null, '0']);
  // Spieler '1' sollte als Gewinner deklariert sein
  expect(ctx.gameover).toEqual({ winner: '1' });
});
```

?> Beachte, dass wir den Vanilla-JavaScript-Client importiert haben, nicht den von `boardgame.io/react`.

### Zufall testen

Wenn du einen Spielzug testest, der die [Random-API](/random) verwendet, kannst du definitionsgemäß nicht immer das gleiche Ergebnis erwarten, was das Testen erschwert. In diesem Fall kannst du eine der folgenden Strategien anwenden.

#### Fester PRNG-Seed

Du kannst `seed` in deinem Spielobjekt festlegen. Dies wird verwendet, um den internen Zustand der Random-API zu initialisieren, und du wirst eine vorhersehbare Sequenz von Ergebnissen bei Aufrufen von Random-API-Methoden sehen:

```js
import { Client } from 'boardgame.io/client';

const Game = {
  moves: {
    rollDice: ({ G, random }) => {
      G.roll = random.D6();
    },
  },
};

it('aktualisiert G.roll mit einer Zufallszahl', () => {
  const client = Client({
      // Seed setzen, damit PRNG immer im gleichen Zustand startet
    game: { ...Game, seed: 'fixed-seed' },
  });
  client.moves.rollDice();
  const { G } = client.getState();
  expect(G.roll).toMatchInlineSnapshot(`4`);
});
```

#### Random-API überschreiben <small>`seit v0.49.10`</small>

Wenn du spezifische Zufallsergebnisse testen musst, kannst du die Random-API vollständig überschreiben, um die volle Kontrolle über die Ergebnisse der API-Methoden zu haben.

```js
import { Client } from 'boardgame.io/client';
import { MockRandom } from 'boardgame.io/testing';

// Erstelle einen Mock des Random-Plugins, bei dem die D6-Methode immer 6 zurückgibt.
// Alle Methoden, für die du keine Implementierung angibst, verhalten sich wie gewohnt.
const randomPlugin = MockRandom({
  D6: () => 6,
});

it ('würfelt eine Sechs', () => {
  const client = Client({
    game: {
      ...Game,
      // Füge den Random-Plugin-Mock zu den Plugins des Spiels hinzu.
      plugins: [...(Game.plugins || []), randomPlugin]
    },
  });
  client.moves.rollDice();
  const { G } = client.getState();
  expect(G.roll).toMatchInlineSnapshot(`6`);
});
```

### Multiplayer-Tests

Verwende den lokalen Multiplayer-Modus, um Multiplayer-Interaktionen in Unit-Tests zu simulieren.

```js
it('Multiplayer-Test', () => {
  const spec = {
    game: MyGame,
    multiplayer: Local(),
  };

  const p0 = Client({ ...spec, playerID: '0' });
  const p1 = Client({ ...spec, playerID: '1' });

  p0.start();
  p1.start();

  p0.moves.moveA();
  p0.events.endTurn();

  // Der Zustand von Spieler 1 spiegelt die von Spieler 0 gemachten Züge wider.
  expect(p1.getState()).toEqual(...);

  p1.moves.moveA();
  p1.events.endTurn();

  ...
});
```

### Integrationstests

Teste die Anwendung durchgängig aus der Sicht der UI-Ebene.

In diesem Fall verwenden wir die [React Testing Library](https://testing-library.com/docs/react-testing-library/intro/), um unsere React-Komponente zu mounten und nach dem TicTacToe-Brett darin zu suchen. Wir prüfen dann, ob das Brett gerendert wird und wie erwartet auf Benutzerinteraktionen reagiert.

```js
import React from 'react';
import { render, fireEvent } from '@testing-library/react';
import '@testing-library/jest-dom/extend-expect';
import App from './app';

describe('Tic-Tac-Toe', () => {
  const { container } = render(<App />);
  const cells = container.querySelectorAll('td');
  
  test('Brett ist anfangs leer', () => {
    expect(cells).toHaveLength(9);
    for (const cell of cells) {
      expect(cell).toBeEmptyDOMElement();
    }
  });
  
  test('Klicken auf eine Zelle setzt die Markierung von Spieler 0', () => {
    fireEvent.click(cells[5]);
    expect(cells[5]).toHaveTextContent('0');
  });
});
```
