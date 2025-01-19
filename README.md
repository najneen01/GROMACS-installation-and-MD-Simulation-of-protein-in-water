
MD Simulation of protein in water

1.	Clean the structure

grep -v HOH 1B5A_prepared.pdb > clean_protein.pdb
 
 

2.	Creating a topology file

 gmx pdb2gmx -f clean_protein.pdb -o processed_protein.gro -water spce
 
 

gmx pdb2gmx -f clean_protein.pdb -o processed_protein.gro -water spce -ignh
 

3.	Solvation
gmx editconf -f processed_protein.gro -o newbox_protein.gro -c -d 1.0 -bt cubic
 
 

gmx solvate -cp newbox_protein.gro -cs spc216.gro -o solv_protein.gro -p topol.top
 
 

4.	Ionization
gmx grompp -f ions.mdp -c solv_protein.gro -p topol.top -o ions.tpr
 
 

gmx genion -s ions.tpr -o solv_ion_protein.gro -p topol.top -pname NA -nname CL -neutral

 
 
 

5.	Energy Minimization
 gmx grompp -f minim.mdp -c solv_ion_protein.gro -p topol.top -o em.tpr
 
 

Reason: The error you're encountering indicates that there is a mismatch between the number of atoms in your coordinate file (solv_ion_protein.gro) and the number of atoms expected by your topology file (topol.top).

Coordinate File: solv_ion_protein.gro has 27826 atoms.
Topology File: topol.top has 27810 atoms.
Difference: You are missing 16 atoms in your topology.

Solution:
Check both file- solv_ion_protein.gro and topol.top
cat solv_ion_protein.gro
 
 

In topol.top file>cat topol.top

 
That means, 
Coordinate File: solv_ion_protein.gro has 27826 atoms, 8876 SOL
Topology File: topol.top has 27810 atoms, 8766 SOL
Difference: You are missing 16 atoms,  110 SOL in your topology.


Open solv_ion_protein.gro file in notepad>
In top>
 

In bottom>
 
In this case, Total atom is 27826, removing the 27829th row. So, the new total atom is 27826-1 = 27825
The updated one-
 
 

In topol.top file>
 
Change SOL from 8766 to 8771 because
27825-27810=15
15/3 =5
SOL: 8766+5 = 8771

Again-
gmx grompp -f minim.mdp -c solv_ion_protein.gro -p topol.top -o em.tpr
 
 
 
 
 


gmx mdrun -v -deffnm em
 
 

6.	 Equilibration 
For temperature equilibration
gmx energy -f em.edr -o potential.xvg
 

 
 

gmx grompp -f nvt.mdp -c em.gro -r em.gro -p topol.top -o nvt.tpr -maxwarn 2
 

gmx mdrun -deffnm nvt
 
 

gmx energy -f nvt.edr -o temperature.xvg
 
 

For Pressure equilibration
gmx grompp -f npt.mdp -c nvt.gro -r nvt.gro -t nvt.cpt -p topol.top -o npt.tpr -maxwarn 2
 

gmx mdrun -deffnm npt -v
 

gmx energy -f npt.edr -o pressure.xvg


 

 
 


gmx energy -f npt.edr -o density.xvg
 

Production

gmx grompp -f md_100ns.mdp -c npt.gro -t npt.cpt -p topol.top -o md_0_1.tpr -maxwarn 2
 

 


Bringing Protein in the center of box
gmx trjconv -s md_0_1.tpr -f md_0_1.xtc -o md_0_1_noPBC.xtc -pbc mol -center
select 1 (protein)   , then  0 ("System") 

1.	RMSD:
gmx rms -s md_0_1.tpr -f md_0_1_noPBC.xtc -o rmsd.xvg -tu ns
select : 3 3 (C alpha)
xmgrace rmsd.xvg
 
Average RMSD:
gmx analyze -f rmsd.xvg -dist rmsd_average.xvg
 
Here, average RMSD is 5.030465e-01, 
 Standard Deviation is 7.300362e-02
xmgrace rmsd_average.xvg
 
2.	RMSF:
gmx rmsf -s md_0_1.tpr -f md_0_1_noPBC.xtc -o rmsf.xvg -res

select : 3 (C alpha)
xmgrace rmsf.xvg


 


Average RMSF:
 gmx analyze -f rmsf.xvg -dist rmsf_average.xvg
 
xmgrace rmsf_average.xvg
 

3.	SASA calculation:
gmx sasa -s md_0_1.tpr -f md_0_1_noPBC.xtc -o sasa.xvg -tu ns
1(protein)
xmgrace sasa.xvg

 

Average SASA:
gmx analyze -f sasa.xvg -dist sasa_average.xvg 
 

xmgrace sasa_average.xvg

 






4.	Radius of Gyration:
gmx gyrate -s md_0_1.tpr -f md_0_1_noPBC.xtc -o rg.xvg
Select a group: 1
Selected 1: 'Protein'

xmgrace rg.xvg

 
Average Rg
gmx analyze -f rg.xvg -dist average_rg.xvg
 




5.	H-bond
gmx hbond -s md_0_1.tpr -f md_0_1_noPBC.xtc -num hbond.xvg
protein 1,1 
xmgrace hbond.xvg
 
Average H-bond
gmx analyze -f hbond.xvg -dist average_hbond.xvg
 

Secondary Structure Analysis (SSE)
https://gromacs.bioexcel.eu/t/xpm2ps-output-error-with-gromacs-2021/3223













RESULT_ANALYSIS_ONLY_PROTEIN_SIMULATION_GROMACS














PROTEIN-LIGAND SIMULATION

Download CHARMM36 forcefield from-
http://mackerell.umaryland.edu/charmm_ff.shtml#gromacs

1. Keep all files in one folder
 
or
 
2. unzip the charmm force field
sudo apt-get install unrar
 

 unrar x charmm36-jul2022.ff.rar
 
3. Preparation of Receptor (Protein) topology file
gmx pdb2gmx -f protein.pdb -o protein.gro
 
1
 
4. Preparation of Ligand Topology file
perl sort_mol2_bonds.pl ligand.mol2 ligand_fix.mol2

 
Go to https://cgenff.com/

 

Since it is paid service
Use https://charmm-gui.org/?
Or
Swissparam: https://old.swissparam.ch/
 



