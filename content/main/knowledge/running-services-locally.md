---
title: Running Services Locally
type: docs
weight: 8
math: false
---

# Running Services Locally

> **Recommended Read** For a general overview of our infrastructure please see [Overall Infrastructure](https://gamescrafters.github.io/main/knowledge/overall-infrastructure/).

For more in-depth information on running these servers locally please see [Running Uni/UWAPI/Classic/Puzzles/One Servers Locally](https://docs.google.com/document/d/1CayoOXbncRLmo6_IUTdfiWkoxBHYNM0sKqdLaDoKPY8/edit?tab=t.0) made by members Robert Shi and Cameron Cheung.

## **GamesmanPy**

GamesmanPy is our most recent addition to our backend lineup of solvers. Started by member Michael Setchko Palmerlee, GamesmanPy allows the solving of small games and puzzles in python; easing complexity for new members in the onboarding process.

### Cloning the Repository
```bash
git clone https://github.com/GamesCrafters/GamesmanPy.git
```


### Setup the Project
Please follow the `README.md` instructions in the [GitHub Repository for GamesmanPy](https://github.com/GamesCrafters/GamesmanPy/tree/main).

### Running the Server
In the root project directory `GamesmanPy/` run
```bash
uv run server
```


### Sending Requests
To send requests to your local server, you can either input a url from a valid API route into the address bar of your browser, or, run `curl -L <url>` from the terminal.

Please see GamesmanPy’s valid routes in [`~/server/src/server/server.py`](https://github.com/GamesCrafters/GamesmanPy/blob/main/server/src/server/server.py).

#### Example Requests

`/<game_id>/<variant_id>/start/`
- [http://localhost:9004/clobber/3x4/start/](http://localhost:9004/clobber/3x4/start/)

Shows the starting position for the game `clobber` (game_id) variant `3x4` (variant_id).

- [http://localhost:9004/horses/regular/start/](http://localhost:9004/horses/regular/start/)

Shows the starting position for the game `horses` variant `regular`.

`/<game_id>/<variant_id>/positions/?p=<position>`
- [http://localhost:9004/clobber/3x4/positions/?p=oxoxoxoxoxoxo](http://localhost:9004/clobber/3x4/positions/?p=oxoxoxoxoxoxo)

Shows the position information and available moves for position `oxoxoxoxoxoxo` for the game `clobber` variant `3x4`.

## **GamesCraftersUWAPI**

### Cloning the Repository
```bash
git clone https://github.com/GamesCrafters/GamesCraftersUWAPI
cd GamesCraftersUWAPI
pip install -r requirements.txt
```

### Setup the Project
Please follow the `README.md` instructions in the [GitHub Repository for GamesCraftersUWAPI](https://github.com/GamesCrafters/GamesCraftersUWAPI).

### Running the Server
In the root project directory `GamesCraftersUWAPI/` run
```bash
python3 server.py
```

### Sending Requests
To send requests to your local server, you can either input a url from a valid API route into the address bar of your browser, or, run `curl -L <url>` from the terminal.

Please see GamesCrafersUWAPI valid routes in the [`README.md`](https://github.com/GamesCrafters/GamesCraftersUWAPI).

#### Example Requests

`/`
- [http://127.0.0.1:8082/](http://127.0.0.1:8082/)

Shows the list of available games and puzzles registered in UWAPI's [`GamesCraftersUWAPI/games/__init__.py`](https://github.com/GamesCrafters/GamesCraftersUWAPI/blob/master/games/__init__.py) file.

`/<game_id>/`
- [http://127.0.0.1:8082/achi/](http://127.0.0.1:8082/achi/)

Shows game information for the game `achi` (game_id).

`/<game_id>/<variant_id>/`
- [http://127.0.0.1:8082/othello/regular/](http://127.0.0.1:8082/othello/regular/)

Shows variant information for variant `regular` (game_id) of game `othello`.

`/<game_id>/<variant_id>/positions/?p=<human_readable_position_string>/`
- [http://127.0.0.1:8082/sevenpennies/regular/positions/?p=00000000_9](http://127.0.0.1:8082/sevenpennies/regular/positions/?p=00000000_9)

Shows position information for position `00000000_9` for the puzzle `sevenpennies` variant `regular`.

## **GamesmanUni**

### Cloning the Repository
```bash
git clone https://github.com/GamesCrafters/GamesmanUni.git
```

### Setup the Project
Please follow the `README.md` instructions in the [GitHub Repository for GamesmanUni](https://github.com/GamesCrafters/GamesCraftersUWAPI).

### Running the Hot Module Replacement (HMR) Server
In the root project directory `GamesmanUni/` run
```bash
yarn dev
```

### Access the Local Server
See your GamesmanUni server at [http://localhost:4173/uni](http://localhost:4173/uni).

### Connect GamesmanUni to your local UWAPI
To make GamesmanUni point to your local UWAPI instance, modify [`GamesmanUni/src/models/datas/defaultApp.ts`](https://github.com/GamesCrafters/GamesmanUni/blob/main/src/models/datas/defaultApp.ts) such that it matches the following code block:

```javascript
export const defaultDataSources: Types.DataSources = {
    gitHubRepositoryAPI: "https://api.github.com/repos/GamesCrafters/GamesmanUni",
    //gameAPI: "https://nyc.cs.berkeley.edu/universal/v1/"
    gameAPI: "http://localhost:8082/"
};
```