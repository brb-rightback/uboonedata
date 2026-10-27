# NuWro cross sections for ExtGen

Total CC and NC cross sections of NuWro, used by GENIE (through the
`NW25_11a_00_000` tune of `ubsim/ExtGen`) to decide which neutrinos interact,
on which nucleus and in which current. NuWro itself (`nuwro_extgen`, NuWro
branch `v08_00_00_br`) then generates the final state of each selected
interaction.

Files, one set per NuWro version and physics card. `<version>` is NuWro's
release (`25_11_1` = NuWro 25.11.1); `<cardhash>` is the first 8 hex digits
of the md5 of `ubsim/ExtGen/generators/nuwro/extgen_nuwro_card.sh`, which
writes every ExtGen NuWro card. Both tables record the NuWro build (commit /
`nuwro_extgen` md5) and card md5 in their header, and
`extgen_nuwro_grid_init.sh` refuses to serve with a different build or card.

* `nuwro_<version>_<cardhash>_xsec.txt`: table of
  `nu_pdg CC|NC target_pdg E[GeV] sigma[1e-38 cm^2 per nucleus]`, summed over
  the NuWro channels of that current (CC: QEL, RES, DIS, COH, MEC, HYP; NC:
  QEL, RES, DIS, COH, MEC), for each flavour (nu_e, nu_e-bar, nu_mu,
  nu_mu-bar) and every nucleus in the **whole** geometry (the 13 `volCryostat`
  nuclei plus N14, K39 and a free neutron, as for GiBUU).
* `nuwro_<version>_<cardhash>_xsec.xml`: the GENIE spline file made from it with
  `extgen_make_splines.py --tune NW25_11a_00_000 --cap 8 --extend-to 200`.
  This is what jobs use (`XSecTable: "genie/nuwro/<file>.xml"`).
* `nuwro_<version>_<cardhash>_max.txt`: largest NuWro weight seen per point
  and channel (`nu_pdg CC|NC target_pdg E dyn max_w[1e-38 cm^2 per nucleon]
  n_trials`), from which each grid job sets the Max of every channel (3 x the
  largest of the neighbouring points; a channel without weight there stays
  closed).

All three come from `extgen_nuwro_tables.py` (then `extgen_make_splines.py`)
over the scan campaign; a grid job takes them as
`extgen_nuwro_grid_init.sh --tables genie/nuwro/nuwro_<version>_<cardhash>`.

Physics: the NuWro MicroBooNE BNB card; QEL spectral function for C12, O16,
Ar40 and Fe56, local Fermi gas for the other nuclei and the free nucleons
(NuWro has no spectral function for them). NaN weights (rare, hybrid RES
model for antineutrinos) are discarded as stock NuWro does, so the cross
sections are those NuWro itself reports.

Current set: none yet (scan campaign of 268 jobs running, 2026-10-27).
