The [Morton order](https://en.wikipedia.org/wiki/Z-order_curve#) is a method of encoding a multi-dimensional array into a one-dimensional array. ^description

The Morton order[^morton-paper]

[^morton-paper]: [A computer Oriented Geodetic Data Base; and a New Technique in File Sequencing](https://dominoweb.draco.res.ibm.com/reports/Morton1966.pdf) Morton, G. M. (1966)
## An Example
Let $x, y, z = 00_x, 11_y, 10_z$, or in base 10: $0_x, 3_y, 2_z$
I've included the subscripts for each coordinate, so we can keep track of them. 
To encode these 3 coordinates, we can *interleave* them in binary. 

We obtain: 
$1_z\ 1_y\ 0_x\ 0_z\ 1_y\ 0_x$, 
so finally $110010$, which is the (todo)th index in base 10.

When considering only 1 bit in 2 dimensions, we can see what happen to each coordinate as we increase Morton index. 
So, to start off with, we have $00$, $(0_x, 0_y)$ with 1 bit for each coordinate. Adding 1 yields $01$, which when de-interleaved results in $(1_x, 0_y)$. Following on, the next index is $10$: $(0_x, 1_y)$, and finally $11$: $(1_x, 1_y)$. This is pictured below in the first iteration of Figure 1. This extends to 3 dimensions quite intuitively, as shown in Figure 2.

> [!quote] Figure 1
> ![[morton.png]]
> Four iterations of the 2D Z-order curve, source[^morton-wikipedia]

[^morton-wikipedia]: By David Eppstein, based on an image by Hesperian. - Own work, CC BY-SA 3.0, https://commons.wikimedia.org/w/index.php?curid=3879675

> [!quote] Figure 2
> ![[morton numbered.png]]
> Numbered 3D Morton curve, source[^morton-forceflow] 
> todo: is this accurate to godot lol

[^morton-forceflow]: Taken from [this article](https://www.forceflow.be/2013/10/07/morton-encodingdecoding-through-bit-interleaving-implementations/) by [Jeroen Baert](https://www.forceflow.be/). He also authored [[20 Sources#^ooc-svo-construction|reference 2]]. todo can I reference this? 