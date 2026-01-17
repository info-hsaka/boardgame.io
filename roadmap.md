# Roadmap zu v1.0

Dies ist ein lebendiges Dokument, das die aktuellen Schwerpunkte festhält und zeigt, was noch erledigt werden muss, bevor wir bereit für ein v1-Release sind.

- _Bereiche, die Hilfe benötigen, sind mit **[Hilfe benötigt]** markiert._
- _Dinge, an denen [nicolodavis@](https://github.com/nicolodavis) arbeitet, sind mit **[N]** markiert._

Die unten aufgeführten Issues (und einige andere, die nicht in diesem Dokument enthalten sind) sind auch über den Link [v1.0 Milestone](https://github.com/boardgameio/boardgame.io/milestone/2) verfügbar.

### KI (AI)

- [x] MCTS-Bot ([Issue](https://github.com/boardgameio/boardgame.io/issues/7#issuecomment-389453032))
- [x] Möglichkeit zum Hinzufügen von Prioritäten und Zielen ([Issue](https://github.com/boardgameio/boardgame.io/issues/7#issuecomment-389453032))
- [ ] Bots in Multiplayer-Spielen ([Issue](https://github.com/boardgameio/boardgame.io/issues/383)) **[Hilfe benötigt]**

### Lobby

- [x] Grundlegende `create`- und `join`-API
- [x] Einfache webbasierte Lobby ([Issue](https://github.com/boardgameio/boardgame.io/issues/197))
- [ ] Lobby-Verbesserungen ([Issue](https://github.com/boardgameio/boardgame.io/issues/354)) **[Hilfe benötigt]**
- [ ] Migration zu Svelte ([Issue](https://github.com/boardgameio/boardgame.io/issues/432)) **[Hilfe benötigt]**

### Speicherung (Storage)

- ##### Datenbanken

  Wir ermutigen Entwickler, Speicher-Connectoren von Drittanbietern beizusteuern, indem sie [die `StorageAPI.Async`-Schnittstelle](https://github.com/boardgameio/boardgame.io/blob/main/src/server/db/base.ts) implementieren.

  Siehe die [Storage-Dokumentation](https://boardgame.io/documentation/#/storage) für Beispiele und Details zu den aktuell verfügbaren Backends.

- ##### Größe / Performance

  - [ ] MessagePack oder andere Kompressionsverfahren untersuchen

### Kern (Core)

- [x] Phasen -> Züge -> Etappen (Stages)
- [x] Verbesserungen der Zugreihenfolge ([Issue](https://github.com/boardgameio/boardgame.io/issues/154))
- [x] Protokoll-Verbesserungen ([Issue](https://github.com/boardgameio/boardgame.io/issues/227))
- [x] Immutability-Helper (Immer) hinzufügen ([Issue](https://github.com/boardgameio/boardgame.io/issues/295))

### Server

- [x] Server-Komponente abstrahieren ([Issue](https://github.com/boardgameio/boardgame.io/issues/251))
- [ ] Server-Skalierung ([Issue](https://github.com/boardgameio/boardgame.io/issues/277))

### Dokumentation

- [ ] Rezepte für verschiedene Spielszenarien
- [ ] Muster zur Code-Organisation
- [x] Deployment-Tutorial
- [x] Tutorial zur Verwendung des Vanilla-JS-Clients
