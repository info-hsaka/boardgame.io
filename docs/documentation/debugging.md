# Fehlersuche (Debugging)

### Verwendung des Debug Panels in der Produktion

boardgame.io wird mit einem Debug Panel ausgeliefert, mit dem du mit deinem Spiel und den Spielclients interagieren kannst. Wenn du deine App für die Produktion erstellst (d. h. wenn `NODE_ENV === 'production'`), wird dieses aus dem finalen Bundle entfernt.

Wenn du das Debug Panel explizit in einen Produktions-Build aufnehmen möchtest, kannst du dies beim Erstellen deines Clients tun:

```js
import { Debug } from 'boardgame.io/debug';

const client = Client({
  // ...
  debug: { impl: Debug },
});
```

### Optionen für das Debug Panel

Du kannst die Option `collapseOnLoad` verwenden, um das Panel standardmäßig auszublenden, wenn der Client geladen wird. Die Option `hideToggleButton` entfernt die Umschaltfläche an der Seite des Panels, was bedeutet, dass du nur noch das Tastaturkürzel verwenden kannst, um die Sichtbarkeit umzuschalten.

```js
const client = Client({
  // ...
  debug: {
    // ...
    collapseOnLoad: true/false,
    hideToggleButton: true/false
  },
});
```

### Benutzerdefinierte Metadaten in Spielprotokollen

Manchmal kann es hilfreich sein, während eines Spielzugs Metadaten anzuzeigen. Dies kannst du mit dem Log-Plugin tun. Zum Beispiel:

```js
const move = ({ log }) => {
  log.setMetadata('Metadaten für diesen Spielzug');
};
```

Diese Metadaten werden in der Client-Eigenschaft `log` gespeichert und im Log-Bereich des Debug Panels angezeigt.

### Redux

Das Framework verwendet intern Redux. Manchmal möchtest du diesen Redux-Store vielleicht direkt debuggen. Dazu kannst du deinem Client einen Redux-Store-Enhancer übergeben. Zum Beispiel:

```js
import logger from 'redux-logger';
import { applyMiddleware } from 'redux';

Client({
  // ...
  enhancer: applyMiddleware(logger),
});
```

Dadurch werden Zustandsänderungen per `console.log` protokolliert. Dies kann auch mit der Browser-Erweiterung [Chrome Redux DevTools](http://extension.remotedev.io/) wie folgt verknüpft werden:

```js
Client({
  // ...
  enhancer: (
    window.__REDUX_DEVTOOLS_EXTENSION__
    && window.__REDUX_DEVTOOLS_EXTENSION__()
  ),
})
```

oder beides:

```js
import logger from 'redux-logger';
import { applyMiddleware, compose } from 'redux';

Client({
  // ...
  enhancer: compose(
    applyMiddleware(logger),
    (window.__REDUX_DEVTOOLS_EXTENSION__ && window.__REDUX_DEVTOOLS_EXTENSION__())
  ),
})
```

### Server + Sockets

Der Koa-Server kann debuggt werden, indem die Umgebungsvariable `DEBUG` vor dem Start gesetzt wird. Dies gibt dir Zugriff auf Protokolle der eingehenden Anfragen sowie auf die socket.io-Protokolle. Um die Umgebungsvariable zu setzen, stelle sie deinem npm-Skript zum Ausführen des Servers wie folgt voran:

```
DEBUG=* node server.js
```

> HINWEIS: Für verschiedene Debugging-Bereiche wirf einen Blick in die [socket.io-Dokumentation](https://socket.io/docs/v4/logging-and-debugging/#available-debugging-scopes)
