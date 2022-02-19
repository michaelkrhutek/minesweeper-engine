# Minesweeper entity

Minesweeper entity is a library for creating a logical model of minesweeper game based on Rx.js library.

## Installation

Use the npmjs package manager [npmjs](https://npmjs.com) to install minesweeper-entity.

Npm:
```bash
npm install minesweeper-entity
```

Yarn:
```bash
yarn add minesweeper-entity
```

## Usage

This is an example of game 10x10 game with 10 bombs in react.

reactExample.tsx
```javascript
import {
  createWorldEntity,
  LocationData,
  LocationEntity,
  WorldEntityConfig,
  LocationMarkType,
} from "minesweeper-entity";
import React, { useEffect } from "react";
import { useObservableState } from "observable-hooks";
import "./reactExample.css";

const WORLD_CONFIG: WorldEntityConfig = {
  size: {
    x: { start: 0, end: 9 },
    y: { start: 0, end: 9 },
    z: { start: 0, end: 0 },
  },
  bombsRatio: 0.1,
};

const LOCATION_SIZE = 40;

export const MinesweeperGame = () => {
  const { current: world } = React.useRef(createWorldEntity(WORLD_CONFIG));
  const locations = world.getLocations();
  const data = useObservableState(world.data$);
  useEffect(() => {
    if (!data) return;
    const { didGameEnded, didAnyBombExploded } = data;
    if (!didGameEnded) return;
    didAnyBombExploded ? alert("You lost!") : alert("You won!");
  }, [data?.didGameEnded, data?.didAnyBombExploded]);
  return (
    <div className="world">
      {locations.map((location, index) => (
        <Location key={index} location={location} />
      ))}
    </div>
  );
};

const Location: React.FC<{ location: LocationEntity }> = ({ location }) => {
  const { position } = location;
  const data = useObservableState(location.data$);
  if (!data) return null;
  return (
    <div
      className="location-container"
      style={{
        width: LOCATION_SIZE,
        height: LOCATION_SIZE,
        top: LOCATION_SIZE * position.x,
        left: LOCATION_SIZE * position.y,
      }}
    >
      {data.isRevealed ? (
        <RevealedLocation data={data} />
      ) : (
        <NotRevealedLocation
          data={data}
          reveal={location.reveal}
          setMark={location.setMark}
        />
      )}
    </div>
  );
};

const NotRevealedLocation: React.FC<{
  data: LocationData;
  reveal: () => void;
  setMark: (mark: LocationMarkType) => void;
}> = ({ data, reveal, setMark }) => {
  const content = data.mark === LocationMarkType.Bomb ? "🚩" : "";
  return (
    <div
      className="location not-revealed"
      onClick={reveal}
      onContextMenu={(event) => {
        event.preventDefault();
        const mark = LocationMarkType.None
          ? LocationMarkType.Bomb
          : LocationMarkType.None;
        setMark(mark);
      }}
    >
      {content}
    </div>
  );
};

const RevealedLocation = ({ data }: { data: LocationData }) => {
  const content = data.hasBomb ? "💣" : data.surroundingBombs;
  return <div className="location revealed">{content}</div>;
};
```

reactExample.css
```css
.world {
    position: relative;
}

.location-container {
    box-sizing: border-box;
    position: absolute;
    padding: 2px;
}

.location {
    width: 100%;
    height: 100%;
    display: flex;
    justify-content: center;
    align-items: center;
}

.not-revealed {
    background-color: grey;
}

.revealed {
    background-color: lightgrey;
}
```

## License
[MIT](https://choosealicense.com/licenses/mit/)