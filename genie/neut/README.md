# NEUT cross sections for ExtGen

Total CC and NC cross sections of NEUT, used by GENIE (through the
`NE06_02a_00_000` tune of `ubsim/ExtGen`) to decide which neutrinos interact,
on which nucleus and in which current. NEUT itself (stock NEUT 6.2, driven by
ExtGen's `neut_extgen`, `ubsim/ExtGen/generators/neut`) then generates the
final state of each selected interaction.

Files, one set per NEUT version and physics card. `<version>` is NEUT's
release (`6_2_3` = NEUT 6.2.3); `<cardhash>` is the first 8 hex digits of the
md5 of `ubsim/ExtGen/generators/neut/extgen_neut_card.sh`, which writes every
ExtGen NEUT card. The table records the NEUT build (commit / `neut_extgen`
md5) and the card md5 in its header, and `extgen_neut_grid_init.sh` refuses
to serve with a different build or card.

* `neut_<version>_<cardhash>_xsec.txt`: table of
  `nu_pdg CC|NC target_pdg E[GeV] sigma[1e-38 cm^2 per nucleus]   # rel_err sigma_NEUT f_PB`
  for nu_e, nu_e-bar, nu_mu, nu_mu-bar and every nucleus in the **whole**
  geometry (the 13 `volCryostat` nuclei plus N14, K39 and the free neutron of
  the geometry's `Vacuum`), 280 energies 0.01-8 GeV.
  sigma = sigma_NEUT x (1 - f_PB): NEUT's total cross section of the current
  (exact, no Monte Carlo) times the fraction of NEUT events that are not
  Pauli blocked (NEUT blocks the nucleon of single-pion events after choosing
  the mode and keeps the event without final state; f_PB is measured with
  NEUT events). rel_err: statistics of f_PB, interpolation between the
  energies, NEUT's single precision; read by the in-job check (V4).
  Targets NEUT cannot model: Mg24 is generated as Na23 and K39 as Ar40 (their
  cross section per nucleon times the requested A); the free neutron is an
  explicit zero (it never interacts, density 1e-25 g/cm^3).
* `neut_<version>_<cardhash>_xsec.xml`: the GENIE spline file made from it with
  `extgen_make_splines.py --tune NE06_02a_00_000 --cap 8 --extend-to 200`.
  This is what jobs use.

Both come from `extgen_neut_tables.sh` (no grid scan campaign: NEUT computes
its cross sections directly); a grid job takes them as
`extgen_neut_grid_init.sh --tables genie/neut/neut_<version>_<cardhash>`.

The NEUT build that grid jobs run, `neut_<version>_<cardhash>.tar.gz` (NEUT
installation with its data, `bin/neut_extgen`, `BUILD_INFO` and the ExtGen NEUT
scripts; `make_neut_tarball.sh`; ROOT comes from CVMFS as recorded in
`BUILD_INFO`), is too large for uboonedata. It is stored in
`/pnfs/uboone/persistent/users/uboonepro/ExtGen`; copy it to a resilient area
and pass it with jobsub -f (`extgen_neut_grid_init.sh --neut-tarball
<resilient path> --neut-md5 <md5sum>`, as the ubsim/ExtGen xml templates do).

Physics (card): NEUT 6.2 defaults, except QE = Nieves (local Fermi gas with
RPA) with M_A = 1.05 GeV, the value of the Nieves 1p1h tables NEUT ships.
2p2h: Nieves hadron tensors (Ar40 uses the A = 40 tensor), 3p3h on.

| set | NEUT | card | tarball (in the persistent area above) | use |
|---|---|---|---|---|
| `neut_6_2_3_e2c4c81f` | 6.2.3 (`2b3d4312`, tag 6.2.3), `neut_extgen` md5 98534fe5 | `extgen_neut_card.sh` md5 e2c4c81f | `neut_6_2_3_e2c4c81f.tar.gz`, md5 4b964fdab0139d6fec5d628049588be6 | production (2026-11-24; f_PB from 4000 events per energy) |
