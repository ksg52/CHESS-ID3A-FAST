Re-configuring the Dexelas from scratch (e.g. after they've been off for a while):
p CWD in SPEC to confirm the current working directory is what you want, probably /nfs/chess/raw/[CYCLE ID]/[BTR ID]
if not, in SPEC cd to the desired directory
now run:
dex_setup DEX1 0
dex_setup DEX2 1

both should now appear as "OFF" when you run dex_show
