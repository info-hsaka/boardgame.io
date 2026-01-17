# Chat

Der boardgame.io-Client bietet eine einfache API zum Senden von Chat-Nachrichten zwischen Spielern in einem Match unter Verwendung des [Multiplayer-Servers](multiplayer?id=remote-master).

Sowohl der [Plain-JS-Client](api/Client?id=properties) als auch der [React-Client](api/Client?id=board-props) (über Board-Props) bieten die folgenden Eigenschaften:

- `sendChatMessage(message)`: Funktion, die eine Chat-Nachricht an andere Spieler sendet. Das Argument `message` kann ein String sein, oder du kannst Objekte senden, um mehr Metadaten einzuschließen. Zum Beispiel könntest du entscheiden, einen Zeitstempel zusammen mit dem Nachrichtentext einzuschließen:

    ```js
    sendChatMessage({ message: 'Hallo', time: Date.now() });
    ```

- `chatMessages`: Ein Array mit Chat-Nachrichten, die dieser Client empfangen hat. Jede Nachricht ist ein Objekt mit den folgenden Eigenschaften:

    - `id`: ein eindeutiger Nachrichten-ID-String
    - `sender`: die `playerID` des Absenders der Nachricht
    - `payload`: der Wert des an `sendChatMessage` übergebenen `message`-Arguments

  Beispiel für ein `chatMessages`-Array:

  ```js
  [
      { id: 'foo', sender: '0', payload: 'Bereit zu spielen?' },
      { id: 'bar', sender: '1', payload: 'Los geht’s!' },
  ]
  ```

### Hinweise

- **Chat-Nachrichten sind flüchtig und werden nicht vom boardgame.io-Server gespeichert.** Ein Client empfängt nur Nachrichten, die gesendet werden, während er mit dem Server verbunden ist. Wenn Nachrichten unter den Spielern gesendet werden, bevor ein anderer Spieler sich verbunden hat, wird der neue Spieler diese vorherigen Nachrichten nicht empfangen. Ebenso gehen bei einer Aktualisierung der Seite alle zuvor empfangenen Nachrichten verloren.

- **Nur Spieler können Chat-Nachrichten senden.** Unter der Annahme, dass das Match über [den Lobby-Server](api/Lobby) authentifiziert ist, dürfen nur Spieler Nachrichten senden, die mit der gleichen Logik wie andere Spielaktionen authentifiziert werden. Zuschauer-Clients können Chat-Nachrichten empfangen und ansehen, aber keine eigenen Nachrichten senden.
