# **NoSprite engine documentation**
---
description:


---
# Subcategories:
* General drawing and system introduction
* World saving and loading
* Rooms
* TextureGen 
* lighting ++
* baked 3D model objects
* polygonal items
* SimulationSystems
* "Humans"
* UI system

---

## General drawing and system introduction
---


## World saving and loading
---

## Rooms
---

## TextureGen
---
the texture generartion system is inspired by blenders shader nodes, and is made for making textures for tiles for TileImageGrid, or NormalImagePlane. the system is designed for making 2d textures with the intention of generating a heightmap thats used to generate a normalmap for lighting (see Rooms).

Note: currently randomness for Textures are not seeded, that may change, and will mean that all texutre gen functions require a seed 

The ```TextureGenerator``` functions mostly works with two different variable types, grid of floats: ```float[,]``` and ```Texture2D```. 

(note that further development might change the normal map output to be in (Monogame Color) ```Color[,]```), this section will be revised if so.

Heigtmaps are worked with in a ```float[width,height]``` space, where values can be anything, even negative, but keep in mind that both the "to normalmap function" ```b``` and different color-ramps and more expect a range of 0-1, and all texutre-giving functions, like height perlin noise ```GeneratePerlinNoiseHeightMap()```, whitenoise ```GenerateNoiseHeight()```, border ```Border()```, gridBorder ```GridBorder()``` and more output a 0-1 space (some versions of perlin might have values that go outside of the range, so think about using clamp () )

### Class: TextureGenerator
there are many functions in the system which are transform functions, made for heightmaps, that accept any float vlaues in the given array.
TextureGenerator is a public static class that holds the functions used in TextureGen, though the function is static, it requires a initializition call during Game1 load, to get GraphicsDevice, ```TextureGenerator.Initialize(GraphicsDevice graphicsDevice)```
this is done so GraphicsDevice isnt needed everywhere where TextureGenerator is used.

### Function: GenerateNormalsFromHeightmap
This function is a public function that is the heart of what connects the TextureGeneratino system with the Tile Image Lightingsystem. the function takes in a "heightmap" ```float[,]``` where the values are between 0,1. the code will still run if values are outside, but results may be unexpected, so consider clamping. the function returns a ```Texture2D``` texture with the same size, which is the normal map of the heightmap. 
```Texture2D GenerateNormalsFromHeightmap(float[,] heightmap)```
The GenerateNormalsFromHeightmap function supports seamless texture tiling, so if the given heightmap tiles, the normal map will too.
Note that normals are made in the newer style, DirectX, which tools such as Unreal and most modern game engines use, where y (G value) being 0 indicates that a sufrace is pointing up (top of screen), which is flipped to how it is many older tools, like Blender and OpenGL, where G = 255 means the surface is pointing up and G = 0 means the surface is pointing down.

### Function: GenerateNoiseTextureColor
This public function returns pure ColorNoise in the form of a Texture2D with the given size, and takes in ```int width```, ```int height``` and ```Random rng```, your random function.

### Function: GenerateNoiseHeight
This public function returns pure noise in float 0-1 in the form of a float Grid ```[width,height]```, and takes in ```int width```, ```int height``` and ```Random rng```, your random function.

### Function: GeneratePerlinNoiseTexture
This public function returns perlin noise in Color black```#000000``` - white ```#ffffff```in the form of a Texture2D ```Texture2D[width*height]```, and takes in ```int width```, ```int height```, ```int size``` and ```Random rng```. It gives out a heightmap-like Texture2D of random perlin noise, with size being the amount of detail, where ```size * size``` is the size of the array of vectors used to generate the perlin noise. the ```size``` should be >= 2 to not get errors, though a size of 2 gives little value. ```size``` should be < width or height, values higher than that are effectivley random noise.

- This function tiles.

### Function: GeneratePerlinNoise2LTexture
This public function returns perlin noise in Color black```#000000``` - white ```#ffffff```in the form of a Texture2D ```Texture2D[width*height]```, and takes in ```int width```, ```int height```, ```int size``` and ```Random rng```. It gives out a heightmap-like Texture2D of random perlin noise, made up of two Perlin layers stacked, layer one where```size * size``` is the size of the array of vectors used to generate the perlin noise, and the second layer is 2 times the vectors. the ```size``` should be >= 2 to not get errors. ```size``` should be < width or height, values higher than that are effectivley random noise.

- This function tiles.

### Function: GeneratePerlinNoiseHeightMap
This public function returns perlin noise in float 0-1 in the form of a float Grid ```[width,height]```, and takes in ```int width```, ```int height```, ```int size``` and ```Random rng```. It gives out a heightmap of random perlin noise, with size being the amount of detail, where ```size * size``` is the size of the array of vectors used to generate the perlin noise. the ```size``` should be >= 2 to not get errors, though a size of 2 gives little value. ```size``` should be < width or height, values higher than that are effectivley random noise. The noise values range from 0-1, but some settings (still being worked on) might make the output get to > 1 or < 0 in the extremes, so it is wise to use a clamp after this function if needed. 

- This function tiles.

### Function: GeneratePerlinNoise2LHeightMap
This public function returns perlin noise in float 0-1 in the form of a float Grid ```[width,height]```, and takes in ```int width```, ```int height```, ```int size``` and ```Random rng```. It gives out a heightmap of random perlin noise, made up of two Perlin layers stacked, layer one where```size * size``` is the size of the array of vectors used to generate the perlin noise, and the second layer is 2 times the vectors. the ```size``` should be >= 2 to not get errors. ```size``` should be < width or height, values higher than that are effectivley random noise. The noise values range from 0-1, but some settings (still being worked on) might make the output get to > 1 or < 0 in the extremes, so it is wise to use a clamp after this function if needed. 

- This function tiles.

### Function: addHeightmap
This public function adds two heightmaps to eachother. It takes in Two Heightmaps ```float[,]``` of equal width and height and returns one heigtmap ```float[,]```. This function accepts heightmaps with any float-values, and will often return Heightmaps with values < 0 or >1.

### Function: addNumberToHeightmap
This public function adds a ```float``` to a Heightmap```float[,]```. It takes in the heightMap: ```float[,]``` and number: ```float```. This function accepts heightmaps with any float-values, and will often return Heightmaps with values < 0 or >1.  This function returns HeightMaps of equal size to the one inputed, with the given number added to each value in the heightmap. The input number can be both negative and possitive.

- Note that there is no "subtractNumberToHeightmap", you instead add a negative number as you would normaly with ```addNumberToHeightmap```.

### Function: multiplyHeightmap
This public function multiplies two heightmaps together element-wise. It takes in two Heightmaps `float[,]` of equal width and height and returns one heightmap `float[,]`. This function accepts heightmaps with any float-values, and will often return heightmaps with values < 0 or > 1. Both input heightmaps are assumed to be the exact same size.

### Function: multiplyNumberToHeightmap
This public function multiplies a `float` with every value in a Heightmap `float[,]`. It takes in the heightMap: `float[,]` and number: `float`. This function returns heightmaps of equal size to the one inputted, with each value scaled by the given number. The input number can be both negative and positive.

- Note that there is no "divideNumberFromHeightmap", you instead multiply by the reciprocal of your desired divisor (e.g. `0.5` instead of `2`) as you would normally with `multiplyNumberToHeightmap`.

### Function: clampHeightmap
This public function clamps all values in a Heightmap `float[,]` to the normalized range [0, 1]. It takes in one Heightmap `float[,]` and returns one heightmap `float[,]` of equal size. Any value below `0` will be set to `0`, and any value above `1` will be set to `1`. This is useful for normalizing heightmaps that have been produced by operations such as `addHeightmap` or `multiplyHeightmap`, which may produce out-of-range values.

### Function GradientHeightmap
This public function returns a heightmap `float[,]` with a linear gradient running either horizontally or vertically, going from `startValue` to `endValue`. It takes in `int width`, `int height`, `float startValue`, `float endValue` and `bool horizontal`. If `horizontal` is true the gradient runs left to right, otherwise top to bottom. Accepts any float values for start and end, so the output range mirrors whatever is passed in.

### Function: Border
This public function returns a heightmap `float[,]` with a hard rectangular border, where border pixels are set to `0` and interior pixels are set to `1`. It takes in `int width`, `int height`, `int borderHorWidth` and `int borderUpWidth`, where `borderHorWidth` controls the thickness of the left and right borders, and `borderUpWidth` controls the thickness of the top and bottom borders. The border thickness is clamped to never exceed the dimensions of the heightmap.

### Function: SmoothBorder
This public function returns a heightmap `float[,]` with a rectangular border that transitions smoothly from `0` at the edges to `1` in the interior via a linear gradient. It takes in `int width`, `int height`, `int borderThickness` and `int gradientThickness`, where `borderThickness` is the total border size and `gradientThickness` is how many pixels of that border are used for the gradient ramp. The flat zero region has a thickness of `borderThickness - gradientThickness`. Both values are clamped to safe ranges to prevent unexpected behaviour on small heightmaps.


### Function: BrickTiledSmoothBorder
This public function returns a heightmap `float[,]` in a brick-lay pattern, where every other row of tiles is offset horizontally by half a tile width. It takes in `int width`, `int height`, `int tileAmountWidth`, `int tileAmountHeight`, `int borderThickness` and `int gradientThickness`. Tile dimensions, border clamping, and tiling behaviour are otherwise identical to `TiledSmoothBorder`.

- Note: for the half-offset to land on a clean pixel boundary, `tileAmountWidth` should be even. Odd values may produce a slight asymmetry at the seam.

- This function tiles.

    #### Function-version: BrickTiledSmoothBorderRandomBrickHeight
    This is a version of the ```BrickTiledSmoothBorder``` static function that also takes ```Random rng``` in as an argument. this function is used when you want bricks of varying colors. the function works just the same as BrickTiledSmoothBorder, but instead of bricks always being value 1, the brick parts of the texture is a number between 1 and 2 (exclusive upper). this is usefull so you can use a colorRamp spesifically ```ColorRamp3BrickVariant```. when using this function for heightmap generation, you can simply clamp the result using ```clampHeightmap```to get a normal 0-1 result.

### Function ChooseHigherColor
This public function composites two albedo textures by choosing the color from whichever of the two corresponding heightmaps is higher at each pixel. It takes in `Texture2D albedo1`, `float[,] height1`, `Texture2D albedo2` and `float[,] height2`. All four inputs must be the same size, otherwise an error is logged and the program exits. Returns a `Texture2D` of the same size, where each pixel is taken from `albedo1` unless `height2` is greater than `height1` at that position.

- This function is designed to be used alongside `HeightMapMax`, where `ChooseHigherColor` gives you the correct albedo and `HeightMapMax` gives you the correct heightmap for the composite result.

### Function HeightMapMax
This public function returns a heightmap `float[,]` where each pixel is the maximum value between two input heightmaps. It takes in `float[,] height1` and `float[,] height2`, which must be the same size, otherwise an error is logged and the program exits. Accepts any float values. Returns a heightmap `float[,]` of the same size.

- This function is also the heightmap companion to `ChooseHigherColor` — using both together lets you correctly composite two textured surfaces by height, producing both the right albedo and the right heightmap for normal map generation.

### Function HeightMapMin
This public function returns a heightmap `float[,]` where each pixel is the minimum value between two input heightmaps. It takes in `float[,] height1` and `float[,] height2`, which must be the same size, otherwise an error is logged and the program exits. Accepts any float values. Returns a heightmap `float[,]` of the same size.

### Function ColorRamp3BrickVariant
This public function converts a heightmap `float[,]` into a `Texture2D` using a two-part color ramp designed for use with `BrickTiledSmoothBorderRandomBrickHeight`. It takes in a heightmap `float[,]`, `float threshold`, `Color color1`, `Color color2` and `Color color3`. The ramp is split into two regions: in the 0-1 range a constant ramp divides `color1` and `color2` at `threshold`, and in the 1-2 range a linear gradient runs from `color2` to `color3`. Values outside 0-2 are clamped.

### Function: ColorRamp2
This public function converts a heightmap `float[,]` into a `Texture2D` by mapping its values between two colors. It takes in a heightmap `float[,]`, `Color color1` and `Color color2`, where `color1` corresponds to a heightmap value of `0` and `color2` corresponds to a value of `1`. Values are clamped to 0-1 before the color interpolation, so it is recommended to clamp the heightmap beforehand if it may contain out-of-range values. Returns a `Texture2D` of the same size as the input heightmap.

### Function ColorRamp2Constant
This public function converts a heightmap `float[,]` into a `Texture2D` by mapping values to one of two colors based on a hard threshold. It takes in a heightmap `float[,]`, `float threshold`, `Color colorUnder` and `Color colorOver`. Any heightmap value below the threshold gets `colorUnder`, and any value at or above gets `colorOver`. Returns a `Texture2D` of the same size as the input heightmap.

### Function HeightmapRamp2Constant
This public function remaps a heightmap `float[,]` to one of two float values based on a hard threshold. It takes in a heightmap `float[,]`, `float threshold`, `float valueUnder` and `float valueOver`. Any value below the threshold is set to `valueUnder`, and any value at or above is set to `valueOver`. Returns a heightmap `float[,]` of the same size as the input.

---

### TextureGen - Non-Tile-Oriented functions:

### Function: TiledSmoothBorder
This public function returns a heightmap `float[,]` filled with a grid of `tileAmountWidth` × `tileAmountHeight` evenly sized SmoothBorder tiles. It takes in `int width`, `int height`, `int tileAmountWidth`, `int tileAmountHeight`, `int borderThickness` and `int gradientThickness`. Each tile is sized automatically so that the full texture is divided evenly with no clipping — rectangular/non-square tiles are valid. The `borderThickness` and `gradientThickness` parameters behave the same as in `SmoothBorder`, and are clamped per-tile to prevent overflow. Returns a heightmap `float[,]` of the given width and height, with values in the 0-1 range.

- One thing worth noting: if `width` or `height` isn't perfectly divisible by the tile amounts, the rightmost column and bottom row of tiles will be slightly narrower/shorter due to integer division. If that matters for your use case, you could handle the remainder pixels by either clamping them to the last tile or stretching — but for most texture work it's unlikely to be visible.

### Function: SmoothBorderTileSize
This public function returns a heightmap `float[,]` filled with SmoothBorder tiles of a fixed size, where the number of tiles is determined by how many fit within the given texture dimensions. It takes in `int width`, `int height`, `int tileWidth`, `int tileHeight`, `int borderThickness` and `int gradientThickness`. Unlike `TiledSmoothBorder`, the tile dimensions are fixed and the tile count is a result — meaning tiles at the right and bottom edges will likely be clipped if the texture dimensions are not exact multiples of the tile size. The `borderThickness` and `gradientThickness` parameters behave the same as in `SmoothBorder`, and are clamped to the tile size to prevent overflow. Returns a heightmap `float[,]` of the given width and height, with values in the 0-1 range.
