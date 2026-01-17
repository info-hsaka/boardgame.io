# Deployment (Bereitstellung)

## Serverlose Optionen

Für Ein-Spieler- oder Pass-and-Play-Spiele benötigst du möglicherweise keinen boardgame.io-Spieleserver und ziehst es vor, eine App bereitzustellen, die vollständig auf dem Client läuft. Wenn du keine Multiplayer-Funktionen benötigst, kann dies viel einfacher sein als die Bereitstellung eines Node.js-Servers.

Es gibt viele Dienste, die bei der Bereitstellung einer statischen App helfen können, darunter einige, die kostenlose Optionen anbieten, wie [Netlify](https://www.netlify.com/) und [Render](https://render.com/).

<!-- tabs:start -->

### **Plain JS**

Wenn du dem Plain-JS-Tutorial gefolgt bist, kannst du auch Parcel verwenden, um deine App für die Produktion zu bauen.

Füge ein Build-Skript zu deiner `package.json` hinzu:

```json
{
  "scripts": {
    "build": "parcel build index.html --out-dir build"
  }
}
```

Das Ausführen von `npm run build` erstellt nun einen optimierten Produktions-Build in `/build`, den du fast überall hosten kannst.

#### Deployment-Konfiguration

Sowohl Netlify als auch Render bieten Optionen für das kontinuierliche Deployment der neuesten Version deiner App aus einem Git-Repository. Diese Konfigurationen sollten dir helfen, mit diesen Diensten startklar zu werden.

<details>
<summary><strong>Netlify</strong></summary>

1. Erstelle ein neues Deployment (siehe [Netlify-Dokumentation](https://docs.netlify.com/site-deploys/create-deploys/)).

2. Verwende die folgenden Werte für das Deployment:

  | Option            | Wert            |
  |-------------------|-----------------|
  | Build Command     | `npm run build` |
  | Publish Directory | `build`         |

</details>

<details>
<summary><strong>Render</strong></summary>

1. Erstelle einen neuen Web Service auf Render und verbinde ihn mit deinem Projekt-Repository.

2. Verwende bei der Erstellung die folgenden Werte:

  | Option            | Wert            |
  |-------------------|-----------------|
  | Environment       | `Static Site`   |
  | Build Command     | `npm run build` |
  | Publish Directory | `build`         |

</details>

### **React**

Das Ausführen von `npm run build` in einem Create-React-App-Projekt erstellt einen optimierten Produktions-Build in `/build`, den du fast überall hosten kannst.

#### Deployment-Leitfäden

- **Netlify:** Siehe [den Leitfaden zur Bereitstellung auf Netlify](https://create-react-app.dev/docs/deployment/#netlify) in der Create-React-App-Dokumentation.

- **Render:** Siehe [„Deploy a Create React App Static Site“](https://render.com/docs/deploy-create-react-app) in der Render-Dokumentation.

<!-- tabs:end -->

## Heroku
[Heroku](https://heroku.com) verwendet zwei verschiedene Arten, um den Startbefehl einer Node-Anwendung zu bestimmen. Es ist möglich, entweder:

- Eine Procfile im Projekt-Stammverzeichnis mit der folgenden Zeile hinzuzufügen:
  `web: node -r esm server.js`

- Das Start-Skript in der package.json zu aktualisieren auf:
  `"start": "node -r esm server.js"`

Auf Heroku ist ein reguläres heroku/nodejs Buildpack erforderlich, um deine App zu bauen, welches normalerweise standardmäßig für Node-Anwendungen ausgewählt wird.

### Frontend und Backend
Um ein Spiel auf Heroku bereitzustellen, muss das Spiel auf einem einzigen Port laufen. Dazu muss der [Server](/api/Server.md) sowohl die API-Anfragen bearbeiten als auch die Seiten bereitstellen.
Unten ist ein Beispiel, wie man das erreicht.

Installiere zuerst diese zusätzlichen Abhängigkeiten:

```
npm i koa-static
```
Passe dann deine `server.js`-Datei wie folgt an:

```js
// server.js

import { Server } from 'boardgame.io/server';
import path from 'path';
import serve from 'koa-static';
import { TicTacToe } from './game';

const server = Server({ games: [TicTacToe] });
const PORT = process.env.PORT || 8000;

// Build-Pfad relativ zur Datei server.js
const frontEndAppBuildPath = path.resolve(__dirname, './build');
server.app.use(serve(frontEndAppBuildPath))

server.run(PORT, () => {
  server.app.use(
    async (ctx, next) => await serve(frontEndAppBuildPath)(
      Object.assign(ctx, { path: 'index.html' }),
      next
    )
  )
});
```

Die [Lobby](/api/Lobby.md) könnte wie folgt aussehen:

```jsx
import React from 'react';
import { Lobby } from 'boardgame.io/react';
import { TicTacToeBoard } from './board';
import { TicTacToe } from './game';

const { protocol, hostname, port } = window.location;
const server = `${protocol}//${hostname}:${port}`;
const importedGames = [{ game: TicTacToe, board: TicTacToeBoard }];

export default () => (
  <div>
    <h1>Lobby</h1>
    <Lobby gameServer={server} lobbyServer={server} gameComponents={importedGames} />
  </div>
);
```

Oder, ohne die Lobby, übergib die Server-Adresse beim Aufruf von `SocketIO`:

```js
import { SocketIO } from 'boardgame.io/multiplayer';

const { protocol, hostname, port } = window.location;
const server = `${protocol}//${hostname}:${port}`;

const GameClient = Client({
  // ...
  multiplayer: SocketIO({ server }),
});
```

### Nur Backend
Wenn du nur dein Backend auf Heroku veröffentlichen musst, kann deine `server.js` wie folgt vereinfacht werden:

```js
// server.js

import { Server } from 'boardgame.io/server';
import { TicTacToe } from './game';

const server = Server({ games: [TicTacToe] });
const PORT = process.env.PORT || 8000;

server.run(PORT);
```

Und deine [Lobby](/api/Lobby.md) würde nun auf deine Heroku-App-URL zeigen:
```jsx
import React from 'react';
import { Lobby } from 'boardgame.io/react';
import { TicTacToeBoard } from './board';
import { TicTacToe } from './game';

const server = `https://deineanwendung.herokuapp.com`;
const importedGames = [{ game: TicTacToe, board: TicTacToeBoard }];

export default () => (
  <div>
    <h1>Lobby</h1>
    <Lobby gameServer={server} lobbyServer={server} gameComponents={importedGames} />
  </div>
);
```

Oder, ohne die Lobby, übergib die Heroku-App-URL beim Aufruf von `SocketIO`:

```js
import { SocketIO } from 'boardgame.io/multiplayer';

const GameClient = Client({
  // ...
  multiplayer: SocketIO({ server: 'https://deineanwendung.herokuapp.com' }),
});
```
