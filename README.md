# Drone-Frame-FEA-Study-Design-Iteration-for-Lightweighting
Project Question: How much weight can I remove from a quadcopter frame while keeping it strong enough to fly safely?
## Design
Two iterations of a quadcopter frame, modeled in Autodesk Fusion
360:
**v2 (lightened)** - everything above, plus:
- 60 × 8 mm rounded slots cut through
each
arm
(rounded ends = less stress concentration than
sharp corners)
- 4 × Ø20 mm holes in the top plate at (=32, ‡32), between the arms in low-stress regions, clear of the standoffs
The + layout was chosen over X because it patterns cleanly (one bar + circular pattern) and is structurally equivalent for this study.
## Simulation setup (identical for v1 and v2)
- Study: Static Stress
- Material: Aluminum 6061 (yield = 276 MPa)
- Constraint: Fixed - bottom face of the bottom plate (represents the central battery/ electronics mass holding the frame)
- Loads: 5 N upward on each motor mount top face (4 × 5 N = 20 N total = 2 kg thrust, a hard climb)
- Contacts: bonded (default)
## Results

**Reading the table:** removing material raised stress and displacement, exactly as theory predicts - but the safety factor went from 109.7 to 62.3, still ~15x above the target of
• 4. The frame was heavily over-designed in v1, and v2 proves the lightening method works while staying far inside the safe envelope.
In v2, peak stress shifted toward the slot ends (stress concentration at the cutouts) - expected, and a good reminder that cutout shape matters.
## Validation - cantilever beam hand-check (MATLAB)
Each arm was modeled as a cantilever beam fixed
at the plate edge:
- Force F = 5 N, unsupported length L = 0.04 m
(mount at 100 mm - plate edge at 60 mm)
- Cross-section b = 0.02 m, h = 0.004 m
1 → S =
b. h2/6
- o = F•L/S = **3.75 MPa**
1 l Hand calc | FEA v2 | Difference |
1-1-1------1
I Max arm stress
3.75 MPa
1 4.415 MPa
**15.1% - PASS**
Script: F = 5;        % thrust per motor, N
L = 0.04;     % unsupported arm length (mount at 100mm, plate edge at 60mm), m
b = 0.02;     % arm width, m
h = 0.004;    % arm thickness, m

S = b*h^2/6;            % rectangular section modulus, m^3
sigma_MPa = F*L/S/1e6;  % max bending stress at arm root, MPa

fea_v1 = 2.507;
fea_v2 = 4.415;

fprintf('Hand calc (cantilever beam): %.2f MPa\n', sigma_MPa);
fprintf('FEA v1: %.3f MPa\n', fea_v1);
fprintf('FEA v2: %.3f MPa\n', fea_v2);
fprintf('Hand calc vs FEA v2 diff: %.1f percent\n', abs(sigma_MPa-fea_v2)/fea_v2*100);

if abs(sigma_MPa-fea_v2)/fea_v2 < 0.5
    fprintf('PASS: hand calc agrees with FEA within 50 percent.\n');
else
    fprintf('CHECK: large difference, review beam assumptions.\n');
end
(run by me in MATLAB)
Note: deflection was deliberately NOT hand-checked - the short, stubby arm bonded to a stiff plate makes simple beam deflection formulas inaccurate (they overpredict by ~100x). Knowing where a hand model stops applying is part of the skill.
## Files
- MATLAB cantilever-beam validation
- CAD: 'Drone_V1' and 'Drone_V2_Light' (Fusion 360)
- Screenshots: v1/v2 CAD isos, v1/v2 stress and displacement plots with legends
## Key takeaways
1. **Design margin is a resource.** v1's safety factor of 109 wasn't "safe" - it was wasteful. The sim quantified exactly how much margin could be traded for weight.
2. **Cut where stress is low.** Slots went mid-arm and holes went between arms - low-stress regions identified from the v1 plot - not at the highly-stressed arm roots.
3. **Validate, don't trust.** The FEA numbers mean nothing until a hand calc says they're sane. 15% agreement earns the right to believe the sim.
4. **Know when to stop.** v2's FoS of 62 means a v3 could go further - and I can say exactly why I stopped (time) and what v3 would target (bigger slots, bottom-plate cutouts).