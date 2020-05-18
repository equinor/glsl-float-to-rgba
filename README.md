# glsl-float-to-rgba

## Installation

```bash
npm install --save glsl-float-to-rgba
```

## Signature

```glsl
vec4 floatToRgba(float texelFloat, bool littleEndian);
```

## Usage

This package exports a GLSL function, `floatToRgba`, that encodes a 32-bit floating point values to the RGBA channels of a WebGL texture. This is a workaround for the lack of support in baseline WebGL 1.0 for floating-point textures. WebGL 2.0 allows floating-point textures, and WebGL 1.0 allows them when the extension `OES_texture_float` is available, but `OES_texture_float` only guarantees reading from floating-point textures, not writing to. This package seeks to fill that gap.

`floatToRgba` is designed to be used as a [GLSLify](https://github.com/glslify/glslify) module. To encode a texel in your shader code, pass the `float` to the function `floatToRgba`. Note that because the function has to encode the float to its constituent bits, it needs to know whether the machine on which it's running uses [big-endian or little-endian representation](https://en.wikipedia.org/wiki/Endianness).

```glsl
// fragment shader

#pragma glslify: floatToRgba = require('glsl-float-to-rgba')

// does the machine use little-endian representation?
uniform bool littleEndian;

void main() {
  float floatValue = ...

  gl_FragColor = floatToRgba(floatValue, littleEndian);
}
```

```javascript
var pixels = new Uint8Array(CANVAS.width * CANVAS.height * 4);
gl.readPixels(0, 0, CANVAS.width, CANVAS.height, gl.RGBA, gl.UNSIGNED_BYTE, pixels);
pixels = new Float32Array(pixels.buffer);
```

Here's a simple way to compute endianness in JavaScript. Pass the result as a `uniform` variable to your shader.

```javascript
const littleEndian = (function machineIsLittleEndian() {
  const uint8Array = new Uint8Array([0xAA, 0xBB]);
  const uint16array = new Uint16Array(uint8Array.buffer);
  return uint16array[0] === 0xBBAA;
})();
```

## Attribution

Based on routine described [here](https://stackoverflow.com/questions/17981163/webgl-read-pixels-from-floating-point-render-target/20859830#20859830).
