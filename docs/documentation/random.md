# Zufall (Randomness)

Viele Spiele erlauben Spielzüge, deren Ausgang von gemischten Karten oder gewürfelten Zahlen abhängt. Nehmen wir zum Beispiel das Spiel [Kniffel (Yahtzee)](https://de.wikipedia.org/wiki/Kniffel). Ein Spieler würfelt, wählt einige Würfel aus, würfelt erneut, wählt weitere aus und führt einen letzten Wurf durch. Je nach den oben liegenden Seiten muss der Spieler nun entscheiden, wo er seine Punkte einträgt.

Dies stellt interessante Herausforderungen an die Implementierung dar.

- **KI**. Zufall macht Spiele interessant, da man die Zukunft nicht vorhersagen kann, aber er muss kontrolliert werden, um Spiele zu ermöglichen, die exakt wiederholt werden können (z. B. für KI-Zwecke).

- **<abbr title="Pseudo-Random Number Generator">PRNG</abbr>-Zustand**. Das Spiel läuft sowohl auf dem Server als auch auf dem Client. Jeder Code und alle Daten auf dem Client können eingesehen und zum Vorteil eines Spielers genutzt werden. Wenn ein Client die nächsten zu generierenden Zufallszahlen vorhersagen könnte, wäre der zukünftige Ablauf eines Spiels nicht mehr unvorhersehbar. Die Bibliothek darf ein solches Szenario nicht zulassen. Der RNG und sein Zustand müssen auf dem Server bleiben.

- **Reine Funktionen (Pure Functions)**. Die Bibliothek ist mit Redux aufgebaut. Dies ist für Spiele wichtig, da jeder Spielzug ein [Reducer](https://redux.js.org/docs/basics/Reducers.html) ist und somit rein sein muss. Der Aufruf von `Math.random()` und anderen Funktionen, die einen externen Zustand pflegen, würde die Spiellogik unrein und nicht idempotent machen.

### Verwendung von Zufall in Spielen

Das Objekt, das an Spielzüge und andere Spiellogik übergeben wird, enthält ein Objekt `random`, das eine Reihe von Funktionen zur Erzeugung von Zufall bereitstellt.

Zum Beispiel ist die Funktion `random.D6` vergleichbar mit dem Werfen eines sechsseitigen Würfels:

```js
{
  moves: {
    rollDie: ({ G, random }) => {
      G.dieRoll = random.D6(); // dieRoll = 1–6
    },

    rollThreeDice: ({ G, random }) => {
      G.diceRoll = random.D6(3); // diceRoll = [1–6, 1–6, 1–6]
    }
  },
}
```

Details zu allen verfügbaren Zufallsfunktionen findest du unten.

### Seed

Du kannst den anfänglichen `seed` (Startwert) für den Zufallszahlengenerator in deinem Spielobjekt festlegen:

```js
const game = {
  seed: 42,
  // ...
};
```

?> `seed` kann entweder ein String oder eine Zahl sein.

## API-Referenz

### 1. Die (Würfel)

#### Argumente

1. `spotvalue` (_Zahl_): Die Dimension des Würfels (_Standard: 6_).
2. `diceCount` (_Zahl_): Die Anzahl der zu werfenden Würfel.

#### Rückgabewert

Der Wert des Würfelwurfs (oder ein Array von Werten, wenn `diceCount` größer als `1` ist).

#### Verwendung

```js
const game = {
  moves: {
    move({ random }) {
      const die = random.Die(6);      // die = 1-6
      const dice = random.Die(6, 3);  // dice = [1-6, 1-6, 1-6]
    },
  }
};
```

### 2. Number

Gibt eine Zufallszahl zwischen `0` und `1` zurück.

#### Verwendung

```js
const game = {
  moves: {
    move({ random }) {
      const n = random.Number();
    },
  }
};
```

### 3. Shuffle (Mischen)

#### Argumente

1. `deck` (_Array_): Ein zu mischendes Array.

#### Rückgabewert

Das gemischte Array.

#### Verwendung

```js
const game = {
  moves: {
    move({ G, random }) {
      G.deck = random.Shuffle(G.deck);
    },
  },
};
```

### 4. Wrapper

`D4`, `D6`, `D8`, `D10`, `D12` und `D20` sind Wrapper um `Die(n)`.

#### Argumente

1. `diceCount` (_Zahl_): Die Anzahl der zu werfenden Würfel.

#### Verwendung

```js
const game = {
  moves: {
    move({ random }) {
      const die = random.D6();
    },
  }
};
```
