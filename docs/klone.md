# Klone

Klone docs: https://hyak.uw.edu/docs

# Slurm

Sbatch docs: https://slurm.schedmd.com/sbatch.html

## Connecting to the cluster

SSH to Klone:
```
ssh <netid>@klone.hyak.uw.edu
```

## Filesystem

Find a summary of storage usage/quotas/paths with
```
$ hyakstorage
                          Usage report for /mmfs1/home/stevengs                           
╭──────────────────────┬────────────────────────────────┬────────────────────────────────╮
│                      │ Disk Usage                     │ Files Usage                    │
├──────────────────────┼────────────────────────────────┼────────────────────────────────┤
│ Total:               │ 7GB / 10GB                     │ 36787 / 256000 files           │
│                      │ 70%                            │ 14%                            │
╰──────────────────────┴────────────────────────────────┴────────────────────────────────╯
                        Usage report for /mmfs1/gscratch/escience                         
╭──────────────────────┬────────────────────────────────┬────────────────────────────────╮
│                      │ Disk Usage                     │ Files Usage                    │
├──────────────────────┼────────────────────────────────┼────────────────────────────────┤
│ Total:               │ 3616GB / 4096GB                │ 1394192 / 4000000 files        │
│                      │ 88%                            │ 35%                            │
│ My usage:            │ 0GB                            │ 1 files                        │
╰──────────────────────┴────────────────────────────────┴────────────────────────────────╯
                          Usage report for /mmfs1/gscratch/dirac                          
╭──────────────────────┬────────────────────────────────┬────────────────────────────────╮
│                      │ Disk Usage                     │ Files Usage                    │
├──────────────────────┼────────────────────────────────┼────────────────────────────────┤
│ Total:               │ 232381GB / 266240GB            │ 33788648 / 260000000 files     │
│                      │ 87%                            │ 13%                            │
│ My usage:            │ 231761GB                       │ 32768577 files                 │
╰──────────────────────┴────────────────────────────────┴────────────────────────────────╯
                          Usage report for /mmfs1/gscratch/astro                          
╭──────────────────────┬────────────────────────────────┬────────────────────────────────╮
│                      │ Disk Usage                     │ Files Usage                    │
├──────────────────────┼────────────────────────────────┼────────────────────────────────┤
│ Total:               │ 916GB / 1024GB                 │ 644188 / 1000000 files         │
│                      │ 89%                            │ 64%                            │
│ My usage:            │ 726GB                          │ 253827 files                   │
╰──────────────────────┴────────────────────────────────┴────────────────────────────────╯
```

Filesystem structure:
- `$HOME`: home directory; small and slow. Don't put anything here other than config files
- `/gscratch/astro`: astro group storage; larger, restricted, but stable storage, used by whole astro department
- `/gscratch/dirac`: dirac group storage; large, used mostly by Steven for DEEP processing, storage scales up and down based on need
- `/gscratch/scrubbed`: scrubbed storage; 10TB / 10M files per user, 30 day retention period. Can make any subdirectories you want: `/gscratch/scrubbed/$USER`, `/gscratch/scrubbed/dirac/$USER`, etc.

## Computing resources

Find a summary of compute usage/quotas/partitions with
```
$ hyakalloc
       Account resources available to user: stevengs        
╭──────────┬────────────────┬──────┬────────┬──────┬───────╮
│  Account │      Partition │ CPUs │ Memory │ GPUs │       │
├──────────┼────────────────┼──────┼────────┼──────┼───────┤
│    astro │ compute-bigmem │   40 │   364G │    0 │ TOTAL │
│          │                │   11 │    85G │    0 │ USED  │
│          │                │   29 │   279G │    0 │ FREE  │
├──────────┼────────────────┼──────┼────────┼──────┼───────┤
│ escience │        gpu-a40 │   52 │   994G │    8 │ TOTAL │
│          │                │   16 │   516G │    1 │ USED  │
│          │                │   36 │   478G │    7 │ FREE  │
├──────────┼────────────────┼──────┼────────┼──────┼───────┤
│ escience │      gpu-rtx6k │   10 │    81G │    2 │ TOTAL │
│          │                │   10 │    20G │    0 │ USED  │
│          │                │    0 │    61G │    2 │ FREE  │
╰──────────┴────────────────┴──────┴────────┴──────┴───────╯
 Checkpoint Resources  
╭───────┬──────┬──────╮
│       │ CPUs │ GPUs │
├───────┼──────┼──────┤
│ Idle: │ 3421 │   66 │
╰───────┴──────┴──────╯
```

Partitions:
- `astro/compute-bigmem`: 40 CPU cores at 9GB / core. Shared among entire astro department
- `escience/gpu-a40`: 52 CPU cores at 19GB / core and 8 NVIDIA A40 GPUs. Shared all of escience
- `escience/gpu-rtx6k`: 10 CPU cores at 8GB / core and 2 NVIDIA RTX 6K
- `(astro-ckpt or escience-ckpt)/ckpt`: all currently unused resources in the cluster. Fair share scheduling based on cluster buy-in (we are a small part of the cluster). Jobs submitted to this queue are requeued when:
1) someone with dedicated access to compute (e.g. `astro/compute-bigmem`) requests resources and the scheduler decides to preempt your job so their's can start
2) after ~4:00:00 walltime for CPU jobs and ~8:00:00 walltime for GPU jobs

## Basic set up on Klone

Set up symlinks to user directories on the various partitions:
```
[klone] $ mkdir -p /gscratch/astro/$USER && ln -s /gscratch/astro/$USER $HOME/astro
[klone] $ mkdir -p /gscratch/dirac/$USER && ln -s /gscratch/dirac/$USER $HOME/dirac
[klone] $ mkdir -p /gscratch/scrubbed/$USER && ln -s /gscratch/scrubbed/$USER $HOME/scrubbed
```

## Log-in Node vs Compute Node

All "work" should be done on a compute node instead of a log-in node (where you land after `ssh kone.hyak.uw.edu`). Work == anything that is not related to accessing the compute node, submitting jobs to the queue, and moving data in and out of the cluster (limited to scp / rsync). Any process called "python" gets flagged and you will get a warning/throttling for running these processes on the log in node.

You can get an interactive compute node for your work (with 1 CPU) with:
```
srun -p compute-bigmem -A astro --cpus-per-task=1 --mem=8GB --pty /bin/bash
```

```
$ srun # runs a task
$ sbatch # submits a script that runs tasks
```

You can set up a job that runs `sshd` so you can ssh directly to the compute node through the login node from your laptop. Useful for VS Code. https://uw-dirac.slack.com/archives/C8WRK67LY/p1711661336131669

## Checkpoint jobs

To access nodes on the checkpoint queue, use these arguments with `srun`/`sbatch`
```
-A astro-ckpt -p ckpt
```
or
```
-A escience-ckpt -p ckpt
```

## GPU resources 

You can find the list of GPU partitions on Klone with
```
$ sinfo | grep gpu
gpu-2080ti          up   infinite      2  fail* g[3006-3007]
gpu-2080ti          up   infinite     12    mix g[3000-3005,3014-3017,3027],z3001
gpu-a100            up   infinite      8    mix g[3080-3085],z[3010-3011]
gpu-a40             up   infinite      1   comp g3071
gpu-a40             up   infinite     28    mix g[3040-3047,3050-3057,3060-3061,3063,3065-3067,3070,3072-3076]
gpu-a40             up   infinite      3  alloc g[3062,3064,3077]
gpu-l40             up   infinite      8    mix g[3090-3096,3098]
gpu-l40             up   infinite      1  alloc g3097
gpu-l40             up   infinite      1   idle g3099
gpu-p100            up   infinite      2  maint z[3005-3006]
gpu-rtx6k           up   infinite      1   comp g3037
gpu-rtx6k           up   infinite     18    mix g[3010-3013,3020-3026,3030-3036]
gpu-titan           up   infinite      1  alloc z3002
```

To run a job selecting a GPU from the checkpoint queue use the following arguments for `srun`/`sbatch`:
```
-p ckpt -A astro-ckpt --gpus-per-node=<gpu-type>:1
```
where `<gpu-type>` might be one of `2080ti, a100, a40, l40, rtx6k, titan` (pulled from the `gpu-*` partition name)

