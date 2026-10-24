# GiBUU cross sections for ExtGen

Total CC and NC cross sections of GiBUU, used by GENIE (through the
`GB25_05a_00_000` tune of `ubsim/ExtGen`) to decide which neutrinos interact,
on which nucleus and in which current. GiBUU itself then generates the final
state of each selected interaction.

Files, one pair per GiBUU version and physics card:

* `gibuu_<version>_<cardhash>_xsec.txt`: table of
  `nu_pdg CC|NC target_pdg E[GeV] sigma[1e-38 cm^2 per nucleus]`, summed over
  every process enabled in the production card, for each flavour
  (nu_e, nu_e-bar, nu_mu, nu_mu-bar) and every nucleus in the **whole**
  geometry. GENIE requires a spline for every nucleus of every material in the
  geometry, even outside `TopVolume`: for `microboonev12.gdml` that is the 13
  `volCryostat` nuclei plus N14, K39 and a free neutron (ROOT reads the GDML
  material `Vacuum` as Z = 0, A = 1).
* `gibuu_<version>_<cardhash>_xsec.xml`: the GENIE spline file made from it with
  `extgen_make_splines.py --tune GB25_05a_00_000 --cap 8 --extend-to 200`.
  This is what jobs use (`XSecTable: "genie/gibuu/<file>.xml"`).

The cross sections must come from the same card as the production, with
only the energy and target changed. Each production job checks them against
GiBUU's own trial statistics.
