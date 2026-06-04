# PEROVSAT GitHub Setup
## Teams
- Flight Software
  - Write access to `perovsat-workspace` and `perovsat-app`
- NSL NDA
  - Devs who have signed NSL's NDA. Access to `eps-driver` and `eyestar-driver`
- Aerospace NDA
  - Devs who have signed Aerospace's NDA. Access to `amu-driver`

## Repositories
### design-and-planning
You are here. Public repository

### perovsat-workspace
This repository technically forms the base directory for all the flight software oriented repos, which it has as subdirectories (which are `.gitignore`d)

It functions as the setup repo. The primary goal is that we can onboard a new dev to the team, and by cloning this and running a script, they can have everything installed and cloned that they could possibly need for the project.

For code, it has
- A `west` manifest for the Zephyr set
- A `setup.sh` script
- A `requirements.txt` for all required Python dependencies
- A `.gitignore` to ensure our subrepos don't get accidentally pushed to the workspace repo

### perovsat-app
This repository is the core of the Perovsat Flight Software, and has the code for:
- All applications threads
- Kconfig
- NDA driver mocks
- `boards/` to store DeviceTree information

### amu-driver
Zephyr driver for Aerospace's provided AMU. This is a private repository, to ensure only team members who have signed the NDA may access it.

### eps-driver
Zephyr driver for Nearspace Launch's EPS. Private for NDA.

### eyestar-driver
Zephyr driver for Nearspace Launch's Eyestar S4. Private for NDA.

### simulation
Simulation setup code to run Basilisk and connect it to our flight software.
