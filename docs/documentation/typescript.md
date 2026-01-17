# TypeScript

boardgame.io enthält Typdefinitionen für TypeScript.

### Grundlegende Verwendung

```typescript
// Game.ts
import type { Game, Move } from "boardgame.io";

export interface MyGameState {
  // auch bekannt als 'G', dein Spielzustand
}

const move: Move<MyGameState> = ({ G, ctx }) => {};

export const MyGame: Game<MyGameState> = {
  // ...
};
```

[Öffne dieses Snippet im TypeScript Playground ↗︎](https://www.typescriptlang.org/play?#code/PTAEHEEMFsFMDoAuBnAUAS2gBwPYCdFREBPLWUAbwhlgBpQBZHAN3IF9QAzPHaUAcgBGOSHgAmAcxrx0OfgG5UqWAA9cBUOgB2iWHk6QAxuQbEocAMqJIuyqlCgQoSAGtIA8P3rEcAVzygUnD8yKDI1rqobEqGOFrhoNAssABcjMkAPKbmsFY2sAB8oAC8oAAU4PSGiCoAlCVFFGyKymr4hLHxhNk0aTlZZjR5ukWlFPaOYPDTUUA)

### React

React-Komponenten müssen boardgame.io-spezifische Eigenschaften enthalten, erweitere deine Props also von `BoardProps`. Indem du deinen Spielzustandstyp an `BoardProps` übergibst, erhältst du die korrekte Typisierung für `G` in deiner Board-Komponente.

```typescript
// Board.tsx
import type { BoardProps } from 'boardgame.io/react';
import type { MyGameState } from './Game.ts'

interface MyGameProps extends BoardProps<MyGameState> {
  // Zusätzliche benutzerdefinierte Eigenschaften für deine Komponente
}

export function MyGameBoard(props: MyGameProps) {
  // Dein Spielbrett
}
```

Lies mehr über den [Client](api/Client.md) in der Referenz. Es sollte keine spezielle Typisierung erforderlich sein.

```typescript
// App.tsx
import { Client } from 'boardgame.io/react';

import { MyGame } from './Game';
import { MyGameBoard } from './Board';

const App = Client({
  game: MyGame,
  board: MyGameBoard,
});
export default App;
```

?> Willst du ein vollständigeres Beispiel sehen? Schau dir eine TypeScript–React-Implementierung des Tic-Tac-Toe-Tutorials auf CodeSandbox an:
<br/><br/>
[![Edit boardgame.io React-TypeScript demo](https://codesandbox.io/static/img/play-codesandbox.svg)](https://codesandbox.io/s/boardgame-io-react-typescript-demo-u5uvm?fontsize=14&hidenavigation=1&theme=dark)
