## vpgTLE example

vpgTLE is broken up into three parts. `Stage 0` is required to be run once, and each vpgTLE simulation is then broken up into `Stage 1` and `Stage 2`. For each stage, you can find an example in their respective sub-directory `stage0` and `stageX`.

To understand the background, physics and mathematics of this example, refer to `Accelerated Prompt Gamma estimation for clinical Proton Therapy simulations` by B.F.B. Huisman.

### Stage 0

Creating the prompt-gamma (PG) database (PGdb) means computing $\frac{\Gamma_Z}{\rho_Z}$ for the elements that appear in your simulations. In `stage0/` you can find all that is required to produce a PGdb. The runall-elements.sh script would create PGdb for most common elements found conversion tables. The `runall-elements.sh` can be used together with `gate_split_and_run.py` to produce a PGdb with $10^9$ statistics per element.
To merge all the PGdb outputs, use the `gate_power_merge_pgdb_all.sh` on the CC (conda env needed).

Note that mac/main.mac can be used to alter the number of primaries per run, and the maximum proton energy which has been set at 200MeV. If your simulation in Stage 1 uses higher proton energies, the PGdb must be recomputed.

A pgdb_20231027.root with $10^9$ primaries per material has been supplied (with Geant4 v11.1.1) in the example for Stage 1.

#### Note

At the time of writing Geant4 generates a very large number of low energy PGs (2-25keV) that appear not physical. They're killed in their first step. The pgdb.root therefore doesn't ship with the 5 heaviest elements, that are severly affected by these affected by these particles. Therefore, the actor (`PromptGammaStatisticActor`) used to create the elemental databases has a hard cut on PGs <40keV (the first bin with the PGs energy windows+binning settings). We feel this is acceptable until the issue with Geant4 is solved, as these particles will never exit the patient anyway.

### Stages X \in (1 , 2)

The head and neck phantom from the protontherapy example has been used.

#### Stage 1

A prepared pgdb_20231027.root is found in the `data/` directory. By default, the same plan is used as in the paper, but selected spot and the original plan have been supplied, also in `data/`. To be consistent with Stage 0, be careful to use the same physics list and production cuts.

The number of primaries required is a user choice. We recommend at least $10^6$ primaries for a sufficiently converged output for Stage 1. 

Another important consideration is the region where the actors score. It is set here to a region containing the layer of the treatment plan.

To run the simulation with the RT-plan use: `Gate mac/main_stage1_vpg_patient.mac'`.

#### Stage 2

For Stage 2, the number of PG is set (like for Stage 1) by the nr of protons: the nr of emitted PG is automatically computed from the integral of the PGdb which gives the total PG yield per proton. To run the simulation with the RT-plan use: `Gate mac/main_stage2_vpg_patient.mac'`.

A prepared PG source yield (with $10^8$ protons) is available in the `stageX/data` directory: `source_vpg_patient_proton2dary[-tof].mhd` for the PG energy [and PG emission-time]. 

