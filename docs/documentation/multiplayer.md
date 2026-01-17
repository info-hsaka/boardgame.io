# Multiplayer

In diesem Abschnitt erklären wir, wie das Framework deine
Spiellogik in eine Multiplayer-Implementierung umwandelt, ohne dass
du Netzwerk- oder Speicherschicht-Code schreiben musst. Wir arbeiten
weiter mit unserem Tic-Tac-Toe-Beispiel aus dem [Tutorial](tutorial.md).

### Clients und Master

Ein boardgame.io-Client ist das, was du mit dem `Client`-Aufruf erstellst.
Du initialisierst ihn mit deinem Spielobjekt (das die Spielzüge enthält),
sodass er über alle Informationen verfügt, die zur Ausführung des Spiels benötigt werden.
In einem Einzelspieler-Setup endet die Geschichte hier.

In einem Multiplayer-Setup fungieren Clients nicht mehr als maßgebliche
Speicher des Spielzustands. Stattdessen delegieren sie die Ausführung des
Spiels an einen Game Master (Spielleiter). In diesem Modus senden Clients Spielzüge / Ereignisse,
aber die Spiellogik läuft auf dem Master, der den nächsten Spielzustand berechnet,
bevor er ihn an andere Clients sendet.

Da die Clients jedoch die Spielregeln kennen, führen sie das Spiel auch
parallel aus (dies wird als optimistisches Update bezeichnet und ist
eine Optimierung, die ein verzögerungsfreies Erlebnis bietet).
Falls ein bestimmter Client den neuen Spielzustand falsch berechnet,
wird er schließlich vom Master überschrieben, sodass das gesamte Setup weiterhin
eine einzige maßgebliche Quelle hat. Wenn ein Spielzug auf einen Zustand zugreift, der für den
Client nicht zugänglich ist (zum Beispiel geheimer Zustand), müssen optimistische
Updates für diesen Spielzug möglicherweise deaktiviert werden. Siehe die
[Dokumentation zum geheimen Zustand](secret-state.md) für weitere Details.

## Lokaler Master

Der Game Master kann vollständig im Browser laufen. Dies ist nützlich, um
Pass-and-Play-Multiplayer einzurichten oder um das Multiplayer-Erlebnis als Prototyp
zu testen, ohne einen Server aufsetzen zu müssen.

Importiere dazu `import { Local } from 'boardgame.io/multiplayer'`
und füge `multiplayer: Local()` zu den Client-Optionen hinzu.
Nun kannst du so viele dieser Clients in deiner App instanziieren, wie du möchtest, und
du wirst bemerken, dass sie alle synchron gehalten werden und denselben Zustand teilen.

<!-- tabs:start -->

#### **Plain JS**

Aktualisieren wir unseren `TicTacToeClient`, um eine zusätzliche `playerID`-Option
in seinem Konstruktor zu erhalten. Wir verwenden diese, damit jeder Client weiß,
für welchen Spieler er spielt.

Dann aktualisieren wir die Erstellung des boardgame.io-Clients, übergeben die
`playerID` und setzen `multiplayer` auf den Local Master.

```js
import { Client } from 'boardgame.io/client';
import { Local } from 'boardgame.io/multiplayer';
import { TicTacToe } from './Game';

class TicTacToeClient {
  constructor(rootElement, { playerID } = {}) {
    this.client = Client({
      game: TicTacToe,
      multiplayer: Local(),
      playerID,
    });
    // ...
  }
  // ...
}
```

Anstatt nun einen Client in unserer App zu rendern, rendern wir
einen für jede Spieler-ID:

```js
const appElement = document.getElementById('app');
const playerIDs = ['0', '1'];
const clients = playerIDs.map(playerID => {
  const rootElement = document.createElement('div');
  appElement.append(rootElement);
  return new TicTacToeClient(rootElement, { playerID });
});
```

[![Edit bgio-plain-js-multiplayer](https://codesandbox.io/static/img/play-codesandbox.svg)](https://codesandbox.io/s/bgio-plain-js-multiplayer-re48t?fontsize=14&hidenavigation=1&module=%2Fsrc%2FApp.js&theme=dark)

#### **React**

```js
// src/App.js

import React from 'react';
import { Client } from 'boardgame.io/react';
import { Local } from 'boardgame.io/multiplayer';
import { TicTacToe } from './Game';
import { TicTacToeBoard } from './Board';

const TicTacToeClient = Client({
  game: TicTacToe,
  board: TicTacToeBoard,
  multiplayer: Local(),
});

const App = () => (
  <div>
    <TicTacToeClient playerID="0" />
    <TicTacToeClient playerID="1" />
  </div>
);

export default App;
```

[![Edit boardgame.io](https://codesandbox.io/static/img/play-codesandbox.svg)](https://codesandbox.io/s/boardgameio-dibw3)
<!-- tabs:end -->

?> Du fragst dich vielleicht, was der Parameter `playerID` im
obigen Beispiel ist. Clients müssen einem bestimmten Spielerplatz
zugeordnet sein, um in einem Multiplayer-Setup Spielzüge ausführen zu können. (Wenn ein Client keine
`playerID` hat, ist er ein Zuschauer, der den Live-Spielzustand sehen, aber keine
Spielzüge ausführen kann.)

```react
<iframe class='plain' src='snippets/multiplayer' height='250' scrolling='no' title='example' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'></iframe>
```

Im obigen Beispiel kannst du abwechselnd als Spieler 0 und Spieler 1
auf den beiden Brettern spielen. Das Klicken auf ein bestimmtes Brett, wenn dieser
Spieler nicht an der Reihe ist, hat keine Auswirkungen.

### Zustand im Browser speichern

Wenn du möchtest, dass der Spielzustand im Browser mit `localStorage` gespeichert wird,
kannst du beim Erstellen eines lokalen Masters zusätzliche Optionen übergeben:

```js
Local({
  // localStorage-Cache aktivieren.
  persist: true,

  // Benutzerdefiniertes Präfix zum Speichern der Daten festlegen. Standard: 'bgio'.
  storageKey: 'bgio',
});
```

## Entfernter Master

// TODO unseren Server-Setup integrieren

Du kannst den Game Master auch auf einem separaten Server laufen lassen. Jeder boardgame.io-Client
kann sich mit diesem Master verbinden (sei es ein Browser, eine Android-App
usw.) und wird in Echtzeit mit anderen Clients synchron gehalten.

Um einen Client mit einem entfernten Master zu verbinden, verwenden wir erneut die
`multiplayer`-Option, aber diesmal importieren wir `SocketIO` anstelle von `Local`
und geben den Standort des Servers an.

<!-- tabs:start -->

#### **Plain JS**

```js
import { SocketIO } from 'boardgame.io/multiplayer'

class TicTacToeClient {
  constructor(rootElement, { playerID } = {}) {
    this.client = Client({
      game: TicTacToe,
      multiplayer: SocketIO({ server: 'localhost:8000' }),
      playerID,
    });
    // ...
  }
  // ...
}
```

Wir müssen auch eine kleine Anpassung an unserer `update`-Methode vornehmen.
Bei Verwendung eines entfernten Masters kennt der Client den Spielzustand
beim ersten Ausführen nicht, daher wird `update` zuerst mit `null` aufgerufen,
und dann mit dem vollständigen Spielzustand, nachdem die Verbindung zum Server hergestellt wurde.

In einer echten Implementierung würdest du vielleicht einen Lade-Spinner anzeigen, um
dies anzuzeigen, aber wir überspringen unser `update` vorerst einfach, wenn der Zustand `null` ist:

```js
update(state) {
  if (state === null) return;
  // ...
}
```

#### **React**

```js
import { SocketIO } from 'boardgame.io/multiplayer'

const TicTacToeClient = Client({
  game: TicTacToe,
  board: TicTacToeBoard,
  multiplayer: SocketIO({ server: 'localhost:8000' }),
});
```

<!-- tabs:end -->

Hinter den Kulissen sendet der Client nun bei jedem Spielzug Aktualisierungen an den entfernten Master
über einen WebSocket. Natürlich müssen wir jetzt einen Server am angegebenen
Standort betreiben, was unten besprochen wird.

### Den Server einrichten

Wir erstellen eine neue Datei unter `src/server.js`, um unseren Server-Code zu schreiben.

boardgame.io bietet ein Server-Modul, das den Betrieb des Game Masters
auf einem Node-Server vereinfacht. Wir importieren dieses Modul und konfigurieren es mit unserem
`TicTacToe`-Spielobjekt und einer Liste von URL-Origins, denen wir die
Verbindung zum Server erlauben wollen. Später würdest du `origins` mit dem Domainnamen
deines Spiels festlegen, aber vorerst importieren wir einen Standardwert, der jeder lokal bereitgestellten
Seite die Verbindung erlaubt.

```js
// src/server.js
const { Server, Origins } = require('boardgame.io/server');
const { TicTacToe } = require('./Game');

const server = Server({
  games: [TicTacToe],
  origins: [Origins.LOCALHOST],
});

server.run(8000);
```

?> Siehe [die Server-Referenzseite](api/Server.md) für weitere Details zu
   den verschiedenen Konfigurationsoptionen.

Da `Game.js` ein ES-Modul ist, verwenden wir [esm](https://github.com/standard-things/esm),
was es uns ermöglicht, `import`-Anweisungen in einer Node-Umgebung zu verwenden:

```
npm install esm
```

Wir können dann ein neues Skript zu unserer `package.json` hinzufügen, um das
Ausführen des Servers zu vereinfachen:

```json
{
  "scripts": {
    "serve": "node -r esm src/server.js"
  }
}
```

Wir können nun `npm run serve` in einem Terminal ausführen, um den Server zu starten, und
`npm start` in einem anderen, um unsere Web-App bereitzustellen.
Du kannst mehrere Clients mit demselben Spiel verbinden, indem du
deine App in mehreren verschiedenen Browser-Tabs öffnest.
Du wirst bemerken, dass während des Spielens alles synchron gehalten wird
(der Zustand geht nicht verloren, selbst wenn du die Seite aktualisierst).

In diesem Beispiel befinden sich beide Spieler noch auf demselben Bildschirm. Ein natürlicheres
Setup wäre, wenn jeder Client nur einen einzigen (aber unterschiedlichen)
Spieler hätte.

<!-- tabs:start -->
#### **Plain JS**

Du möchtest, dass ein Client gerendert wird:
```js
new TicTacToeClient(appElement, { playerID: '0' });
```

und ein anderer:
```js
new TicTacToeClient(appElement, { playerID: '1' });
```

#### **React**

Du möchtest, dass ein Client gerendert wird:
```
<TicTacToeClient playerID="0" />
```

und ein anderer:
```
<TicTacToeClient playerID="1" />
```
<!-- tabs:end -->

Eine Möglichkeit, dies zu tun, besteht darin, den Spieler beim Öffnen deiner App zu fragen, welchen Platz er
einnehmen möchte, und dann die `playerID` entsprechend zu setzen.
Du kannst auch einen URL-Pfad verwenden, um den Spieler zu bestimmen, oder eine Matchmaking-Lobby nutzen.

Der vollständige Code aus diesem Abschnitt ist auf CodeSandbox sowohl für die
[React](https://codesandbox.io/s/boardgameio-fsl8y)- als auch für die
[Plain JS](https://codesandbox.io/s/bgio-plain-js-multiplayer-server-742oh)-Version
verfügbar.
Um den Server auszuführen, kannst du auf **File** > **Export to ZIP** klicken, um
das Projekt herunterzuladen, und dann den Server und den Client wie oben beschrieben ausführen.
Vergiss nicht, zuerst `npm install` im Projektverzeichnis auszuführen!

?> **TIPP** Du kannst die `playerID` während des Prototypings auch auf einen beliebigen Spieler setzen,
indem du im Debug-UI auf das Feld des jeweiligen Spielers klickst.

### Mehrere Spieltypen

Du kannst mehrere Arten von Spielen über denselben Server bereitstellen:

```js
const app = Server({ games: [TicTacToe, Chess] });
```

Damit dies korrekt funktioniert, stelle sicher, dass jede Spielimplementierung
einen Namen angibt:

```js
const TicTacToe = {
  name: 'tic-tac-toe',
  // ...
};
```

### Spielinstanzen

Standardmäßig verbinden sich alle Client-Instanzen mit einem Spiel mit
der ID `'default'`. Um eine neue Spielinstanz zu spielen, kannst du `matchID`
an deinen Client übergeben. Alle Clients, die diese ID verwenden,
sehen nun denselben Spielzustand.

<!-- tabs:start -->

#### **Plain JS**

Übergib `matchID` beim Erstellen deines boardgame.io-Clients:
```js
const client = Client({
  game: TicTacToe,
  matchID: 'matchID',
  // ...
});
```

Du kannst eine `matchID` auch bei einem bereits instanziierten Client aktualisieren:
```js
client.updateMatchID('neueID');
```

#### **React**

```
<TicTacToeClient matchID="match-id"/>
```
<!-- tabs:end -->

Die `matchID` kann, ähnlich wie die `playerID`, wiederum entweder
durch einen URL-Pfad oder eine Lobby-Implementierung bestimmt werden.

### Speicherung

Die Standard-Speicherimplementierung ist eine In-Memory-Map.
Wenn du etwas Dauerhafteres möchtest, kannst du einen
der verfügbaren Datenbank-Connectoren verwenden oder sogar deinen eigenen implementieren.

Siehe die [Speicher-Dokumentation](storage.md) für weitere Details.
