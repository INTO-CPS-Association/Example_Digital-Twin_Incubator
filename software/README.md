This folder contains the code that makes the digital twin possible.

# Directory Structure

```
├───cli -- Contains code to communicate with the running DT components.
├───digital_twin -- Code that forms the digital twin.
├───docs -- Documentation.
├───incubator -- Code that implements the physical twin
├───integration_tests -- Code to run tests that involve settup and running scenarios with the DT components.
├───mock_plant -- Code setting up the local virtual incubator plant.
├───old_software -- Old code that is still being organized
└───startup -- Code that allows starting and stopping the DT components.
```

# Cloning this repo

When cloning this repo, don't forget to initialize the ![submodules](https://git-scm.com/book/en/v2/Git-Tools-Submodules).

# Running the Unit Tests

## First-time setup
1. Open terminal in this folder.
2. Optional: create a virtual environment: `python -m venv venv`
3. Optional: activate the virtual environment (there are multiple possible activate scripts. Pick the one for you command line interface.): 
   1. Windows Powershell:`.\venv\Scripts\Activate.ps1` 
   2. Linux/Mac: `source venv/bin/activate`
4. Recommended: install pip wheel: `pip install wheel`
5. Install dependencies:
   1. `pip install -r ./requirements.txt`.


## After first time setup

Parameters for rabbitmq, inlfuxdb, PT, and DT can be adjusted in the ```startup.conf```

See script [./run_tests.ps1](./run_tests.ps1) for more details on which environment variables to set.
Configure your IDE accordingly.

# Development Notes

Note that the incubator is included as a git submodule. 

Workflow with the incubator

After cloning the repo, the following needs to be executed for the submodule code to be cloned locally:
```bash
software $ git submodule update --init --recursive
```

The working branch needs to be checkout out, otherwise git cannot track the changes made to it. 
```bash
software/incubator$ git checkout main
```

When making local changes, add and commit as usual.
```bash
software/incubator$ git add . && git commit -m"commit the local changes made to the incubator"
```

In order for the commit to be pushed on the remote, the main repo needs to point to this new commit.
First, merge in any updates that have happened in the submodule remote. This will assume the master branch, for our case we need to append ```branch = main``` in the ```.gitmodules``` file first. Thereafter:
```bash
software/incubator$ cd ..
software/$ git submodule update --remote --merge
```

Thereafter, push with the recurse-submodules option, with on-demand telling git to try and push the submodule commits, if they're not already pushed.
```bash
software/$ git push --recurse-submodules=on-demand
```

Alternatively, working directly in the submodule directory
Pushing the changes made to the submodule

```bash
software/incubator$ git add . 
software/incubator$ git commit -m"committing changes to incubator code" 
software/incubator$ git pull
software/incubator$ git push
```

Thereafter, the contents in the parent folder should be added, committed, and pushed, such that the module head points to the new commit of the incubator submodule, like:

```bash
software/incubator$ cd ..
software$ git add . && git commit -m"committing changes of the submodule in " && git push
```

More info on git submodules can be found here: [git submodule doc](https://git-scm.com/book/en/v2/Git-Tools-Submodules), [workflows with submodules](https://www.atlassian.com/git/tutorials/git-submodule)

# Starting the DT framework

Follow the instructions in [README.md](./startup/README.md)

# Running Integration Tests

Make sure you can successfully start the DT framework and run the unit tests before attempting to run the integration tests.

The script [run_integration_tests.ps1](./run_integration_tests.ps1) contains the instructions.

# Running real experiments with Pi
1. in order to use the gpiozero package, we need 
   ```bash
   export PYTHONPATH=/usr/lib/python3/dist-packages/:incubator
   ```
2. run the low_level_driver (under the software folder)
   ```bash
   python3 -m incubator.physical_twin.low_level_driver_server
   ```
3. Then you can run your controller or other service, before that, please configure the ip address correctly, currently it WLAN of the raspberry pi is 192.168.4.10 
