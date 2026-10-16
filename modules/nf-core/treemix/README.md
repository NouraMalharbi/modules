# treemix (`TREEMIX`)

One TreeMix 1.13 run. The pipeline includes it twice: `TREEMIX_SWEEP` (m sweep for OptM) and
`TREEMIX_FINAL` (replicates at the chosen m).

```
treemix -i <treemix.gz> -o <prefix> [-m m] [-seed seed] [-tf tree] <ext.args> > <prefix>.log 2>&1
```

* `m`: `-m` is left out when `m` is null (TreeMix default 0). `m = 0` is passed as `-m 0`.
* `seed`: `-seed` is left out when null; TreeMix then picks one and prints `SEED: <n>` in the log.
* `tree`: optional starting tree (`-tf`), `[]` to skip.
* Everything else goes in `ext.args`: `-root`, `-k`, `-bootstrap`, `-global`, `-se`, `-noss`, ...
* If TreeMix fails, its log is printed to stderr so the error shows in the Nextflow report.

## Outputs

| Emit | File |
|---|---|
| `llik` | `<prefix>.llik` (Starting / Exiting ln(likelihood)) |
| `treeout` | `<prefix>.treeout.gz` (Newick tree + one line per migration edge) |
| `vertices`, `edges` | `<prefix>.vertices.gz`, `<prefix>.edges.gz` |
| `cov`, `modelcov`, `covse` | covariance files (`covse` optional) |
| `log` | `<prefix>.log` (TreeMix stdout/stderr, with `SEED:`) |
| `versions_treemix` | topic `versions`: `[process, 'treemix', version]` |

## Use

```nextflow
include { TREEMIX } from './modules/nf-core/treemix/main'
TREEMIX ( channel.of([ [id:'goat'], file('goat.treemix.gz'), 3, 12345, [] ]) )
```
```groovy
withName: 'TREEMIX' { ext.args = '-root Bezoar -k 500' }
```

Container: `treemix:1.13--hffe338e_4` (checked on IBEX: pulls, runs, `-k` default 1, prints `SEED:`).
The version is read from the first line of the usage text (`TreeMix v. 1.13`); check that the
`versions` topic shows `1.13` after the first run.

## Test

```bash
nf-test test modules/nf-core/treemix --profile singularity
```
