# emmu-gb

Core de **Game Boy (DMG)** do Emmu, usado no navegador via WebAssembly. No
desktop o emulador continua sendo o Mesen (`emmu-mesen-sys`).

O núcleo de emulação é o [oxGBC](https://github.com/mxmgorin/oxgbc), vendorizado
em `vendor/oxgbc-core` (GPL-3.0, ver `LICENSE` e `UPSTREAM.txt`). Todas as
dependências de emulação ficam dentro desta pasta. O core é instanciado em modo
`GbModel::Dmg`, com a paleta verde clássica do Game Boy.

## API

`GbCore` (Rust nativo) e `GbEmulator` (WASM) expõem:

- `new(rom)` / `gameTitle()`
- `runFrame(renderGraphics)` → frame RGBA8888 160×144
- `audioSamples()` (f32 estéreo intercalado), `audioSampleRate()` (44100),
  `audioChannels()` (2) e `setVolume(v)`
- `setButton(button, pressed, player)` (apenas P1; A/B/Start/Select/D-pad)
- `restart()`, `saveState()` e `loadState(bytes)` (prefixo `EMMUGMB1`)

`GbCore` também compila nativamente e possui `save_ram()` / `load_save_ram()`
para persistência separada da bateria.

## Testes

```sh
cargo test -p emmu-gb
EMMU_GB_TEST_ROM="/caminho/para/jogo.gb" cargo test -p emmu-gb real_rom_smoke_test -- --nocapture
```

## Build WASM

```sh
wasm-pack build --target web --out-dir ../../platforms/web/ui/src/wasm/gb --out-name emmu_gb
```
