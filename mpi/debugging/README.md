# Debugging

## General notes

- Code needs to be compiled with debugging option `-g`
- Compiler optimizations might complicate debugging (dead code
  elimination, loop transformations, *etc.*), recommended to
  compile without optimizations with `-O0`
    - Sometimes bugs show up only with optimizations


## Launching Arm DDT

### LUMI

Set up VNC for smoother GUI performance:
```bash
module load lumi-vnc
start-vnc
```
Follow the instructions from the command output to set up
SSH port forwarding and opening VNC on a browser.

Load module and start debugger in an interactive session:
```bash
module load ARMForge
export SLURM_OVERLAP=1
salloc -A project_465000536 --nodes=1 --ntasks-per-node=2 --time=00:30:00 --partition=debug
ddt srun ./buggy
```

The debugger GUI will open in VNC in the browser window.

Note: you can also skip the VNC step and use X11 forwarding for GUI (might be slow).

### Puhti and Mahti

Launch a desktop session on a browser for smoother GUI performance
(see [the documentation](https://docs.csc.fi/computing/webinterface/desktop/) for detailed instructions):
* Login to https://www.puhti.csc.fi or https://www.mahti.csc.fi
* Launch Desktop with 1 core
* Open a terminal window in the desktop session and proceed there

Load module and start debugger in an interactive session:
```bash
module load ddt
export SLURM_OVERLAP=1
salloc -A project_2007995 --nodes=1 --ntasks-per-node=2 --time=00:15:00 --partition=test
ddt srun ./buggy
```

Note: you can also skip the desktop session step and use X11 forwarding for GUI (might be slow).


## Examples

These example codes can be built with `make all`.

### Message exchange revisited

Debug the [exchange.cpp](exchange.cpp) code similar to
[the earlier exercise](../message-exchange/).

The following will be demoed with DDT:
* Launching DDT
* Examining per-process status
* Setting build configuration
* Fixing the code within DDT

### Collective operations revisited

Debug the [collective.cpp](collective.cpp) code similar to
[the earlier exercise](../collectives/).

The following will be demoed with DDT:
* Setting breakpoints
* Stepping execution
* Using distributed array view

#### Bonus 1

Memory debugging with sanitizer. On LUMI:
```bash
CC -g -fsanitize=address collective.cpp -o collective.exe
srun -A project_465000536 --nodes=1 --ntasks-per-node=4 --time=00:05:00 --partition=debug --label ./collective.exe
```

Note the `--label` option that prepends task number to lines of stdout/err.

#### Bonus 2

Memory debugging with valgrind4hpc. On LUMI:

```bash
module load valgrind4hpc

CC -g collective.cpp -o collective.exe
valgrind4hpc --num-ranks=4 --launcher-args="-A project_465000536 --nodes=1 --ntasks-per-node=4 --time=00:05:00 --partition=debug" ./collective.exe
```

