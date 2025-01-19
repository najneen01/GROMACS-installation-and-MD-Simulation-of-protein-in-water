
MD Simulation of protein in water

1.	Clean the structure

grep -v HOH 1B5A_prepared.pdb > clean_protein.pdb

![image](https://github.com/user-attachments/assets/a88dd4c4-ee0a-4981-a796-047a1fda602b)

![image](https://github.com/user-attachments/assets/fc34ff66-8108-43f6-a892-622c78c348b4)

 

2.	Creating a topology file

 gmx pdb2gmx -f clean_protein.pdb -o processed_protein.gro -water spce
 
![image](https://github.com/user-attachments/assets/db775418-02b3-4838-890b-3a4ce85a6bc2)

![image](https://github.com/user-attachments/assets/38b0941a-9066-424d-8438-32240e069555)


gmx pdb2gmx -f clean_protein.pdb -o processed_protein.gro -water spce -ignh

![image](https://github.com/user-attachments/assets/ae68fdf7-0878-4eb0-bd37-4bd44f2bccb9)


3.	Solvation
gmx editconf -f processed_protein.gro -o newbox_protein.gro -c -d 1.0 -bt cubic

![image](https://github.com/user-attachments/assets/3fd2c6d3-a8b3-40eb-be33-10c721400498)
 
![image](https://github.com/user-attachments/assets/86de045f-9267-4ccb-9d40-ee1ecdca9fba)


gmx solvate -cp newbox_protein.gro -cs spc216.gro -o solv_protein.gro -p topol.top

![image](https://github.com/user-attachments/assets/b14a3905-d25e-425b-8879-616054ee95f6)

![image](https://github.com/user-attachments/assets/20f2511c-f1d3-4860-9ec8-c3853fda86cd)

 

4.	Ionization
gmx grompp -f ions.mdp -c solv_protein.gro -p topol.top -o ions.tpr

![image](https://github.com/user-attachments/assets/57722748-dee4-4555-a249-9f5e1d481698)

![image](https://github.com/user-attachments/assets/a1106f33-8fda-47eb-996c-2c59383e1e63)


gmx genion -s ions.tpr -o solv_ion_protein.gro -p topol.top -pname NA -nname CL -neutral

![image](https://github.com/user-attachments/assets/8165570f-6b72-4c1f-b013-984f64d5c99c)
 
![image](https://github.com/user-attachments/assets/31800930-881e-4e16-8229-960cc68c6f2e)
 
![image](https://github.com/user-attachments/assets/0fbc7e7d-2b99-4a83-94af-fcb221ecc3e8)


5.	Energy Minimization
 gmx grompp -f minim.mdp -c solv_ion_protein.gro -p topol.top -o em.tpr

![image](https://github.com/user-attachments/assets/cc6d96e3-d4fb-42fe-ba8c-8ce82b6ff1ba)

![image](https://github.com/user-attachments/assets/3d864af7-bded-40aa-9798-ddce4b5c369f)


Reason: The error you're encountering indicates that there is a mismatch between the number of atoms in your coordinate file (solv_ion_protein.gro) and the number of atoms expected by your topology file (topol.top).

Coordinate File: solv_ion_protein.gro has 27826 atoms.
Topology File: topol.top has 27810 atoms.
Difference: You are missing 16 atoms in your topology.

Solution:
Check both file- solv_ion_protein.gro and topol.top
cat solv_ion_protein.gro

![image](https://github.com/user-attachments/assets/e84e7f81-1f9d-43f6-89f2-4261685cf199)
 
 

In topol.top file>cat topol.top

![image](https://github.com/user-attachments/assets/cf5f786b-bc7f-4841-8243-29e05e78c498)

 
This means that the Coordinate File, solv_ion_protein.gro, contains 27,826 atoms and 8,876 SOL molecules, while the Topology File, topol.top, has 27,810 atoms and 8,766 SOL molecules. The difference shows that 16 atoms and 110 SOL molecules are missing in the topology.


Open solv_ion_protein.gro file in notepad>
In top>

![image](https://github.com/user-attachments/assets/6bef5395-f722-491f-ae4d-805e993c272d)


In bottom>

![image](https://github.com/user-attachments/assets/407603a0-68e3-40b2-9754-ccff3a0cc4b9)

The total number of atoms was 27,826, and after removing the 27,829th row, the new total becomes 27,825.
The updated one-

![image](https://github.com/user-attachments/assets/0a0224d0-7e0d-4cd4-9c10-58322477b2f2)
 
 

In topol.top file>

![image](https://github.com/user-attachments/assets/001776e3-e81a-4ee9-8885-42e276aa3b62)

 
Change SOL from 8766 to 8771 because
27825-27810=15
15/3 =5
SOL: 8766+5 = 8771

Again-
gmx grompp -f minim.mdp -c solv_ion_protein.gro -p topol.top -o em.tpr

![image](https://github.com/user-attachments/assets/6e846863-fa97-4387-9f15-a39679152b09)

![image](https://github.com/user-attachments/assets/e1c93490-f152-46b2-ab66-9f0afa2d6433)
 
![image](https://github.com/user-attachments/assets/740a6841-c32e-4bda-88c7-ed5d4882a394)

![image](https://github.com/user-attachments/assets/5f0b5b27-06c9-4347-bcc1-25dc98a8e288)

![image](https://github.com/user-attachments/assets/19a00384-d978-4def-9417-a6fbf906ef6a)

 


gmx mdrun -v -deffnm em

![image](https://github.com/user-attachments/assets/60ceffc1-43d3-492b-8baa-7ff9eb0ecc89)

![image](https://github.com/user-attachments/assets/dddd1de8-6be6-47c0-aa4e-0625dbabadde)
 
 

6.	 Equilibration 
For temperature equilibration
gmx energy -f em.edr -o potential.xvg
 
![image](https://github.com/user-attachments/assets/11517007-2b58-4bf0-9fb2-2b693bc8c9c6)

![image](https://github.com/user-attachments/assets/b1de16cd-7638-4971-a77e-ead67682ef37)

![image](https://github.com/user-attachments/assets/9de79f9b-0dcd-46b2-b809-64b763e9d29a)

 

gmx grompp -f nvt.mdp -c em.gro -r em.gro -p topol.top -o nvt.tpr -maxwarn 2

![image](https://github.com/user-attachments/assets/27ac430d-461c-47f5-96f9-7f4788fa4697)


gmx mdrun -deffnm nvt

![image](https://github.com/user-attachments/assets/58a80455-779e-4919-b1ce-39195acb8366)
 
![image](https://github.com/user-attachments/assets/0704bb47-bc97-4cf2-9914-f5d55cbb21d3)


gmx energy -f nvt.edr -o temperature.xvg

![image](https://github.com/user-attachments/assets/c147f61d-0eb6-4588-94e4-3a2ada5752ea)
 
![image](https://github.com/user-attachments/assets/8c2bc1ee-64be-46bc-8fb0-08e1ccfe6db6)


For Pressure equilibration
gmx grompp -f npt.mdp -c nvt.gro -r nvt.gro -t nvt.cpt -p topol.top -o npt.tpr -maxwarn 2

![image](https://github.com/user-attachments/assets/e707e4a0-d79d-4d97-a945-f0639b6eaf52)


gmx mdrun -deffnm npt -v

![image](https://github.com/user-attachments/assets/6543892c-df7f-4e72-9193-26b7bea77611)
 

gmx energy -f npt.edr -o pressure.xvg

![image](https://github.com/user-attachments/assets/8dbd26dc-e079-428a-9b9a-27f3d47d40b5)

![image](https://github.com/user-attachments/assets/bcd4f3d8-f287-41e9-b9c5-b155d00e270e)

![image](https://github.com/user-attachments/assets/39bad201-8577-4504-b579-bd373b44d6db)

 

 
 


gmx energy -f npt.edr -o density.xvg

![image](https://github.com/user-attachments/assets/7cb5da3a-6684-407d-b2e5-db0cc434be36)


Production

gmx grompp -f md_100ns.mdp -c npt.gro -t npt.cpt -p topol.top -o md_0_1.tpr -maxwarn 2

![image](https://github.com/user-attachments/assets/4f036fb5-f9f4-4eef-a917-cdc34725815e)
 

![image](https://github.com/user-attachments/assets/01c5e96b-c250-41a1-a91a-4b97822f5e09)

 


Bringing Protein in the center of box
gmx trjconv -s md_0_1.tpr -f md_0_1.xtc -o md_0_1_noPBC.xtc -pbc mol -center
select 1 (protein)   , then  0 ("System") 

1.	RMSD:
gmx rms -s md_0_1.tpr -f md_0_1_noPBC.xtc -o rmsd.xvg -tu ns
select : 3 3 (C alpha)
xmgrace rmsd.xvg

![image](https://github.com/user-attachments/assets/0d443abc-4cf1-4cf0-8246-2e0864d8c15b)

 
Average RMSD:
gmx analyze -f rmsd.xvg -dist rmsd_average.xvg

![image](https://github.com/user-attachments/assets/f22615bd-ed0e-45f6-a563-357e20769c22)


Here, average RMSD is 5.030465e-01, 
Standard Deviation is 7.300362e-02

xmgrace rmsd_average.xvg

![image](https://github.com/user-attachments/assets/75eecee9-be04-416e-88fb-dd61cfefa4ac)

2.	RMSF:
gmx rmsf -s md_0_1.tpr -f md_0_1_noPBC.xtc -o rmsf.xvg -res

select : 3 (C alpha)
xmgrace rmsf.xvg

![image](https://github.com/user-attachments/assets/99af7519-29af-4cef-a22c-dab6b7a09855)


Average RMSF:
gmx analyze -f rmsf.xvg -dist rmsf_average.xvg

![image](https://github.com/user-attachments/assets/94a80338-a970-4f8e-b084-5b63815a61de)


xmgrace rmsf_average.xvg
 
![image](https://github.com/user-attachments/assets/5c696043-9eed-4993-9947-834881b88efe)


3.	SASA calculation:
gmx sasa -s md_0_1.tpr -f md_0_1_noPBC.xtc -o sasa.xvg -tu ns
1(protein)
xmgrace sasa.xvg

![image](https://github.com/user-attachments/assets/ad57f0aa-6831-4391-b5ba-4e8db7ca110b)

 

Average SASA:
gmx analyze -f sasa.xvg -dist sasa_average.xvg 

![image](https://github.com/user-attachments/assets/a2cef854-f28f-4cbb-a65e-26055c23eea2)


xmgrace sasa_average.xvg

![image](https://github.com/user-attachments/assets/57aa2d83-0a8a-4d0f-aefa-17ba82a63695)







4.	Radius of Gyration (Rg):
gmx gyrate -s md_0_1.tpr -f md_0_1_noPBC.xtc -o rg.xvg
Select a group: 1
Selected 1: 'Protein'

xmgrace rg.xvg

![image](https://github.com/user-attachments/assets/e70d83fb-eb03-4bba-aaee-80b9ffc5f77a)

 
Average Rg
gmx analyze -f rg.xvg -dist average_rg.xvg
 
![image](https://github.com/user-attachments/assets/43e15052-98a4-4f30-80d9-79011a716e6d)




5.	H-bond
gmx hbond -s md_0_1.tpr -f md_0_1_noPBC.xtc -num hbond.xvg
protein 1,1 
xmgrace hbond.xvg

![image](https://github.com/user-attachments/assets/1728042d-526b-4ccd-bd64-b736605257b2)

Average H-bond
gmx analyze -f hbond.xvg -dist average_hbond.xvg

![image](https://github.com/user-attachments/assets/9942a199-d697-4627-99bf-a8a4654954f5)




