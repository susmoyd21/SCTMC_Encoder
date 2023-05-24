# SCTMC_Encoder
Automatically converts ACTMC specifications of PEPA to PRISM compatible SCTMC files using model embeddings


We considered stochastic process descriptions written in PEPA (https://www.dcs.ed.ac.uk/pepa/), a popular tool for modeling stochastic systems, for our case studies. PEPA allows the generation of the state space for the corresponding process description which we inferred as an ACTMC by inferring the generated state space as well as the process description. Our current prototypical implementation converts PEPA process specifications of ACTMCs to SCTMCs.
  
  It relies on two assumptions whilst defining the ACTMC in the PEPA language. The first assumption is all processes of the form, say \verb|P1=(a,r).(b,s).P2| are re-written as P1=(a,r).P' and P'=(b,s).P2, i.e., the (a,r).P operator is not used in sequence. The other assumption is a process P cannot evolve as P' via two different actions. This can be taken care of by making multiple copies of $P'$ each of which is reached via each action for such scenarios. 
  
  With these assumptions, it allows us to derive the action leading to a state change from the process description itself and the rates and states can be retrieved from the PEPA tool-set generated state-space and generator files. With this, we have successfully retrieved all components of the ACTMC. Now, we use the model embedding to construct the SCTMC and generate the output as PRISM (https://www.prismmodelchecker.org/) human as well as machine-readable files which can be model-checked. PRISM is a state-of-the-art model checker for model checking state-labeled stochastic systems. The human-readable code allows theta as a variable that can be tweaked as per requirements during run-time, whereas for machine readable files, theta needs to be given while generating these files. We specify properties to be checked in ACSL and manually construct the corresponding CSL formula using the logical embeddings.
  
  Steps:
  
  1. Write the process specification in PEPA with the above assumptions and save it as 'Process.pepa'. Generate the statespace and generator matrix from the PEPA toolset, choose ',' as separator and tick on 'Include state number' so that state numbers are present. These two preferences are important. Preferably name the '.statespace' and '.generator' files also as 'Process.statespace' and 'Process.generator'.
  
  2. Now, use our script, input the file-name of your process specification without the extension ('Process' for our running example), make a choice of theta, it is set default at 1000000, and run through the cells to generate both human-readable and machine-readable files compatible with PRISM. You can change the value of theta in the human readable PRISM file later as well.
  
  3. Write your specification to be verified in ACSL and convert to CSL using our logical embeddings. Use PRISM GUI or command-line to build and model-check the properties. 
  
  For the case studies discussed in our paper, we have saved all the PEPA files under the subdirectory 'PEPA_Files' and the generated files for PRISM are
 in the subdirectory 'PRISM_files' for each case-study respectively. The ACSL properties discussed in the paper are embedded and the corresponding CSL
 properties are saved with the '.props' extension in the subdirectory 'PRISM_files' under each case study as well.

For Running the Code:

1. For each case study, move to the corresponding subdirectory ('PEPA_Files') and copy the three files: .pepa, .statespace and .generator from the
   subdirectory to the main directory 'SCTMC_Encoder-main' which contains the scripts. Note that , all the three files will have the same names.

2. Next in the first group of Jupyter notebook, enter the name of the filename in the variable filename without any extension and then give your choice
   theta in the subsequent line in the variable theta which is by default chosen 1000000. Note that, theta must not exceed the limits of PRISM.

3. Next, run all the cells. At the end, four files will be generated, three machine readable files (.sta,.tra,.lab) and one human readable file (.prism)
   with names 'ats<filename>' in the directory 'SCTMC_Encoder-main'.

For Running the Output Model in PRISM:

Please refer to the PRISM website (https://www.prismmodelchecker.org/) for instructions on building your model, model-checking and making changes to settings. 
Here are a few for your reference:

Command line option to build model: 
(a)human readable file <./prism ats<filename>.prism .props>
(b)machine readable file.  <./prism -importtrans ats.tra -importstates ats<filename>.sta -importlabels ats.lab -ctmc .props>

In case you face parsing issues, one possible fix is to increase the java stack size: <./prism ats<filename>.prism .props -javastack 1g>

In cases that the probabilities do NOT converge, you can increase the maximum iterations for it to converge by suffixing the query with:
 '-maxiter 100000' where 100000 can be replaced with any number. This number is 10000 in default for PRISM.

If the probabilities still do not converge, you may need to change the numerical method used or increase the relative error threshold. 
They can be done in the GUI under the 'Options' section or through command-line as well details of which can be found at: 
https://www.prismmodelchecker.org/manual/ConfiguringPRISM/SolutionMethodsAndOptions

All snippets above assumes that the model and the property are in the same directory, i.e., the prism bin and the output result is
 also saved at the same directory. If that is not the case, this can be alternately done as follows:

./prism ~/path-to-directory/Court_room.prism ~/path-to-directory/property.props
	
Please write to:
************************* <susmoy18@iiserb.ac.in>  *********************** 
in case of clarifications, bug reports or issues faced. 
P.S. I am new to Github.
  
  
