## Accessing CHESS compute resources
You should not perform operations requiring significant processing power or memory on the station computer. Instead, you can use the CLASSE Compute Farm. All CHESS users have access to general-purpose Compute Farm nodes; your staff scientist can also give you access to a dedicated set of FAST nodes. 

To access the Compute Farm, you will use lnx201.classe.cornell.edu as a "stepping stone" into the rest of the network. Like the station computer, lnx201 has limited CPU/RAM resources, so only use it for lightweight operations such as filesystem navigation, basic text file editing/viewing, and ssh'ing to other places.

CLASSE Compute Farm intro and how-to [here](https://wiki.classe.cornell.edu/Computing/ComputeFarmIntro).

Logging into an interactive queue on the Compute Farm:
- ```ssh -Y [your CLASSE ID]@lnx201.classe.cornell.edu```
    - Note: when ssh'ing from a Windows machine, you must install and run an X server (e.g. [Xming](https://sourceforge.net/projects/xming/)) to enable graphics forwarding.
    - Alternatively: NoMachine into lnx201 (see NoMachine instructions [here](https://wiki.classe.cornell.edu/Computing/NoMachine)) 
- ```qrsh -q interactive.q``` (puts you into an interactive terminal, operates just like a normal Linux terminal)
- Or if you have FAST node access: ```qrsh -q chess_fast_interactive.q```

The simple qrsh command above will get you up and running in an interactive session. In many cases - notably including running HEXRDGUI - it's good practice to specify how many cores you want to claim for your interactive session. Do this by adding the argument ```-pe sge_pe [number of cores``` to your qrsh command: for example, ```qrsh -q chess_fast_interactive.q -pe sge_pe 12``` to claim 12 cores.

The FAST Compute Farm nodes are lnx301, lnx302, lnx303, lnx304. **Never access these via SSH - always qrsh if you want an interactive session.** SSH'ing to these machines can cause conflicts with how CPU and memory allocation is managed by the underlying grid engine.

To open a session on a specific node, you can specify that in your qrsh code: for example, 

```qrsh -q chess_fast_interactive.q@lnx301 -pe sge_pe 12``` 

will route you to lnx301.

**Note: the Compute Farm is a shared resource. Please be aware of your usage and its impact on others.**
- When logged into any Linux machine, including an interactive Compute Farm node, you can check the current load from the command line by running the command ```top``` (quick guide to this command [here](https://www.howtogeek.com/668986/how-to-use-the-linux-top-command-and-understand-its-output/)).
- You can check the current load on a specific queue (e.g. chess_fast_interactive.q, chess_interactive.q, interactive.q) by running: ```qstat -f -q [name of the target queue]``` from the command line of a CHESS Linux machine.
    - Example: check the load on all FAST interactive nodes by running: ```qstat -f -q chess_fast_interactive.q```
    - Example: check the load on a specific 

- Each FAST node has a total of 56 cores. As a general rule please keep your usage at or below 48 cores. Contact your beamline scientist if you anticipate needing resources beyond this level on a regular or extended basis.
