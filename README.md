This is the SmallerC fork that is built into PartyPlanner64.

To build:

- Install emscripten and any dependencies.
- `cd v0100`
- `emcc -O3 smlrc.c -o smlrc.js -s MODULARIZE=1 -s 'EXPORT_NAME="SmallerC"' -s EXPORTED_FUNCTIONS='["_main"]' -s 'EXTRA_EXPORTED_RUNTIME_METHODS=["ccall", "cwrap", "setValue", "FS"]'`
- Copy `smlrc.js` and `smlrc.wasm` to PartyPlanner64 source.
- In `smlrc.js`, add `/* eslint-disable */`

To use:

```
const str2ptr = (s) => {
  const ptr = Module._malloc((s.length+1)*Uint8Array.BYTES_PER_ELEMENT);
  for (let i = 0; i < s.length; i++) {
    Module.setValue(ptr+i, s.charCodeAt(i), 'i8');
  }
  Module.setValue(ptr+s.length, 0, 'i8');
  return ptr;
};

var str2ptrs = (strList) => {
  const listPtr = Module._malloc(strList.length*Uint32Array.BYTES_PER_ELEMENT);

  strList.forEach((s, idx) => {
    const strPtr = str2ptr(s);
    Module.setValue(listPtr + (4*idx), strPtr, 'i32');
  });

  return listPtr;
};

FS.createDataFile("/", "input.c", "int main() { return 0; }", true, true, true);

var smallerc = Module.cwrap("main", "number", ["number", "number"]);

var args = ["./smallerc", "/input.c", "/output.s"];
smallerc(args.length, str2ptrs(args));

new TextDecoder("utf-8").decode(FS.readFile("/output.s"))
```