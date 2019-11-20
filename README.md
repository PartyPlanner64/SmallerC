This is the SmallerC fork that is built into PartyPlanner64.

To build:

- Install emscripten and any dependencies.
- `cd v0100`
- `emcc -O3 smlrc.c -o smlrc.html -s EXPORTED_FUNCTIONS='["_main"]' -s 'EXTRA_EXPORTED_RUNTIME_METHODS=["ccall", "cwrap"]'`
