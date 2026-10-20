# hydro_digital_twin

An interactive Sau + Susqueda release-scenario tool for NEREIDA's monthly
seasonal forecast (Ter basin, Catalonia). The user changes how much water
leaves the Ter system — ATL transfer, Girona supply, Baix Ter irrigation,
maintenance flow — and the browser recomputes the reservoir balance for all
51 SEAS5 ensemble members live, showing storage ranges and the risk of
crossing the PES drought thresholds.

Live at the repo's GitHub Pages URL (root `index.html`, no build step,
no server — the water-balance recompute runs client-side in ~1–3 ms).

## What it shows

Pick a forecast year/month, adjust the release sliders, press **Executa**:
the page reruns the water balance for every ensemble member against that
month's SEAS5-driven inflow forecast and redraws the storage-range chart
and drought-threshold risk.

## Repository layout

```
index.html        The tool (UI + water-balance engine, data fetched separately)
data/index.json    List of available forecast months
data/<year>.json    One file per year: inflow ensemble members, starting
                     storage, release plan, observed storage/inflow for
                     every forecast month that year (loaded on demand)
```

## Updating

Rebuilt automatically each month by `main_operational.sh` in the main
NEREIDA pipeline repo, from `tool/digital_twin_demo/` there. To push a
rebuild here manually:

```bash
cd /path/to/claude/main/tool/digital_twin_demo
python3 build/build_data_all.py
python3 build_html.py
cp index.html /path/to/tools/hydro_digital_twin/
cp -r data /path/to/tools/hydro_digital_twin/
cd /path/to/tools/hydro_digital_twin
git add index.html data
git commit -m "Forecast update — automated" && git push origin main
```

## Known limits (also shown on the page)

- Same simplified balance as the operational pipeline: GR4J inflow to Sau
  only, no local Susqueda inflow, no evaporation.
- GR4J–SEAS5 underestimates Sau inflows (−24% to −51% vs. observed
  climatology) — storage numbers should be read as relative, not absolute.
- Water temperature / residence time (GLM) is not included here — it would
  need a precomputed scenario grid run on the cluster.
