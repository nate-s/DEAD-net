# DEAD-net
Wave Function Collapse with deep equilibrium diffusion networks: Deep Equilibrium Adjacent Diffusion Network (DEAD-Net) 

deadnet.ipynb is the most current version. It's still a deep equilibrium method using a GRU network, but the GRU is 
pretty heavily modified. 
Highest performing network uses curriculum learning to consistently score > 5 but < 6 correct tiles out of 9.




-Background on WFC:

Wave Function Collapse (WFC) is a rule based problem solver. 
It's an incredibly intuitive algorithm that can be implemented by individual developers, but tends to be very slow since it generates content with sequential decisions. 
As a quick overview, when generating a something with WFC we start with an "uncollapsed wave space". 
The wave space represents the span of possibilities, and in the case of procedural map generation this is often the list of tiles we are using for the map.
When we say a wave space is uncollapsed we simply mean that we haven't made a decision yet about what is placed there.
In the context of a completely empty map this could be any of our tiles, so when we start the generation process we choose one to place and "collapse the wave function".
This likely has a cascading effect on the adjacent tiles because not every tile can be placed next to one another. 
For instance, a tree tile will not appear in the middle of a set of water tiles (probably). 

When we place our first tile we constrain the wave space of adjacent tiles via these placement rules.
This means there are less possible tiles we can place in those spaces, or conversely, we are more certain about what to place there because there are less totall options to choose from. 
This forms the backbone of the WFC algorithm, since at each step we look for the wave space with greatest certainty and collapse the function there.
We repeat this process until each wave space is collapsed hopefully resulting in a perfectly generated map!




-Motivations for this project:

There are some key points of WFC that we would really like to improve upon.

1) because it is a sequential decision maker it can potentially be very slow.

2) the problem becomes a lot more unpredictable with larger tile sets and more complex rules.

DEAD-Net aids WFC by solving much larger tile spaces with real time performance by collapsing the function in parallel with a neural network, all while not invalidating any pre-existing WFC development put in by game devs.
The architecture of the network uses a Gated Recurrent Unit as the backbone.
We treat the uncollapsed wave space as a 3D block of size [number of tiles, map height, map width] which is refined iteratively with the GRU by feeding the block back into the network after each forward pass.
This is analogous to a deep equilibrium model, and to a lesser extent neural ODEs, which exhibit natural stability and allow for networks emulate arbitrarily deep feed forward models while only using O(1) memory for training (since the number of paramters is constant through the "refinment" process).
By using a GRU on the feature block we can solve significantly more complex problems with miniscule network size. 


In the current version the network uses a heavily modified GRU network leveraging principles from the Squeeze-Excite formualtion and comes in at a size of 250 Kb with a tile space of 30 possibilities.
This result is repeatable, but the network size is larger since the class contains other network architecture experiemnts.
On average we found the current training routine and architecture places approximately ~5.5 correct tiles out of 9 when given an empty 3x3 space.
We found that suprisingly the network did not benefit from considering alrger spacial correlations than the immediate neighbors, and as such the performance measured in a 3x3 space translates to an arbitrary map size. 
Further, the network maintains a mean tile choice and variance roughly equivalent to the dataset suggesting a map variety also following those generated with WFC. 
By increasing the number of refinment steps the number of valid tile placements increase as well but at the cost of tile variance.
As such, we belive that if we can imrpove the network architecture/ training routine/ loss function to ahe point of 9/9 correct tile placements, the network will be able to generate near arbitrarily sized maps in times >>1 second.





