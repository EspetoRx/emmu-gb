# oxGBC (cópia vendorizada para emmu-gb)

Código do núcleo [oxGBC](https://github.com/mxmgorin/oxgbc) incorporado ao crate `emmu-gb` para emulação de **Game Boy DMG** no navegador.

## Procedência

Registro completo em [`UPSTREAM.txt`](UPSTREAM.txt):

| Campo | Valor |
| --- | --- |
| Fonte | `https://github.com/mxmgorin/oxgbc` |
| Caminho original | `crates/core` (apenas `src/` e `Cargo.toml`) |
| Commit | `b95ff230bcfbf9ec21186cbac4b954d377a7507d` |
| Licença | **GPL-3.0-only** |
| Autor upstream | `troidem <mxmgorin@gmail.com>` |

## Modificações locais

- O nome do pacote foi alterado de `core` para `oxgbc-core-gb` para evitar conflito com o crate `core` da biblioteca padrão e com a cópia usada por `emmu-gbc` no mesmo workspace.
- Dev-dependencies, benchmarks e testes que exigem ROMs volumosos foram removidos.
- O código em `src/` permanece idêntico ao upstream.

## Árvore de arquivos

```text
oxgbc-core/
├── Cargo.toml          # pacote oxgbc-core-gb 0.1.0, edition 2021
├── LICENSE-GPL-3.0     # texto integral da GPLv3
├── UPSTREAM.txt        # proveniência e alterações
└── src/
    ├── apu.rs          # APU e geração de áudio
    ├── aux.rs          # utilidades
    ├── bus.rs          # barramento e memória
    ├── cart.rs         # cartuchos/MBC/RAM/RTC
    ├── cpu.rs          # CPU LR35902
    ├── emu.rs          # orquestração, runtime e save state
    ├── lib.rs          # API pública do core
    └── ppu.rs          # PPU e framebuffer
```

## Uso no Emmu

O crate `emmu-gb` instancia este core com `GbModel::Dmg` e paleta verde clássica. `GbCore` envolve `Emu`/`EmuRuntime`, aplica volume, converte áudio para `f32` estéreo e serializa o estado com o magic `EMMUGMB1`. No desktop, DMG é executado pelo Mesen, não por esta cópia.

## Licença

GPL-3.0-only. O texto integral está em [`LICENSE-GPL-3.0`](LICENSE-GPL-3.0). Ao redistribuir o Emmu web, preserve o texto da licença e disponibilize o código-fonte correspondente.
