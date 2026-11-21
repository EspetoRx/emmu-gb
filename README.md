# emmu-gb

Core de **Game Boy (DMG)** do Emmu para o navegador. Usa o mesmo [oxGBC](https://github.com/mxmgorin/oxgbc) do `emmu-gbc`, mas em modo `GbModel::Dmg` e com a paleta verde clássica. No desktop, `.gb` roda no Mesen via `emmu-mesen-sys`.

## Dependências (todas dentro desta pasta)

```text
vendor/oxgbc-core/
├── Cargo.toml            # pacote renomeado para oxgbc-core-gb
├── LICENSE-GPL-3.0       # GPL-3.0-only
├── UPSTREAM.txt          # fonte, commit e alterações locais
└── src/                  # APU, barramento, cartucho, CPU, emulação, PPU, utilidades
```

- Upstream: `https://github.com/mxmgorin/oxgbc`
- Commit vendorizado: `b95ff230bcfbf9ec21186cbac4b954d377a7507d`
- Licença: **GPL-3.0-only**
- Alterações locais: rename para `oxgbc-core-gb` para não colidir com o crate da biblioteca padrão `core` nem com a cópia do `emmu-gbc`; remoção de dev-dependencies/benchmarks/testes com ROMs grandes. `src/` permanece idêntico.

## API

### Nativa (`GbCore`)

- `new(rom)` / `game_title()`
- `run_frame(render_graphics)` → RGBA8888 **160 × 144**
- `take_audio_samples()` — `f32` estéreo intercalado, **44.100 Hz**, 2 canais
- `audio_sample_rate()`, `audio_channels()`, `set_volume(v)`
- `set_button(button, pressed, player)` — apenas P1; `A`, `B`, `Start`, `Select`, `Up`, `Down`, `Left`, `Right`
- `restart()` — preserva SRAM de bateria
- `save_state()` / `load_state(bytes)` — magic **`EMMUGMB1`** + JSON do `EmuSaveState` (SRAM/MBC e RTC)
- `save_ram()` / `load_save_ram()` — persistência separada

### WASM (`GbEmulator`)

Mesma API em camelCase via `wasm-bindgen`: `new`, `gameTitle`, `runFrame`, `audioSamples`, `audioSampleRate`, `audioChannels`, `setButton`, `setVolume`, `restart`, `saveState` e `loadState`.

X/Y/L/R são ignorados; nomes desconhecidos retornam erro. Save states com magic diferente são rejeitados.

## Aparência e ritmo

- Paleta DMG clássica: `#E0F8D0`, `#88C070`, `#346856`, `#081820` (RGBA).
- `EmuRuntime::run_frame` controla o ritmo no navegador.

## Árvore de arquivos

```text
emmu-gb/
├── Cargo.toml        # cdylib + rlib; serde_json, wasm-bindgen, vendor path
├── LICENSE           # texto integral da GPLv3
├── README.md
├── src/
│   └── lib.rs        # GbCore, GbEmulator WASM, configuração e testes
└── vendor/
    └── oxgbc-core/
        ├── Cargo.toml
        ├── LICENSE-GPL-3.0
        ├── UPSTREAM.txt
        └── src/
```

## Build e teste

A partir da raiz:

```sh
cargo test -p emmu-gb
```

Com ROM real:

```sh
EMMU_GB_TEST_ROM="/caminho/para/jogo.gb" cargo test -p emmu-gb real_rom_smoke_test -- --nocapture
```

Pacote WASM:

```sh
cd crates/emmu-gb
wasm-pack build --target web \
  --out-dir ../../platforms/web/ui/src/wasm/gb \
  --out-name emmu_gb
```

Testes incluídos: `runs_frame_and_returns_rgba`, `save_state_round_trip`, `restarts_and_keeps_audio_api` e `real_rom_smoke_test` (condicional).

## Desktop

O frontend envia `.gb` com console `GB`; o desktop configura o Mesen com `Gameboy` e save states `.mss`. O formato `EMMUGMB1` existe apenas no caminho WASM.

## Licenças

| Componente | Licença | Crédito |
| --- | --- | --- |
| `emmu-gb` | GPL-3.0-only | `LICENSE` |
| oxGBC vendorizado | GPL-3.0-only | `troidem <mxmgorin@gmail.com>` — `vendor/oxgbc-core/LICENSE-GPL-3.0` |
| `serde_json`, `wasm-bindgen` | MIT OR Apache-2.0 | dependências Rust usuais |

Ao redistribuir o bundle web, cumpra a GPLv3: disponibilize o código-fonte do núcleo e preserve os avisos de licença.
