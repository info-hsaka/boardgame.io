# Speicherung (Storage)

**boardgame.io** ist unabhängig von der Art der Speicherung. Es stehen verschiedene Adapter zur Verfügung, mit denen du deinen Spielzustand in unterschiedlichen Speichersystemen sichern kannst.

Du kannst sogar deinen [eigenen Adapter](/storage?id=writing-a-custom-adapter) für ein benutzerdefiniertes Backend schreiben.

### Flatfile

Installiere zuerst die notwendigen Pakete:

```
npm install node-persist
```

Ändere dann deine Server-Spezifikation, um anzugeben, dass du dich mit einer Flatfile-Datenbank verbinden möchtest:

```js
const { Server, FlatFile } = require('boardgame.io/server');
const { TicTacToe } = require('./game');

const server = Server({
  games: [TicTacToe],

  db: new FlatFile({
    dir: '/storage/directory',
    logging: (true/false),
    ttl: (optional, siehe node-persist Dokumentation),
  }),
});

server.run(8000);
```

### Andere Backends

#### Firebase

Anleitungen unter https://github.com/delucis/bgio-firebase.

#### Azure Storage

Anleitungen unter https://github.com/c-w/bgio-azure-storage.

#### Postgres

Anleitungen unter https://github.com/janKir/bgio-postgres.

#### MongoDB

In Kürze verfügbar (wurde früher unterstützt, ist aber nicht synchron mit der neuesten Version).

### Caching

Abhängig von deinem Setup möchtest du vielleicht, dass der Server einige Daten zwischenspeichert, um die Last auf deine Datenbank zu verringern und die Serverantworten zu beschleunigen. [@boardgame.io/storage-cache](https://github.com/boardgameio/storage-cache) bietet ein einfaches Caching-Modell, das mit jedem boardgame.io-Datenbank-Connector kompatibel ist.

### Einen benutzerdefinierten Adapter schreiben

Erstelle eine Klasse, die die Schnittstelle [StorageAPI.Async](https://github.com/boardgameio/boardgame.io/blob/main/src/server/db/base.ts) implementiert.
