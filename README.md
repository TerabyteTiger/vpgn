# vpgn

## Props

### pgnFile

This is where your `pgn` file goes. You'll need to strip out any header information (from my testing - chess.js wasn't accepting my Header info)

### startPosition

By default, the board will start in the new game position. If you'd like to start from a specific move, you can pass the value in here.

_Note that each player's move is accounted for here, so Black's 2nd move is move 4_

### whitePlayer

This is the string that will display in the header for the white player.

By default this will be set to "Unknown"

### blackPlayer

This is the string that will display in the header for the black player.

By default this will be set to "Unknown"

### event

This is the "sub-header" displayed under the players in the header.

By default this will be set to "" and not display.

### theme

By default the theme will be set to "standard" (Tan/brown coloring)

Other available themes include:

- purple
- pink
- green
- red
- grey
- gray
- blue
