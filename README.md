# PPINAC
This README describes the usage of the command line interface of PPINAC (Protein-Protein Interaction Networks for COVID19).

The executable PPINAC is compiled for Linux x86_64 platform.

The program PPINAC finds a global alignment of pairwise input
networks. Given two networks with N1, N2 nodes each,
it returns a matching between the input networks, each match corresponding to
best-matching nodes from the multiple networks. Adding to that, we provide the virus-host protein-protein interaction networks (i.e. SARS-CoV-2 versus SARS-CoV-1), in order to discover more conserved edges or common viral infections by applying the alignment between the SARS-CoV-2 versus SARS-CoV-1.

To understand how to use the algorithm, let's start with an example of
pairwise alignment of two input networks. The multiple case is similar.


(1) Suppose the species are named 'A' and 'B'

(2) Create the graph files and results from the BLAST runs (between the
    nodes) in a single directory. You'll need 5 files:

  (2.1) Network files:
     You'll need A.pin and B.pin , tab-separated files
     where each line contains an interaction. For example, the first 5 lines of
     A.pin are:

     ====== BEGIN ========
     INTERACTOR_A INTERACTOR_B
     a0 a1
     a0 a2
     a0 a3
     a0 a4
     ====== END ========

      Columns are separated by tabs. The first line is a header line of the
     form as shown above. All other lines describe an interaction, one per
     line.

     There may be a third column which contains edge weights (0 < wt <= 1) 
     and in that case the header line should've a third column titled Weight_VAL.

  (2.2) Results of BLAST runs.
     You'll need to perform an the all-against-all run
     of BLAST between all the nodes of the two networks. You should store the results in 3 files:

        A-B.sim,  A-A.sim, B-B.sim

     The files contain, as their names indicate, the results of BLAST runs
     between species A & B, A & A, and B & B, respectively. IMPORTANT: for
     files containing Blast scores between two species, the filename should
     have the species names in lexicographic order, i.e., A-B.sim is
     expected, not B-A.sim.

     The first 5 lines of the A-B.sim file are:

     ====== BEGIN ========
     a0      b0      1
     a1      b1      1
     a2      b2      1
     a3      b3      1
     a4      b4      1
     ====== END ========

     Each line is of the form:
     <id1>  <id2>   <Bit-Score>



  (2.3)  Gene ontology (GO) File
 
     IMPORTANT: Download gene ontology file from the Website "http://www.geneontology.org/" and uncompress it 
     under the "PPINAC" folder. The name should be "gene_ontology.1_2.obo" under the data folder.


  (2.4) GO annotation file which contains GO annotations for proteins in the input networks. The format of this GO annotation file should be compliant with the GO consortium.
     
    A) File: goa_uniprot_gcrp.gaf → This set contains all GO annotations for canonical accessions
      from the UniProt reference proteomes for all species, which provide one protein per gene.
      
    B) Files: goa_<species>.gaf for each species → This set contains all GO annotations for canonical accessions 
      from the UniProt reference proteome for the species, which provides one protein per gene. 
      
      IMPORTANT: Download gene annotation files for each species from the Uniprot Website "http://www.ebi.ac.uk/goa/downloads" and uncompress them under the "PPINAC" folder.
 

(4) Call the code. Here are samples:
    
   
If you want to compile the code, simply run the following command:

g++ PPINAC.cpp Network.cpp Alignment.cpp -o PPINAC

If you want to run the executable file, you just need to run the following command:

./PPINAC net1.tab net2.tab [-a aa] [-b bb] [-c cc] [-i it]

where the arguments are:

------------------------------

net1.tab: The name of the first network file
net2.tab: The name of the second network file

------------------------------

-a aa:  an option for defining value of parameter a (see the paper for more information)
aa is a float number between 0 and 1.It is a value for parameter a that controls the weight of similarity and interaction scores. If aa=1 then the program considers only similarity scores and if aa=0 then it considers only interaction scores. The default value is aa=0.0001.

------------------------------

-b bb: an option for defining value of parameter b (see the paper for more information)
bb is a float number between 0 and 1.It is a value for parameter b that controls the weight of biological and topological similarities. If bb=1 then the program considers only biological similarity and if bb=0 then it considers only topological similarity. If you don't want to use any biological data, you should set bb=0 and you don't need to provide any file for biological similarities. Default value is bb=0.

------------------------------

-c cc: an option for defining value of parameter c (see the paper for more information)
cc is a float number between 0 and 1. The more the cc, the more the contribution of neighbors of two points in calculating the similarity between them. If cc=0 then the program considers only similarity of two nodes and if cc=1 then it considers only similarities of their neighbors. If you don't want to use any biological data, set cc=1. Default value is cc=1.

------------------------------

-i it: an option for defining value of parameter it (see the paper for more information)
it is a non-negative integer number .It defines the number of iterations for computing similarities. The default value is it=2;

------------------------------

An example of the alignment between Homo Sapiens and Mouse networks (c.f. the networks are in the "data/ppi_Human_mouse" under the compressed folder PPINAC.tar.xz) :
./PPINAC data/ppi_Human_mouse/10090_uniprot4.pin data/ppi_Human_mouse/9606_uniprot4.pin -o 3 -i 2

An example of the alignment between SARS-COV2 and SARS-COV1 networks (c.f. the networks are in the "data/ppi_Human_coronavirus" under the compressed folder PPINAC.tar.xz) :
./PPINAC data/ppi_Human_coronavirus/sars2.tab data/ppi_Human_coronavirus/sars1.tab -o 2 -i 2

An example of the alignment between Human-SARS-COV1 and Human-SARS-COV2 networks (c.f. the networks are in the "data/ppi_Human_coronavirus" under the compressed folder PPINAC.tar.xz) :
./PPINAC data/ppi_Human_coronavirus/human_sars1.tab data/ppi_Human_coronavirus/human_sars2.tab -o 2 -i 2

Note:
 - The uniprot gene ontology annotation file (which contain the annotations of the Homo Sapiens, Mouse and the coronavirus) is under the compressed folder "uniprot_goa_Human_Mouse_SARS.tar.xz". Therefore, we urge the users to decompress the files and place it under the folder "data/goa".
 - The blast bit score between the compared species can be downloaded from this link "http://doi.org/10.5281/zenodo.5708117", and putted under each compared networks folder.
 - We supply the alignment results of some global alignment approaches (i.e., PPINAC MAGNA++, BEAMS and NETAL) on all the compared protein-protein interaction networks.

To generate different random instances of a network, please see the code for the required parameters.

(5)  The output

      The program outputs one file "ppinac_alignment.txt"(contains global alignment of two networks).

