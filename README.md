<p align="center"><strong>The project is not being actively developed at the moment, but is pretty stable and used in several games. If you would like to become a maintainer, please open an issue to discuss.</strong></p>

<p align="center">
  <a href="https://boardgame.io/">
    <img src="https://raw.githubusercontent.com/boardgameio/boardgame.io/main/docs/logo-optimized.svg?sanitize=true" alt="boardgame.io" />
  </a>
</p>

<p align="center">
<a href="https://www.npmjs.com/package/boardgame.io"><img src="https://badge.fury.io/js/boardgame.io.svg" alt="npm version" /></a>
<a href="https://github.com/boardgameio/boardgame.io/actions?query=workflow%3ATests"> <img src="https://github.com/boardgameio/boardgame.io/workflows/Tests/badge.svg" alt='Build Status'></a>
<a href='https://coveralls.io/github/boardgameio/boardgame.io?branch=main'><img src='https://coveralls.io/repos/github/boardgameio/boardgame.io/badge.svg?branch=main' alt='Coverage Status' /></a>
<a href="https://gitter.im/boardgame-io"><img src="https://badges.gitter.im/boardgame-io.svg" alt="Gitter" /></a>
</p>

<p align="center">
  <strong><a href="https://boardgame.io/documentation/#/">Dokumentation lesen</a></strong>
</p>

<p align="center">
  <strong>boardgame.io</strong> ist eine Engine zur Erstellung von rundenbasierten Spielen mit JavaScript.
</p>

Schreibe einfache Funktionen, die beschreiben, wie sich der Spielzustand ändert,
wenn ein bestimmter Spielzug ausgeführt wird. Dies wird automatisch
in ein spielbares Spiel umgewandelt, komplett mit Online-Multiplayer-Funktionen,
ohne dass du eine einzige Zeile Netzwerk- oder Speicher-Code schreiben musst.

### Funktionen

- **Zustandsverwaltung**: Der Spielzustand wird nahtlos und automatisch zwischen Clients, Server und Speicher verwaltet.
- **Multiplayer**: Der Spielzustand wird in Echtzeit und plattformübergreifend synchron gehalten.
- **KI**: Automatisch generierte Bots, die dein Spiel spielen können.
- **Spielphasen**: mit verschiedenen Spielregeln und Zugreihenfolgen pro Phase.
- **Lobby**: Spielersuche und Spielerstellung.
- **Prototyping**: Interface zur Simulation von Spielzügen, noch bevor das Spiel gerendert wird.
- **Erweiterbar**: Plugin-System, das neue Abstraktionen ermöglicht.
- **Unabhängig von der View-Ebene**: Verwende den Vanilla-JS-Client oder die Bindungen für React / React Native.
- **Protokolle**: Spielprotokolle mit der Möglichkeit der Zeitreise (Betrachtung des Spielbretts in einem früheren Zustand).

## Verwendung

### Installation

```sh
npm install boardgame.io
```

### Dokumentation

Lies unsere [vollständige Dokumentation](https://boardgame.io/documentation/), um zu lernen, wie man
boardgame.io benutzt, und tritt der [Community auf Gitter](https://gitter.im/boardgame-io/General) bei,
um deine Fragen zu stellen!

### Beispiele in diesem Repository ausführen

```sh
npm install
npm start
```

Die Beispiele befinden sich im Ordner [examples](examples/).

#### Benutzt du VS Code?

Dieses Repository ist bereit, in einem Dev-Container in VS Code ausgeführt zu werden. Siehe [die Richtlinien für Mitwirkende für Details](CONTRIBUTING.md).

## Changelog

Siehe [Changelog](docs/documentation/CHANGELOG.md).

## Mitmachen

Wir freuen uns über Beiträge aller Art!
Bitte nimm dir einen Moment Zeit, um unseren [Verhaltenskodex](CODE_OF_CONDUCT.md) zu lesen.

🐛 **Einen Bug gefunden?**  
Lass es uns wissen, indem du ein [Issue erstellst][new-issue].

❓ **Hast du eine Frage?**  
Unser [Gitter-Kanal][gitter] und die [GitHub Discussions][discussions]
sind gute Anlaufstellen.

⚙️ **Interessiert daran, einen [Bug][bugs] zu beheben oder eine [Funktion][features] hinzuzufügen?**  
Schau dir die [Richtlinien für Mitwirkende](CONTRIBUTING.md)
und die [Projekt-Roadmap](roadmap.md) an.

📖 **Können wir [unsere Dokumentation][docs] verbessern?**  
Pull-Requests, auch für kleine Änderungen, können hilfreich sein. Jede Seite in der
Dokumentation kann durch Klicken auf den Link „Edit on GitHub“ oben rechts bearbeitet werden.

[new-issue]: https://github.com/boardgameio/boardgame.io/issues/new/choose
[gitter]: https://gitter.im/boardgame-io/General
[discussions]: https://github.com/boardgameio/boardgame.io/discussions
[bugs]: https://github.com/boardgameio/boardgame.io/issues?q=is%3Aissue+is%3Aopen+label%3Abug
[features]: https://github.com/boardgameio/boardgame.io/issues?q=is%3Aissue+is%3Aopen+label%3Afeature
[docs]: https://boardgame.io/documentation/
[sponsors]: https://github.com/sponsors/boardgameio
[collective]: https://opencollective.com/boardgameio#support

## Lizenz

[MIT](LICENSE)
