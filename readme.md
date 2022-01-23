Since the current [Sauerbraten](http://sauerbraten.org/) version does not have commands to select a specific face or an edge of a cube, this script uses some workarounds to achieve a similar result using the existing commands, not requiring a modified client (thus being slower than it would be if implemented natively).

### Installation
Just download and place the [`cubedeformer.cfg`](#file-cubedeformer-cfg-L0) file in the Sauerbraten root folder and run `/exec cubedeformer.cfg` ingame, the [`cubedeformer_examples.cfg`](#file-cubedeformer_examples-cfg-L0) file is optional.

### Face & Edge commands

![](https://raw.githubusercontent.com/SalatielSauer/misc/master/cubedeformer_indexes.png)

- `gotoface` `f` `[c]` `s`
Teleports to face `f` (0..3) and executes `[c]`. `s` is an optional delay that gives time for the gotoface to finish before running another gotoface (otherwise some sync problems may occur), default: 500ms when there is an nested gotoface or 1ms when there is none.

![](https://raw.githubusercontent.com/SalatielSauer/misc/master/cubedeformer_demo_gotoface.gif)

- `gotoedge` `e` `[c]` `b`
Teleports to the edge `e` (0..3) of the face being looked at and executes `[c]`. `b` (0/1) is optional and determines whether the gridpower should be changed to an equivalent of the selected edge.

![](https://raw.githubusercontent.com/SalatielSauer/misc/master/cubedeformer_demo_gotoedge.gif)

- `moveseldo` `x` `y` `z` `c`
Adds `x`, `y` and `z` to the position of the current selection, executing `[c]` every move.

- `getlookatface`
Returns the face the camera is looking at relative to the world (not the selected face).

### Native commands

The commands below are already available in the Sauerbraten 2020 edition natively.

- `editface` `d` `t`
`t` determines whether the face (0), selection (1) or edge (2) of a cube will be pushed to `d`.

- `flip`
Inverts selected geometry.

- `movesel` `a` `d`
`d` determines whether the selection will be moved to x (0), y (1) or z (2) `a` times.

- `rotate` `d`
Rotates once left (-1) or right (1) relative to the selected face.

- `setselpos` `x` `y` `z`
Sets the position of the current selection to `x` `y` and `z` or creates a new one.

### Limitations and their workarounds
There are two limitations you should be aware of:

- In order for the native `editface` command to work properly (you will use it to push an edge or a face), the camera needs to be slightly aligned to the horizon (the camera's pitch and yaw), you don't have to worry about the camera's yaw (horizontal) value, `gotoface` will fix it automatically, however the only way to correct the pitch automatically is to respawn the player.
You can use the `gotoface_fixpitch` `f` `[c]` shortcut or simply move the camera closer to the horizon manually.

- For the same reason mentioned above, you cannot access the top or bottom face of the selection (+Z and -Z) directly with `gotoface`, to make changes to them you will have to rotate the geometry.
Example of how to apply colors to the top and bottom face of a geometry:
```
gotoface 2 [ // go to a side face (2, -Y).
  rotate 1 // rotates the cube so that the top face becomes a side face (0, +X).
  gotoface 0 [ // go to the "top" face which is now temporarily on the side.
    allfaces 0 // makes sure that only the selected face has its color changed.
    vcolor 1 0 0 // sets the red color.
    flip // flips the geometry to access the "bottom" face.
    vcolor 0 0 1 // sets the blue color.
    gotoface 2 [ // go back to the side face (2, -Y).
      rotate 1 // returns the bottom and top faces to their initial rotation.
    ]
  ]
]
```
![](https://raw.githubusercontent.com/SalatielSauer/misc/master/cubedeformer_demo_zfaces.gif)

Regarding other limitations or problems you may run into, usually a half-second `sleep` fixes them :P

### Shortcuts
Although handy, the commands below might not be a good idea if you want to have absolute control over `gotoface` and `gotoedge`.

- `gotoface_fixpitch` `f` `[c]`
Respawns the player to fix the camera orientation and calls `gotoface` with `f` and `[c]` as its parameters.

- `pushedge` `e` `d` `[c]`
Uses `gotoedge` and `editface` to go to an edge and push it `d` times, `[c]` is executed at the end.

- `editfaceedge` `f` `e` `d` `[c]`
Uses `gotoface` (`f`), `pushedge` (`e`) and `editface` (`d`), with the difference that it handles +Z (4) and -Z (5) faces automatically (rotating them back and forth).

Keep in mind that you want to avoid anything involving `gotoface` and `gotoedge` as much as possible, most of the time you only need to change faces twice and edge only once, everything else can be handled with `flip` and `rotate`.

### Examples
- #### [Ramp](#file-cubedeformer_examples-cfg-L180)
![](https://raw.githubusercontent.com/SalatielSauer/misc/master/cubedeformer_demo_ramp.gif)

- #### [Twisted Column](#file-cubedeformer_examples-cfg-L114)
![](https://raw.githubusercontent.com/SalatielSauer/misc/master/cubedeformer_demo_twistedcolumn.gif)

- #### [Emerald](#file-cubedeformer_examples-cfg-L347)
![](https://raw.githubusercontent.com/SalatielSauer/misc/master/cubedeformer_demo_emerald.gif)

See more examples in the [`cubedeformer_examples.cfg`](#file-cubedeformer_examples-cfg-L0) file.