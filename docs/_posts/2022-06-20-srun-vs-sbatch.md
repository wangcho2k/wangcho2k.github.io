---
title:  "srun vs sbatch"
date:   2022-06-20 14:17:40 +0900
categories: slurm
---

## 공통점
- 둘 다 slurm 클러스터에 job 을 할당하는 기능을 수행합니다.
- 대부분의 명령 옵션이 동일합니다. 일부 예외가 존재하는데 아래 차이점에 기술합니다.

## 차이점
- `srun` 은 interactive 하며 blocking 합니다.
   - Interactive: 콘솔 (`tty` 등)에서 프로그램을 실행하는 것과 비슷하게 작업이 이루어지죠. `stdin/out`  이 콘솔에서 즉각 처리됩니다.
   - Blocking: 프로그램이 실행되는 동안에는 프롬프트를 사용할 수 없으며 프로그램이 종료되어야 다음 쉘 명령어를 입력할 수 있습니다.
  
- `sbatch` 는 그렇지 않습니다. 즉 batch-processing 이며 non-blocking 입니다. 프로그램 (job) 이 클러스터에서 실행되는 동안 콘솔에서 다른 작업을 수행할 수 있습니다.
- `sbatch` 에서 실행하는 스크립트 내부에서 `srun` 을 이용해 job 을 실행할 수 있다.
  - Job array 즉 일련의 연속된 job 을 할당 및 실행하려면 `sbatch` 를 이용해야 합니다. 
- 일부 명령 옵션 사용법에 차이가 존재합니다. 
  - 예를 들어 `--exclusive`  명령의 경우 한 node 가 하나의 job 만을 실행하도록 강제하는데 ([consumable resource](https://slurm.schedmd.com/cons_res.html) 를 활용해 CPU 코어 등 나누어 할당할 수 있는 리소스가 존재함에도 불구하고), `sbatch` 의 경우 job 단위만 고려하는데 반해 `srun` 의 경우 하나의 job이 여러 개의 job step 으로 구성된 경우 이들 job step 끼리도 노드를 독점적으로 쓰게 강제합니다.
![참고: Slurm entities]({{site.url}}/assets/slurm-entities.gif)
*참고: Slurm entities*


## 참고
1. [Stack Overflow - SLURM `srun` vs `sbatch` and their parameters](https://stackoverflow.com/questions/43767866/slurm-srun-vs-sbatch-and-their-parameters)  
2. [slurm document - `sbatch`](https://slurm.schedmd.com/sbatch.html)  
3. [slurm document - `srun`](https://slurm.schedmd.com/srun.html)  
4. [slurm document - Job Array Support](https://slurm.schedmd.com/job_array.html)  
5. [Advanced Bash-Scripting Guide: 36.1. Interactive and non-interactive shells and scripts](https://tldp.org/LDP/abs/html/intandnonint.html)  
6. [Superuser - How to run programs from a linux terminal without blocking the terminal?](https://superuser.com/questions/154486/how-to-run-programs-from-a-linux-terminal-without-blocking-the-terminal)