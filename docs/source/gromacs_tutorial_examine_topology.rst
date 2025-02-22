 .. _gromacs-tutorial-examine-topology-label:

 .. role:: bolditalic
  :class: bolditalic

.. role:: boldcode
  :class: boldcode

.. role:: italiccode
  :class: italiccode

================
Examine Topology
================

Let's look at what is in the output topology (topol.top). Again, using a plain text editor, inspect its contents. After several comment lines (preceded by ;), you will find the following:

.. code-block::

   #include "oplsaa.ff/forcefield.itp"

This line calls the parameters within the OPLS-AA force field. It is at the beginning of the file, indicating that all subsequent parameters are derived from this force field. The next important line is [ moleculetype ], below which you will find

.. code-block::

   ; Name       nrexcl
   Protein_A    3

The name "Protein_A" defines the molecule name, based on the fact that the protein was labeled as chain A in the PDB file. There are 3 exclusions for bonded neighbors. More information on exclusions can be found in the GROMACS manual; a discussion of this information is beyond the scope of this tutorial.

The next section defines the ``[ atoms ]`` in the protein. The information is presented as columns:

.. code-block::

   [ atoms ]
   ;   nr       type  resnr residue  atom   cgnr     charge       mass  typeB    chargeB      massB
   ; residue   1 LYS rtp LYSH q +2.0
        1   opls_287      1   LYS       N      1       -0.3    14.0067   ; qtot -0.3
        2   opls_290      1   LYS      H1      1       0.33      1.008   ; qtot 0.03
        3   opls_290      1   LYS      H2      1       0.33      1.008   ; qtot 0.36
        4   opls_290      1   LYS      H3      1       0.33      1.008   ; qtot 0.69
        5  opls_293B      1   LYS      CA      1       0.25     12.011   ; qtot 0.94
        6   opls_140      1   LYS      HA      1       0.06      1.008   ; qtot 1

The interpretation of this information is as follows:

* nr: Atom number

* type: Atom type

* resnr: Amino acid residue number

* residue: The amino acid residue name

  * Note that this residue was "LYS" in the PDB file; the use of .rtp entry "LYSH" indicates that the residue is protonated (the predominant state at neutral pH).

* atom: Atom name

* cgnr: Charge group number

  * Charge groups define units of integer charge; they aid in speeding up calculations

* charge: Self-explanatory

  * The "qtot" descriptor is a running total of the charge on the molecule

* mass: Also self-explanatory

* typeB, chargeB, massB: Used for free energy perturbation (not discussed here)

Subsequent sections include ``[ bonds ]``, ``[ pairs ]``, ``[ angles ]``, and ``[ dihedrals ]``. Some of these sections are self-explanatory (bonds, angles, and dihedrals). The parameters and function types associated with these sections are elaborated on in Chapter 5 of the GROMACS manual. Special 1-4 interactions are included under "pairs" (section 5.3.4 of the GROMACS manual).

The remainder of the file involves defining a few other useful/necessary topologies, starting with position restraints. The "posre.itp" file was generated by pdb2gmx; it defines a force constant used to keep atoms in place during equilibration (more on this later).

.. code-block::

   ; Include Position restraint file
   #ifdef POSRES
   #include "posre.itp"
   #endif

This ends the "Protein_A" moleculetype definition. The remainder of the topology file is dedicated to defining other molecules and providing system-level descriptions. The next moleculetype (by default) is the solvent, in this case SPC/E water. Other typical choices for water include SPC, TIP3P, and TIP4P. We chose this by passing "-water spce" to pdb2gmx.

.. code-block::

   ; Include water topology
   #include "oplsaa.ff/spce.itp"

   #ifdef POSRES_WATER
   ; Position restraint for each water oxygen
   [ position_restraints ]
   ;  i funct       fcx        fcy        fcz
      1    1       1000       1000       1000
   #endif

As you can see, water can also be position-restrained, using a force constant (kpr) of 1000 kJ mol-1 nm-2.

Ion parameters are included next:

.. code-block::

   ; Include generic topology for ions
   #include "oplsaa.ff/ions.itp"

Finally come system-level definitions. The ``[ system ]`` directive gives the name of the system that will be written to output files during the simulation. The ``[ molecules ]`` directive lists all of the molecules in the system.

.. code-block::

   [ system ]
   ; Name
   LYSOZYME

   [ molecules ]
   ; Compound        #mols
   Protein_A           1

A few key notes about the ``[ molecules ]`` directive:

1. The order of the listed molecules must exactly match the order of the molecules in the coordinate (in this case, .gro) file.
2. The names listed must match the [ moleculetype ] name for each species, not residue names or anything else.

If you fail to satisfy these concrete requirements at any time, you will get fatal errors from grompp (discussed later) about mismatched names, molecules not being found, or a number of others.

Now that we have examined the contents of a topology file, we can continue building our system.

Next: :ref:`gromacs-tutorial-solvation-label`