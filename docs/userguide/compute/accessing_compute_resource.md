## Accessing CHESS compute resources
You should not perform operations requiring signficant processing power or memory on the station computer. Instead, you can access the dedicated 3A workstation f2-analysis-2 or use the CHESS Compute Farm. To access these, you will use lnx201.classe.cornell.edu as a "stepping stone" into the rest of the network.

Options for performing analysis on CHESS compute resources:
- Log into workstation f2-analysis-2 (can NoMachine to this computer, or ssh from lnx201.classe.cornell.edu)
- Use the general nodes on the CLASSE compute Farm (available to anyone with a CLASSE ID)
- Use the FAST nodes on the CLASSE Compute Farm (available to whitelisted individuals; ask your beamline scientist for access)


CLASSE Compute Farm intro and how-to here: https://wiki.classe.cornell.edu/Computing/ComputeFarmIntro


Logging into an interactive terminal on the Compute Farm:
- ```ssh -Y [your CLASSE ID]@lnx201.classe.cornell.edu```
- ```qrsh -q interactive.q``` (puts you into an interactive terminal just like a normal terminal)
- Or if you have FAST node access: ```qrsh -q chess_fast_interactive.q```

**Note: both the analysis computer and the Compute Farm are shared resources. Please be aware of your usage and its impact on others.**
- You can check the current load on f2-analysis-2 from the command line by running the command ```top``` (quick guide to this command here: https://www.howtogeek.com/668986/how-to-use-the-linux-top-command-and-understand-its-output/).
- You can check the current load on the FAST interactive nodes by running: ```qstat -f -u “*” -q chess_fast_interactive.q``` on lnx201.classe.cornell.edu. As a general rule please keep your typical use of the interactive nodes below 60 cores. Contact your beamline scientist if you anticipate needing resources beyond this level on a regular or extended basis.
