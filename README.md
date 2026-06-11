# ⚙️ Gearworks

An interactive **involute gear-train playground** in a single dependency-free HTML file.

Click to drop gears onto a blueprint canvas — they snap into mesh and spin with correct
kinematics. Build compound reductions by stacking pinions on shafts, read live ratio and
torque numbers, and watch the whole drivetrain stall if you wire up a loop that can't turn.

## Run it

No build, no dependencies:

- double-click `index.html`, or
- `python3 -m http.server 9412 --directory .` and open http://localhost:9412

Append `?demo` to the URL to reset to the demo scene (a two-stage 10.9 : 1 compound
reduction driven by an 11-tooth pinion).

## Controls

| Action | Effect |
|---|---|
| click background | add a gear (tooth count from the chips panel) |
| drag gear | move it — magnetic snap-to-mesh |
| scroll on gear | change its tooth count |
| `S` / ⊕ stack | stack a pinion on the selected gear's shaft (compound stage) |
| `⌫` / double-click | delete gear |
| scroll / drag background | zoom / pan |

Scenes autosave to `localStorage`.

## The physics

- **True involute tooth profiles** — 20° pressure angle, addendum *m*, dedendum 1.25 *m*,
  shared module so any pair of gears can mesh.
- **Phase-locked meshing** — each frame the solver orients every driven gear so a tooth
  always faces a gap at the contact point (the constraint `f₁(φ) + f₂(φ+π) ≡ ½ (mod 1)`
  on tooth phases). Teeth interlock; they never clip.
- **Correct speed/torque ratios** — `ω₂ = −ω₁·N₁/N₂` across a mesh, equal ω across a
  stacked (keyed) shaft, propagated by BFS from the motor.
- **Jam detection** — odd mesh loops can't satisfy direction constraints (and most loops
  can't satisfy tooth phase). The solver detects the conflict and stalls the whole train
  rather than letting gears spin through each other.

A debug API is exposed at `window.GW` (`GW.check()` reports mesh count, max phase error,
and per-gear ratios; `GW.tick()` forces a frame when rAF is paused in background tabs).

## Deploy (Vercel)

The included `vercel.json` pins a zero-build static deployment:

```sh
vercel        # preview
vercel --prod # production
```

…or import the GitHub repo in the Vercel dashboard — no build settings needed.

## License

[MIT](LICENSE)
