# LeniaDynamics.jl

Julia balíček pro simulaci **Lenia** (kontinuální varianta „Game of Life“), se zaměřením na:

- **výkon** (FFT konvoluce přes FFTW vs. naivní konvoluce),
- **reprodukovatelné experimenty** (oddělená prostředí v `scripts/` a `docs/`),
- **interaktivní vizualizaci** přes **GLMakie** (GUI ve `scripts/run_gui.jl`),
- volitelně **CUDA** akceleraci (package extension, pokud je nainstalované `CUDA.jl`).

---

## Rychlý start (API balíčku)

```julia
using LeniaDynamics

spec = KernelSpec(
    radius=13,
    rings=Float32[0.45, 0.75],
    ring_widths=Float32[0.15, 0.12],
    ring_weights=Float32[1.0, 0.7],
)

p = LeniaParams(kernel=spec, growth=gaussian_growth, μ=0.15f0, σ=0.015f0, dt=0.10f0)

st = initialize_state(256; init=:spot, seed=42, backend=:fft)
run!(st, p, 200; integrator=RK2())
```

---

## Spuštění skriptů (examples / GUI / bench)

Repo obsahuje samostatné prostředí `scripts/` pro spustitelné ukázky.

### PowerShell (Windows)

Z kořene repozitáře:

```powershell
# 1) připrav scripts prostředí (poprvé / po pullu)
julia --project=scripts -e "import Pkg; Pkg.instantiate(); Pkg.precompile()"

# 2) example
julia --project=scripts scripts/run_example.jl

# 3) GUI (GLMakie)
julia --project=scripts scripts/run_gui.jl

# 4) benchmark
julia --project=scripts scripts/bench.jl
```

### Poznámka k `scripts/Manifest.toml` (portabilita)
Pokud `scripts/Manifest.toml` obsahuje u závislosti `LeniaDynamics` absolutní Windows cestu, změň ji na relativní:

```toml
path = ".."
```

Případně smaž `scripts/Manifest.toml` a nechej ho znovu vygenerovat:

```powershell
julia --project=scripts -e "import Pkg; Pkg.develop(path=".."); Pkg.instantiate()"
```

---

## Testy

```powershell
julia --project=. -e "import Pkg; Pkg.instantiate(); Pkg.test()"
```

---

## Dokumentace (Documenter.jl)

```powershell
julia --project=docs -e "import Pkg; Pkg.instantiate()"
julia --project=docs docs/make.jl
```

---

## CUDA (volitelné)

CUDA se aktivuje automaticky, pokud je dostupné `CUDA.jl` a běží extension.

```julia
using LeniaDynamics
has_cuda() && @info "CUDA available"
```

---

## License

Viz `LICENSE` (MIT).
